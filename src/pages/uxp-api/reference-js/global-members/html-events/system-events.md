---
title: System sleep and awake events
description: Receive system sleep and wakeup notifications in a UXP plugin.
---

# System sleep and awake events

**Since**: v9.2.0

UXP plugins can receive system sleep and wakeup notifications in the JavaScript layer. These notifications help plugins manage features such as media playback when the system sleeps or wakes up.

Register listeners on `window` for the `sleep` and `awake` events:

```js
function onSleep() {
    console.log("System is sleeping");
}

function onAwake() {
    console.log("System is awake");
}

window.addEventListener("sleep", onSleep);
window.addEventListener("awake", onAwake);
```

Remove the listeners when they are no longer needed:

```js
window.removeEventListener("sleep", onSleep);
window.removeEventListener("awake", onAwake);
```