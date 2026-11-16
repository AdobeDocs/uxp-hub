---
title: window.URL
description: Parse URLs, inspect URL components, and manage blob URLs.
---

# window.URL

## URL([url], [base])

Provides methods to work with a URL object.

**Throws**:

- `TypeError` if the URL is not valid.

| Param | Type | Description |
| --- | --- | --- |
| [url] | `string` | URL string representing an absolute or relative URL. |
| [base] | `string` | Base URL used to resolve a relative URL. |

**Example**

```js
const url = new URL("https://example.com?foo=1&bar=2&foo=3");
const relativeUrl = new URL("/uxp/index.html", "https://www.adobe.com");
try {
    new URL("/path", "not a valid base URL");
} catch (error) {
    console.log(error.message); // Invalid URL
}
```

## href : `string`

**Read only**

The whole URL.

```js
const url = new URL("https://example.com?foo=1&bar=2&foo=3");
console.log(url.href); // https://example.com?foo=1&bar=2&foo=3
```

## origin : `string`

**Read only**

The origin of the URL.

```js
const url = new URL("https://example.com/path");
console.log(url.origin); // https://example.com
```

## protocol : `string`

**Read only**

The protocol of the URL, including the final colon (`:`).

```js
const url = new URL("https://example.com");
console.log(url.protocol); // https:
```

## username : `string`

**Read only**

The username of the URL.

```js
const url = new URL("https://user:pass@example.com");
console.log(url.username); // user
```

## password : `string`

**Read only**

The password of the URL.

```js
const url = new URL("https://user:pass@example.com");
console.log(url.password); // pass
```

## host : `string`

**Read only**

The hostname, followed by a colon and port number if the port is nonempty.

```js
const url = new URL("https://example.com:8080/path");
console.log(url.host); // example.com:8080
```

## hostname : `string`

**Read only**

The domain name or IP address of the URL.

```js
const url = new URL("https://example.com:8080/path");
console.log(url.hostname); // example.com
```

## port : `string`

**Read only**

The port number of the URL. Returns an empty string for a default port, such as 80 for HTTP or 443 for HTTPS.

```js
const url = new URL("https://example.com");
const customPortUrl = new URL("https://example.com:8080");
console.log(url.port); // ""
console.log(customPortUrl.port); // "8080"
```

## pathname : `string`

**Read only**

The location in the hierarchical path of the URL, prefixed by a slash (`/`).

```js
const url = new URL("https://example.com/path?foo=1");
console.log(url.pathname); // /path
```

## search : `string`

**Read only**

The serialized query, prefixed by a question mark (`?`). Returns an empty string if the URL has no query.

```js
const url = new URL("https://example.com?foo=1&bar=2&foo=3");
console.log(url.search); // ?foo=1&bar=2&foo=3
```

## hash : `string`

**Read only**

The fragment identifier, prefixed by a hash (`#`). Returns an empty string if the URL has no fragment identifier.

```js
const url = new URL("https://example.com#foo");
console.log(url.hash); // #foo
```

## searchParams : `URLSearchParams`

**Read only**

A [URLSearchParams](url-search-params.md) object containing the components of the URL's query string.

```js
const url = new URL("https://example.com?foo=1&bar=2&foo=3");
console.log(url.searchParams.getAll("foo")); // ["1", "3"]
```

## toString()

Returns the whole URL as a string.

**Returns**: `string`

## toJSON()

Returns a serialized version of the URL.

**Returns**: `string`

```js
const url = new URL("https://example.com?foo=1&bar=2&foo=3");
console.log(url.toJSON()); // "https://example.com?foo=1&bar=2&foo=3"
```

## createObjectURL(blob)

Creates a blob URL pointing to the supplied object. Use `URL.revokeObjectURL()` to release it.

**Returns**: `string` - The blob URL.

| Param | Type | Description |
| --- | --- | --- |
| blob | `Blob` | Blob object for which to create a URL. |

```js
const blob = new Blob(["Hello, world!"], { type: "text/plain" });
const url = URL.createObjectURL(blob);
```

## revokeObjectURL(url)

Releases an object URL previously created by `URL.createObjectURL()`.

| Param | Type | Description |
| --- | --- | --- |
| url | `string` | Object URL to release. |

```js
const blob = new Blob(["Hello, world!"], { type: "text/plain" });
const url = URL.createObjectURL(blob);
URL.revokeObjectURL(url);
```