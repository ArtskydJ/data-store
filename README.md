# data-store [![NPM version](https://img.shields.io/npm/v/data-store.svg?style=flat)](https://www.npmjs.com/package/data-store) [![NPM monthly downloads](https://img.shields.io/npm/dm/data-store.svg?style=flat)](https://npmjs.org/package/data-store) [![NPM total downloads](https://img.shields.io/npm/dt/data-store.svg?style=flat)](https://npmjs.org/package/data-store) [![Linux Build Status](https://img.shields.io/travis/jonschlinkert/data-store.svg?style=flat&label=Travis)](https://travis-ci.org/jonschlinkert/data-store)

> Easily persist and load config data. No dependencies.

Please consider following this project's author, [Jon Schlinkert](https://github.com/jonschlinkert), and consider starring the project to show your :heart: and support.

- [Install](#install)
- [Usage example](#usage-example)
- [API](#api)
- [Options](#options)
- [About](#about)

_(TOC generated by [verb](https://github.com/verbose/verb) using [markdown-toc](https://github.com/jonschlinkert/markdown-toc))_

## Install

Install with [npm](https://www.npmjs.com/):

```sh
$ npm install --save data-store
```

## Usage example

By default a JSON file is created with the name of the store in the `~/.config/data-store/` directory. This is completely customizable via [options](#options).

```js
const Store = require('data-store');
const store = new Store({ path: 'config.json' });

store.set('one', 'two'); 
console.log(store.data); //=> { one: 'two' }

store.set('x.y.z', 'boom!');
store.set({ c: 'd' });

console.log(store.get('e.f'));
//=> 'g'

console.log(store.get());
//=> { name: 'app', data: { a: 'b', c: 'd', e: { f: 'g' } } }

console.log(store.data);
//=> { a: 'b', c: 'd', e: { f: 'g' } }
```

## API

### [Store](index.js#L27)

Initialize a new `Store` with the given `name`, `options` and `default` data.

**Params**

* `name` **{string}**: Store name to use for the basename of the `.json` file.
* `options` **{object}**: See all [available options](#options).
* `defaults` **{object}**: An object to initialize the store with.

**Example**

```js
const store = require('data-store')('abc');
//=> '~/data-store/a.json'

const store = require('data-store')('abc', { cwd: 'test/fixtures' });
//=> './test/fixtures/abc.json'
```

### [.set](index.js#L68)

Assign `value` to `key` and save to the file system. Can be a key-value pair, array of objects, or an object.

**Params**

* `key` **{string}**
* `val` **{any}**: The value to save to `key`. Must be a valid JSON type: String, Number, Array or Object.
* `returns` **{object}** `Store`: for chaining

**Example**

```js
// key, value
store.set('a', 'b');
//=> {a: 'b'}

// extend the store with an object
store.set({a: 'b'});
//=> {a: 'b'}
```

### [.union](index.js#L98)

Add the given `value` to the array at `key`. Creates a new array if one doesn't exist, and only adds unique values to the array.

**Params**

* `key` **{string}**
* `val` **{any}**: The value to union to `key`. Must be a valid JSON type: String, Number, Array or Object.
* `returns` **{object}** `Store`: for chaining

**Example**

```js
store.union('a', 'b');
store.union('a', 'c');
store.union('a', 'd');
store.union('a', 'c');
console.log(store.get('a'));
//=> ['b', 'c', 'd']
```

### [.get](index.js#L124)

Get the stored `value` of `key`.

**Params**

* `key` **{string}**
* `returns` **{any}**: The value to store for `key`.

**Example**

```js
store.set('a', {b: 'c'});
store.get('a');
//=> {b: 'c'}

store.get();
//=> {a: {b: 'c'}}
```

### [.has](index.js#L146)

Returns `true` if the specified `key` has a value.

**Params**

* `key` **{string}**
* `returns` **{boolean}**: Returns true if `key` has

**Example**

```js
store.set('a', 42);
store.set('c', null);

store.has('a'); //=> true
store.has('c'); //=> true
store.has('d'); //=> false
```

### [.hasOwn](index.js#L174)

Returns `true` if the specified `key` exists.

**Params**

* `key` **{string}**
* `returns` **{boolean}**: Returns true if `key` exists

**Example**

```js
store.set('a', 'b');
store.set('b', false);
store.set('c', null);
store.set('d', true);
store.set('e', undefined);

store.hasOwn('a'); //=> true
store.hasOwn('b'); //=> true
store.hasOwn('c'); //=> true
store.hasOwn('d'); //=> true
store.hasOwn('e'); //=> true
store.hasOwn('foo'); //=> false
```

### [.del](index.js#L195)

Delete one or more properties from the store.

**Params**

* `keys` **{string|Array}**: One or more properties to delete.

**Example**

```js
store.set('foo.bar', 'baz');
console.log(store.data); //=> { foo: { bar: 'baz' } }
store.del('foo.bar');
console.log(store.data); //=> { foo: {} }
store.del('foo');
console.log(store.data); //=> {}
```

### [.clone](index.js#L218)

Return a clone of the `store.data` object.

* `returns` **{object}**

**Example**

```js
console.log(store.clone());
```

### [.clear](index.js#L233)

Reset `store.data` to an empty object.

* `returns` **{undefined}**

**Example**

```js
store.clear();
```

### [.json](index.js#L249)

Stringify the store. Takes the same arguments as `JSON.stringify`.

* `returns` **{string}**

**Example**

```js
console.log(store.json(null, 2));
```

### [.save](index.js#L266)

Calls [.writeFile()](#writefile) to persist the store to the file system, after an optional [debounce](#options) period. This method should probably not be called directly as it's used internally by other methods.

* `returns` **{undefined}**

**Example**

```js
store.save();
```

### [.unlink](index.js#L283)

Delete the store from the file system.

* `returns` **{undefined}**

**Example**

```js
store.unlink();
```

## Options

| **Option** | **Type** | **Default** | **Description** | 
| --- | --- | --- | --- |
| `debounce` | `number` | `undefined` | Milliseconds to delay writing the JSON file to the file system. This can make the store more performant by preventing multiple subsequent writes after calling `.set` or setting/getting `store.data`, but comes with the potential side effect that the config file will be outdated during the timeout. To get around this, use data-store's API to [(re-)load](#load) the file instead of directly reading the file (using `fs.readFile` for example). |
| `indent` | `number | null` | `2` | The indent value to pass to `JSON.stringify()` when writing the file to the fs, or when [.json()](#json) is called |
| `name` | `string` | `undefined` | The name to use for the store file stem (`name + '.json'` is the store's file name) |
| `home` | `string` | `process.env.XDG_CONFIG_HOME` or `path.join(os.homedir(), '.config')` | The root home directory to use |
| `base` | `string` | `path.join(home, 'data-store')` | The directory to use for data-store config files. This value is joined to `home` |
| `path` | `string` | `...` | Absolute file path for the data-store JSON file. This is created by joining `base` to `name + '.json'`. Setting this value directly will override the `name`, `home` and `base` values. |

## About

<details>
<summary><strong>Contributing</strong></summary>

Pull requests and stars are always welcome. For bugs and feature requests, [please create an issue](../../issues/new).

</details>

<details>
<summary><strong>Running Tests</strong></summary>

Running and reviewing unit tests is a great way to get familiarized with a library and its API. You can install dependencies and run tests with the following command:

```sh
$ npm install && npm test
```

</details>

<details>
<summary><strong>Building docs</strong></summary>

_(This project's readme.md is generated by [verb](https://github.com/verbose/verb-generate-readme), please don't edit the readme directly. Any changes to the readme must be made in the [.verb.md](.verb.md) readme template.)_

To generate the readme, run the following command:

```sh
$ npm install -g verbose/verb#dev verb-generate-readme && verb
```

</details>

### Related projects

You might also be interested in these projects:

* [get-value](https://www.npmjs.com/package/get-value): Use property paths like 'a.b.c' to get a nested value from an object. Even works… [more](https://github.com/jonschlinkert/get-value) | [homepage](https://github.com/jonschlinkert/get-value "Use property paths like 'a.b.c' to get a nested value from an object. Even works when keys have dots in them (no other dot-prop library can do this!).")
* [has-value](https://www.npmjs.com/package/has-value): Returns true if a value exists, false if empty. Works with deeply nested values using… [more](https://github.com/jonschlinkert/has-value) | [homepage](https://github.com/jonschlinkert/has-value "Returns true if a value exists, false if empty. Works with deeply nested values using object paths.")
* [set-value](https://www.npmjs.com/package/set-value): Create nested values and any intermediaries using dot notation (`'a.b.c'`) paths. | [homepage](https://github.com/jonschlinkert/set-value "Create nested values and any intermediaries using dot notation (`'a.b.c'`) paths.")
* [write](https://www.npmjs.com/package/write): Write data to a file, replacing the file if it already exists and creating any… [more](https://github.com/jonschlinkert/write) | [homepage](https://github.com/jonschlinkert/write "Write data to a file, replacing the file if it already exists and creating any intermediate directories if they don't already exist. Thin wrapper around node's native fs methods.")

### Contributors

| **Commits** | **Contributor** | 
| --- | --- |
| 156 | [jonschlinkert](https://github.com/jonschlinkert) |
| 4 | [doowb](https://github.com/doowb) |
| 3 | [nytamin](https://github.com/nytamin) |
| 2 | [charlike-old](https://github.com/charlike-old) |
| 1 | [jamen](https://github.com/jamen) |

### Author

**Jon Schlinkert**

* [LinkedIn Profile](https://linkedin.com/in/jonschlinkert)
* [GitHub Profile](https://github.com/jonschlinkert)
* [Twitter Profile](https://twitter.com/jonschlinkert)

### License

Copyright © 2018, [Jon Schlinkert](https://github.com/jonschlinkert).
Released under the [MIT License](LICENSE).

***

_This file was generated by [verb-generate-readme](https://github.com/verbose/verb-generate-readme), v0.6.0, on July 01, 2018._