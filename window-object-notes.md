# `window` Object — Browser Global

> `window` is the global object in the browser. Represents the browser tab and contains all Web APIs.

---

## Key Concept
- Every top-level `var` becomes `window.var`
- `window.` prefix is optional (e.g. `fetch()` = `window.fetch()`)
- Not available in Node.js / SSR — always guard if using Next.js

```js
// SSR-safe guard
if (typeof window !== 'undefined') { ... }

// Or inside useEffect (React) — always safe
useEffect(() => { ... }, []);
```

---

## Common Properties & Methods

### Navigation
```js
window.location.href          // current full URL
window.location.pathname      // e.g. "/dashboard"
window.location.search        // query string e.g. "?id=1"
window.location.reload()      // refresh page
window.history.back()         // go back
window.history.forward()      // go forward
window.history.pushState()    // change URL without reload
```

### Storage
```js
window.localStorage           // persists across sessions
window.sessionStorage         // cleared on tab close
```

### Dimensions
```js
window.innerWidth             // viewport width
window.innerHeight            // viewport height
window.screen.width           // full screen width
window.screen.height          // full screen height
window.scrollX                // horizontal scroll position
window.scrollY                // vertical scroll position
```

### Timers
```js
window.setTimeout(fn, ms)     // run once after delay
window.setInterval(fn, ms)    // run repeatedly
window.clearTimeout(id)
window.clearInterval(id)
```

### Network
```js
window.fetch(url, options)    // HTTP requests
window.navigator.onLine       // true/false — internet connected?
window.navigator.userAgent    // browser info string
```

### DOM
```js
window.document               // entire HTML document
window.document.getElementById('id')
window.document.querySelector('.class')
```

### Dialogs
```js
window.alert('msg')           // popup message
window.confirm('sure?')       // returns true/false
window.prompt('enter:')       // returns string input
```

### Events
```js
window.addEventListener('resize', fn)
window.addEventListener('scroll', fn)
window.addEventListener('popstate', fn)   // back/forward button
window.addEventListener('online', fn)     // internet connected
window.addEventListener('offline', fn)    // internet lost
window.addEventListener('beforeunload', fn) // tab close/refresh
```

---

## Explore `window` in DevTools Console

```js
console.log(window)                        // full object
Object.keys(window)                        // all keys
Object.keys(window).filter(k => typeof window[k] === 'function')  // all methods
Object.keys(window).filter(k => typeof window[k] === 'object')    // all objects
```

> Or just type `window.` in DevTools and use autocomplete.

---

## React-Specific Tips

| Scenario | Approach |
|---|---|
| Accessing `window` in a component | Use inside `useEffect` |
| SSR (Next.js) | Guard with `typeof window !== 'undefined'` |
| Resize / scroll tracking | Add listener in `useEffect`, clean up on unmount |
| Back button control | `window.addEventListener('popstate', fn)` |

```js
// Resize example in React
useEffect(() => {
  const handleResize = () => console.log(window.innerWidth);
  window.addEventListener('resize', handleResize);
  return () => window.removeEventListener('resize', handleResize); // cleanup
}, []);
```

---

## Quick Reference

| Property | Description |
|---|---|
| `window.location` | URL info & navigation |
| `window.history` | Browser history stack |
| `window.document` | DOM access |
| `window.localStorage` | Persistent key-value storage |
| `window.sessionStorage` | Session key-value storage |
| `window.navigator` | Browser/device info |
| `window.screen` | Screen dimensions |
| `window.innerWidth/Height` | Viewport size |
| `window.scrollX/Y` | Scroll position |
| `window.fetch` | HTTP requests |
| `window.setTimeout/Interval` | Timers |
