---
title: window.URLSearchParams
description: Construct, inspect, and modify URL query parameters.
---

# window.URLSearchParams

**Since**: v9.1.0

## URLSearchParams([init])

Provides methods to work with the query string of a [URL](url.md).
The optional `init` argument accepts any one of the following forms.

| Param | Type | Description |
| --- | --- | --- |
| [init] | `string` | Key-value pairs separated by `&`, with each key linked to its value by `=`. |
| [init] | `Array` | Nested array in which each sub-array contains a key and value. |
| [init] | `Object` | Object containing key-value pairs. |

```js
const param1 = new URLSearchParams("name=John&age=30");
const param2 = new URLSearchParams([["name", "John"], ["age", "30"]]);
const param3 = new URLSearchParams({ name: "John", age: "30" });
```

## append(name, value)

Appends a new key-value pair.

| Param | Type | Description |
| --- | --- | --- |
| name | `string` | Name of the parameter to append. |
| value | `string` | Value of the parameter to append. |

```js
const params = new URLSearchParams("name=John&age=30");
params.append("city", "New York");
console.log(params.toString()); // name=John&age=30&city=New+York
```

## delete(name)

Deletes all parameters with the specified name and their associated values.

| Param | Type | Description |
| --- | --- | --- |
| name | `string` | Name of the parameters to delete. |

```js
const params = new URLSearchParams("foo=1&bar=2&foo=3");
params.delete("foo");
console.log(params.toString()); // bar=2
```

## get(name)

Returns the first value associated with the given parameter, or `null` if it is not found.

**Returns**: `string` | `null`

| Param | Type | Description |
| --- | --- | --- |
| name | `string` | Name of the parameter to return. |

```js
const params = new URLSearchParams("foo=1&bar=2&foo=3");
console.log(params.get("foo")); // "1"
console.log(params.get("baz")); // null
```

## getAll(name)

Returns all values associated with the given parameter as an array. The array is empty if the parameter is not found.

**Returns**: `Array<string>`

| Param | Type | Description |
| --- | --- | --- |
| name | `string` | Name of the parameter to return. |

```js
const params = new URLSearchParams("foo=1&bar=2&foo=3");
console.log(params.getAll("foo")); // ["1", "3"]
console.log(params.getAll("baz")); // []
```

## has(name)

Indicates whether the specified parameter exists.

**Returns**: `boolean`

| Param | Type | Description |
| --- | --- | --- |
| name | `string` | Name of the parameter to find. |

```js
const params = new URLSearchParams("foo=1&bar=2&foo=3");
console.log(params.has("foo")); // true
console.log(params.has("baz")); // false
```

## set(name, value)

Sets the value of the given parameter. If several values exist, deletes the others. If the parameter does not exist, creates it.

| Param | Type | Description |
| --- | --- | --- |
| name | `string` | Name of the parameter to set. |
| value | `string` | Value to set. |

```js
const params = new URLSearchParams("foo=1&bar=2&foo=3");
params.set("foo", "4");
console.log(params.toString()); // foo=4&bar=2
params.set("baz", "5");
console.log(params.toString()); // foo=4&bar=2&baz=5
```

## sort()

Sorts all key-value pairs by their keys in ascending order.

```js
const params = new URLSearchParams("foo=1&bar=2&foo=3");
params.sort();
console.log(params.toString()); // bar=2&foo=1&foo=3
```

## toString()

Returns a query string suitable for use in a URL, without the leading question mark.

**Returns**: `string`

```js
const params = new URLSearchParams("foo=1&bar=2&foo=3");
console.log(params.toString()); // foo=1&bar=2&foo=3
```