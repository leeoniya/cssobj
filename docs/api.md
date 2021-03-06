# cssobj API

### `var result: object = cssobj(obj: object|array, config?: object, state? object)`

parse obj, generate virtual css, then render `<style>` tag into `<head>`

#### `obj : object|array`

Nothing special, just plain js `Object`, or `Array` of `Object`, with below rules:

* non-object(`string|number`) value act as css value.

* if value is object, key will act as css selector.

* if value is non-object, key will act as css property.

##### IMPORTANT: Please read the [cssobj input format spec](https://github.com/cssobj/cssobj/wiki/Input-Object-Format)

#### `config?: object`

name | type | default | description
-----|-----|-----------|---------------
intros | Array | `[ ]` | Objects or functions to return as preset css objects, which will combined into user passed source object.
local | Boolean\|Object | `false` | `true` to localize class names, using `config.local.space` as name space.
local.space | String | random string | name space for localized names, will using random string if not specified.
local.localNames | Object | `{ }` | predefined `key - val` to control each class name when localized.
cssom | Object | `{ }` | `cssom-plugin` option, supported key: `frame` as iframe DOM, `id` of style id, `attrs` for style tag.
plugins | Array | `[ ]` | Functions to add different feature, for `post`, `value`, `selector`, see [plugins](https://github.com/cssobj/cssobj#plugins) section.
state | Object | `undefined` | Default state when state arguments is falsy.

### `result: object`

The **return value** of `cssobj()` and `result.update()`, it's a js object with below keys:

name | type | description
-----|-----|-----------
intro | Object | The source js object merged from `config.intros` array.
obj | Object | The source js object for `cssobj()` function call.
root | Object | Virtual CSSOM object parsed from `obj`, mainly used for value functions and plugins.
nodes | Array | Array of virtual css nodes, for the convinence of `filter` or `map` etc.
space | String | The current localize space, or `''` if `local:false`.
localNames | Object | The current localize localNames.
mapSel | Function | Input CSS selector string, return localized CSS selector string, this want to keep consistency with `h("ul#nav.list")` in any [hyperscript](https://github.com/dominictarr/hyperscript) or [virtual-hyperscript](https://github.com/Matt-Esch/virtual-dom/tree/master/virtual-hyperscript). Function signature is `function({string} cssSelector){ return {string} mappedSelector }`.
mapClass | Function | Input space separated class list string **(WITHOUT DOT)**, return localized class list string, this want to keep consistency with `<div class="a b c"...` in HTML **class attribute**. Function signature is `function({string} classList){ return {string} mappedClassList }`.
ref | Object | Key/value pairs for named objects. Named objects is objects with `$id` string value in `obj`.
update | Function | Update the `RESULT` object from `obj`, generate `diff`, update CSSOM and all relevent data. Function signature is `function updater ([{object} data]) { return {object} result }`
-  | *updater param*:<br>data | [optional] Passed to `update()` function, and set to `RESULT` `data` value, for later use.
diff | Object | Set from `update()` function, with `added`, `removed`, `changed` nodes and props.
data | Object | Store for data parameter of `update()` function, can be referenced and changed in object functions and plugins.
config | Object | Store for `cssobj()` `config` parameter, can be referenced and changed in object functions and plugins.
cssdom | Stylesheet<br>Element | Style sheet element generated by `cssobj()` function. Each call of `cssobj()` will generate a new cssdom. `update()` function only update cssdom with `diff` result.

