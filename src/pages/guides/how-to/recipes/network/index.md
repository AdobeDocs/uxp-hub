---
title: Network Operations
description: Allow trusted domains, send HTTP requests, open WebSocket connections, and handle network failures in UXP plugins.
keywords:
  - network
  - fetch
  - xhr
  - websockets
  - permissions
  - api requests
  - async
  - http
contributors:
  - https://github.com/karan0207
---

# Connect to network services

Use `fetch()` for most HTTP requests, `XMLHttpRequest` when progress events are required, and `WebSocket` for persistent two-way communication. Every remote endpoint must match a domain declared in the manifest.

<Fragment src="../_shared/prerequisites.md" />

## Allow network domains

UXP blocks undeclared network access. Add every HTTP or WebSocket endpoint the plugin needs to [`requiredPermissions.network`](../../../explanation/concepts/manifest/index.md#networkpermission).

List the allowed domains in `manifest.json`:

```json
{
  "requiredPermissions": {
    "network": {
      "domains": [
        "https://api.example.com",
        "https://*.adobe.io"
      ]
    }
  }
}
```

Use a wildcard only when the plugin must reach several related hosts, such as `"https://api.*.example.com"`. Requests to any unmatched domain fail with a permission error.

<InlineAlert variant="warning" slots="text"/>

Use HTTPS endpoints. Although the APIs accept HTTP URLs, macOS restricts insecure HTTP traffic.

## Choose a network API

UXP supports three primary ways to perform network communication:

| API            | Best For                            | Supported Features                 |
| ---------------- | -------------------------------------- | ------------------------------------- |
| fetch          | Modern, promise-based HTTP requests | JSON, text, binary data, streaming |
| XMLHttpRequest | Legacy compatibility                | Progress events, upload tracking   |
| WebSocket      | Real-time communication             | Persistent bidirectional data flow |

These APIs are available globally; no `require()` call is needed.

### Send requests with `fetch()`

Use `fetch()` for promise-based JSON, text, and binary requests.

<CodeBlock slots="heading, code" repeat="2" languages="JavaScript, JSON" />

#### index.js

```js
async function getForecast() {
  try {
    const response = await fetch(
      "https://api.weather.gov/gridpoints/MTR/99,82/forecast"
    );

    if (!response.ok) {
      throw new Error(
        `HTTP error ${response.status}: ${response.statusText}`
      );
    }
    const data = await response.json();
    console.log(
      `Forecast: ${data.properties.periods[0].detailedForecast}`
    );
  } catch (error) {
    console.error("Failed to fetch forecast:", error);
  }
}
```

#### manifest.json

```json
{
  "requiredPermissions": {
    "network": {
      "domains": ["https://api.weather.gov"]
    }
  }
}
```

<InlineAlert variant="info" slots="text"/>

Parse the response with `json()`, `text()`, or `blob()` for the expected data type. Each method returns a [Promise](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Promise).

### Load a remote image

An image URL also requires its domain in the allowlist:

<CodeBlock slots="heading, code" repeat="2" languages="HTML, JSON" />

#### index.html

```html
<img src="https://picsum.photos/300/200" alt="A random image" />
```

#### manifest.json

```json
{
  "requiredPermissions": {
    "network": {
      "domains": ["https://picsum.photos/"]
    }
  }
}
```

The `<img>` tag works the same as in a browser, provided the remote domain is allow-listed.

### Send a JSON request

Set the content type and serialize the body when an API expects a JSON [POST request](https://developer.mozilla.org/en-US/docs/Web/HTTP/Reference/Methods/POST):

```js
async function postUserData(user) {
  try {
    const response = await fetch("https://api.example.com/users", {
      method: "POST",
      headers: { "Content-Type": "application/json" },
      body: JSON.stringify(user)
    });

    if (!response.ok) throw new Error(`Server error: ${response.status}`);
    const result = await response.json();
    console.log("User created:", result);
  } catch (error) {
    console.error("Failed to post user data:", error);
  }
}

// Example usage
postUserData({ name: "Jamie", role: "Editor" });
```

<InlineAlert variant="warning" slots="heading, text"/>

Account for UXP runtime differences

UXP does not implement every browser API. For example, `TextDecoder` is unavailable for decoding a response stream. Check the [UXP tech stack](../../../explanation/tech-stack/index.md#what-you-write-in) before relying on browser-specific helpers.

```js
async function fetchStreamedData() {
  const response = await fetch("https://api.example.com/stream");
  const reader = response.body.getReader();
  const decoder = new TextDecoder(); // Not available in UXP.
  // ...
}
```

### Track progress with `XMLHttpRequest`

Use the event-driven `XMLHttpRequest` API when an operation needs progress events or upload monitoring.

<CodeBlock slots="heading, code" repeat="2" languages="JavaScript, JSON" />

#### index.js

```js
function getForecastWithXHR() {
  const xhr = new XMLHttpRequest();
  xhr.open(
    "GET",
    "https://api.weather.gov/gridpoints/MTR/99,82/forecast"
  );
  xhr.responseType = "json";

  xhr.onload = () => {
    if (xhr.status === 200) {
      console.log(
        `Forecast: ${xhr.response.properties.periods[0].detailedForecast}`
      );
    } else {
      console.error(`XHR failed with status ${xhr.status}`);
    }
  };

  xhr.onerror = () => console.error("Network error occurred");
  xhr.send();
}
```

#### manifest.json

```json
{
  "requiredPermissions": {
    "network": {
      "domains": ["https://api.weather.gov"]
    }
  }
}
```

### Maintain a WebSocket connection

Use a WebSocket for persistent, bidirectional updates.

<CodeBlock slots="heading, code" repeat="2" languages="JavaScript, JSON" />

#### index.js

```js
let socket;

async function connectToServer() {
  try {
    if (socket) {
      console.log("Disconnecting existing socket...");
      socket.close();
      socket = null;
      return;
    }

    socket = new WebSocket(
      "wss://javascript.info/article/websocket/demo/hello"
    );

    socket.onopen = () => {
      console.log("WebSocket connection established");
      socket.send("Hello from your UXP plugin!");
    };

    socket.onmessage = (event) => {
      console.log(`Message from server: ${event.data}`);
    };

    socket.onerror = (error) => {
      console.error("WebSocket error:", error);
    };

    socket.onclose = () => {
      console.log("Connection closed");
      socket = null;
    };

  } catch (error) {
    console.error("Failed to connect via WebSocket:", error);
  }
}
```

#### manifest.json

```json
{
  "requiredPermissions": {
    "network": {
     "domains": ["wss://javascript.info"]
    }
  }
}
```

<InlineAlert variant="info" slots="heading,text"/>

UXP supports WebSocket clients only

Plugins can connect to WebSocket servers, but cannot host or accept incoming connections.

## Handle errors and timeouts

Network calls can fail: the user may be offline, the endpoint may be down, or your permission list might be incomplete.

- Always wrap network calls in `try...catch` blocks.
- Use `response.ok` to detect HTTP errors.
- Set reasonable timeouts for long operations.
- Log informative errors using `console.error()`.

```js
async function safeFetch(url, options = {}, timeoutMs = 8000) {
  const controller = new AbortController();
  const timeout = setTimeout(() => controller.abort(), timeoutMs);

  try {
    const response = await fetch(url, {
      ...options,
      signal: controller.signal,
    });
    if (!response.ok) throw new Error(`HTTP ${response.status}`);
    return await response.json();
  } catch (error) {
    console.error("Network request failed:", error);
    throw error;
  } finally {
    clearTimeout(timeout);
  }
}
```

## Troubleshoot network requests

| Symptom                                  | Likely Cause                       | Solution                                                                        |
| ------------------------------------------- | --------------------------------------- | ------------------------------------------------------------------------------------ |
| TypeError: Failed to fetch               | Domain not allow-listed            | Add the domain under `requiredPermissions.network.domains`                      |
| Connection fails only on macOS           | HTTPS required                     | macOS disallows http:// URLs; use https:// instead                             |
| Request blocked by CORS                  | Remote server missing CORS headers | Ensure your server allows requests from UXP (check Access-Control-Allow-Origin) |
| WebSocket connection closed unexpectedly | Server-side disconnect             | Check for idle timeout or SSL misconfiguration                                  |
