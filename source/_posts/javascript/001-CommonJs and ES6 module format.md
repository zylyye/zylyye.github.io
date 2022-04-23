---
title: CommonJs and ES6 module format
categories: [Javascript]
tags: [CommonJs, ES6]
date: 2022-03-28 23:33:38
---

---

As a web developer, you may confused with these module import/export usages:

- `exports.xx = xx`
- `module.exports = xx`
- `export xx`
- `export default xx`
- `require('..')` or` import xx from '..'`?

<!-- more -->

Here are a brief instruction of these two module format usages:

### CommonJs module format

**Named exports**

```js
# name export demo.js
exports.name1 = 'a'
exports.name2 = 'b'
# import
const obj = require('./demo.js')
obj.name1 === 'a'
obj.name2 === 'b'
```

**Module.exports**

```js
# demo.js
module.exports = {
  name1: 'a',
  name2: 'b'
}
# import
const obj = require('./demo.js')
obj.name1 === 'a'
obj.name2 === 'b'
```

Actually, the `module` is like this:

```json
{
  id: '',
  exports: {}, // what we have exported
}
```

the `module.exports` === `exports` in `exports.name1` and `exports.name2`, when we use `require('demo.js')` to import a module, it is same as:

```js
const obj = module.exports
```



### ES6 module

ES6 uses `export` instead of `exports`:

```js
# demo.js
const name1 = 'a'
export name1
export const name2 = 'b'
# import
import { name1, name2 } from './demo.js'
```

Of course it supports **default export**:

```js
# demo.js
export default {
  name1: 'a',
  name2: 'b',
}

# import
import obj from './demo.js'
```

You could even mix the default export and named export:

```js
# demo.js
export default a
export b
export c
# import
import a, { b, c } from './demo.js'
```
