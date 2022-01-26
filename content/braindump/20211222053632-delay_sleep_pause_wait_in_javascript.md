+++
title = "Delay, Sleep, Pause & Wait in Javascript"
author = [7696122]
lastmod = 2022-01-19T10:18:38+09:00
draft = false
+++

[Delay, Sleep, Pause, & Wait in JavaScript - SitePoint](https://www.sitepoint.com/delay-sleep-pause-wait/)  

```js
console.log("Hello");
setTimeout(() => {
  console.log("World!");
}, 2000);
```

```js
console.log("Hello");
setTimeout(() => {
  console.log("World!");
}, 2000);
console.log("Goodbye!");
```

```js
function sleep(milliseconds) {
  const date = Date.now();
  let currentDate = null;
  do {
    currentDate = Date.now();
  } while (currentDate - date < milliseconds);
}

console.log("Hello");
sleep(2000);
console.log("World!");
```

```js
// async function sleep(ms) {
//   return setTimeout(() => {}, ms);
// }

function sleep(ms) {
  return new Promise((resolve) => setTimeout(resolve, ms));
}

console.log("Hello");
sleep(2000)
  .then(() => console.log("world!"))
  .then(() => sleep(2000))
  .then(() => console.log("Goodbye!"));
```

```js
import { setTimeout } from "timers/promises";
(async () => {
  const result = await setTimeout(2000, "resolved");
  // Executed after 2 seconds
  console.log(result); // "resolved"
})();
```
