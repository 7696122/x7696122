+++
title = "javascript global factory"
author = [7696122]
lastmod = 2022-01-19T10:37:44+09:00
tags = ["javascript"]
draft = false
+++

```js
// rename function () { 'use strict' ...
function Vue() {
  "use strict";
  /* */
}

// rename function (global, factory) ...
function UMD(global, factory) {
  /* deleted for clarity */
}

UMD(this, Vue);
```


## Ref {#ref}

[javascript - Why is function(global, factory) used in so many JS libraries? -...](https://stackoverflow.com/questions/60365052/why-is-functionglobal-factory-used-in-so-many-js-libraries)
