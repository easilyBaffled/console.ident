# Console.ident/logIdent

```js
v => (console.log(v), v);
```

`logIndent` is a function that takes in a value, logs the value, then returns the value. With the purpose of providing a logging function that does not interrupt your existing code

This module provides a polyfill option - that will add `logIndent` to the global `console` object as `console.ident` - in addition to the `logIndent` function to use as a standalone function.
I believe that `logIndent` **should** be a part of the standard, and as such I will be referring to it as `console.ident` going forward.

You can view the slides and notes for my lighting talk proposing `console.ident` at [Ident Presentation](https://ident-presentation-qalclei0w.now.sh/?mode=presenter#0)

---

# Why

Javascript has become an Expression dominated language. Which means just about everything we do results in a value. Which in turn mean that we are able to write more concise code where one thing leads cleanly into the next.

**For Example:**

```js
const userID = getUserId(
  JSON.parse(localStorage.getItem("user"))
);
```

```js
const pickAndFormatData = ({ date, amount }) => ({
  date: moment(date).format("DD/MM/YYY "),
  amount: Number(amount) ? formatCurrency(amount) : amount
});
```

```js
const result = arr
  .map(parseNumbers)
  .filter(removeOdds)
  .reduce(average);
```

But there is no `console` function that fit in modern style Javascript. Instead `console.log` and it's like return `undefined`. Which means you will have to awkwardly break up the code to debug it. `console.ident` Solves the `undefined` issue. It takes in a value, logs the value, then returns the value.

**For Comparison:**

**With `console.log`:**

```js
const userStr = localStorage.getItem("user");
console.log(userStr);

const userID = getUserId(JSON.parse(userStr));
```

**With `console.ident`:**

```js
const user = JSON.parse(
  console.ident(localStorage.getItem("user"))
);
```

---

**With `console.log`:**

```js
const pickAndFormatData = ({ date, amount }) => {
  console.log(amount, Number(amount));
  const result = {
    date: moment(date).format("DD/MM/YYY "),
    amount: Number(amount) ? formatCurrency(amount) : amount
  };
  console.log(result);
  return result;
};
```

**With `console.ident`:**

```js
const pickAndFormatData = ({ date, amount }) =>
  console.ident({
    date: moment(console.ident(date)).format("DD/MM/YYY"),
    amount: console.ident(Number(amount))
      ? formatCurrency(amount)
      : amount
  });
```

---

**With `console.log`:**

```js
const filtered = arr.map(parseNumbers).filter(removeOdds);
console.log(filtered);

const result = filtered.reduce(average);
```

**With `console.ident`:**

```js
const result = console
  .ident(arr.map(parseNumbers).filter(removeOdds))
  .reduce(average);
```

---

# API

## `logIdent( value, [options] )`

Takes in a value, logs the value, then returns the value.

Developer consoles cannot accurately display the file name and line numbers for `logIdent` calls since they pull that information based on where where the `console.log` is called. To make up for that `logIdent` takes an optional second parameter that serves as an identifying label for the call.

#### Arguments

`value (*)`: The value that will be logged and then returned.  
`[options] (*|IdentOptions)`:  
    `*`: Any value that will be logged preceding the `value` as a label.  
    `IdentOptions`:  
        `label (string)`: a value that will be logged preceding the `value` as a label.  
        `lineNumber (boolean)`: a flag to indicate if the function should add the line number where the function is being called from to the label  

#### Example

```js
import { logIdent } from "console.ident";

fetch(url)
  .then(res => res.json)
  .then(console.ident)
  .then(dispatchRecivedData);
```

```js
import { logIdent } from "console.ident";

const filterOptionsByInputText = ({
  options,
  filterText
}) =>
  options.filter(value =>
    logIdent(value.contains(filterText), value)
  );
```

```js
const SuggestionList = ({ options, filterText }) => (
  <ul>
    {options
      .filter(value =>
        logIdent(value.contains(filterText), {
          label: `${filterText} ${value}`,
          lineNumber: true
        })
      )
      .map(value => (
        <li key={value}>{value}</li>
      ))}
  </ul>
);
```

## `polyfill()`

When called, this function will add `logIdent` to the global `console` object as `console.ident`

#### Example

```js
import { polyfill } from "console.ident";
polyfill();

const value = console.ident("anything");
```