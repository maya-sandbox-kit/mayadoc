# 🧾 White Paper  
## **Direct Event Invocation in Maya MFEs Using `ev` & Key Inheritance**  
### 📌 Recommended Approach Over Pub/Sub for Parent-to-Child Rendering

---

## 📖 Overview

This document proposes a **direct event invocation** pattern for Maya Micro Frontends (MFEs) when a **parent MFE controls multiple child MFEs** as part of its view. This method leverages the native `ev` object and **key inheritance** to seamlessly pass context and trigger child MFE events.

This approach is intended to **replace `Maya.Store.Publish/Subscribe`** in cases where synchronous rendering and tightly scoped communication are needed, particularly for UI layout orchestration.

---

## 🧠 Motivation

### Current State with `Publish/Subscribe`:
- Maya supports a Pub/Sub mechanism for **asynchronous, decoupled** MFE communication.
- While effective in some contexts, Pub/Sub introduces:
  - **Extra ceremony**: requiring topic declarations, message handling via `onMessage`, and filtering logic.
  - **Delayed response**: execution is indirect and async by design.
  - **Weak coupling**: lacks execution guarantees and direct traceability.

### Why It’s Suboptimal for Parent→Child Scenarios:
When a parent MFE orchestrates **multiple embedded child MFEs**, Pub/Sub becomes overkill:
- Parent already **knows** which child needs to render.
- Parent already **owns** the instance key and context.
- Communication is **intra-view**, not global.

---

## ✅ Proposed Pattern: Direct Event Invocation

Maya MFEs support **key inheritance** and scoped event dispatching. This allows a parent to directly call a child’s event handler using:

```js
await Maya.Store.<child-store>.events.<handler>({ key, parent, view, query, ... });
```

Where:
- `key` is **inherited** from the parent.
- `parent` is an explicit reference to the calling MFE.
- Data updated in child MFE using this key is **automatically scoped** and ready for rendering.

---

## 🔧 How It Works

### 1. **Child MFE Declaration**
Child MFEs inherit the key from the parent via `connectedCallback()`:

```js
this.setKey(
  this.attributes.key?.value ||
  (this.keyInheritance && host?.getKey ? host.getKey() : this.dt)
);
```

### 2. **Parent MFE Triggers Child Event**
Directly invoke child MFE events:

```js
await Maya.Store.childstore.events.loadData({
  key: this.getKey(),
  view: 'summary',
  parent: this
});
```

### 3. **Child Updates Store with Same Key**
Child stores data using the shared `key`:

```js
Maya.Store.SetData({ store: 'childstore', key: ev.key })(data);
```

This ensures:
- Seamless store updates.
- Automatic rerender of child MFE.
- No key mismatch or data pollution.

---

## 🔁 Why Rerendering Becomes Trivial

### Maya.Store.SetData:
Handles scoped updates and auto-renders:

```js
Maya.Store.SetData({ store, key, view })(data);
```

Because both parent and child share the same `key`, re-rendering the child is automatic and requires **no manual orchestration**.

### Example Use Case:
```js
Maya.Store.childpanel.events.showDetails({
  key: this.getKey(),
  parent: this,
  entity: 'task',
  view: 'detail'
});
```

The child MFE loads into its slot, fetches scoped data, and renders without a pub/sub topic.

---

## 🆚 Comparison: Direct Invocation vs Pub/Sub

| Criteria                        | Direct Event Invocation             | Pub/Sub                            |
|---------------------------------|-------------------------------------|------------------------------------|
| Coupling                        | Tight (synchronous control)         | Loose (decoupled, async)          |
| Context Awareness               | Full (`ev.key`, `parent`)           | Minimal (requires manual parsing) |
| Performance                    | High (direct call)                  | Moderate (async propagation)      |
| Code Simplicity                | ✅ Clean and declarative            | ❌ Extra handlers and mapping      |
| Rerender Management             | Automatic via shared key            | Manual via topic/data matching    |
| Best Use Case                   | Rendering sub-MFEs within parent    | Broadcasting async updates        |

---

## 🔄 When to Use This Pattern

| ✅ Use Direct Invocation…                       |
|------------------------------------------------|
| Parent MFE dynamically controls child MFEs     |
| Child MFEs are scoped to same `key`            |
| Context (view, entity, query) is pre-known     |
| Need deterministic rendering & performance     |

| ❌ Avoid for...                                 |
|------------------------------------------------|
| Peer-to-peer updates between unrelated MFEs    |
| Global cross-cutting concerns (flags, alerts)  |
| Complex orchestration requiring loose coupling |

---

## 🧩 Best Practices

- Always pass the parent key via `ev.key` when invoking child MFE events.
- Use `this.getKey()` in the parent to retrieve the active instance key.
- Inside child MFEs, use `Maya.Store.SetData({ key: ev.key })` to persist data.
- Avoid `Publish/Subscribe` unless components are decoupled or independently reactive.

---

## 📘 Summary

Maya’s architecture is uniquely positioned to benefit from **tight, scoped communication** between parent and child MFEs. By adopting **direct event invocation with key inheritance**, developers can:
- Eliminate boilerplate message handling
- Maintain shared data state
- Enable highly performant and context-rich rendering

> **Recommendation:**  
For all scenarios where a parent MFE loads multiple embedded child MFEs synchronously, this pattern should be the default over Pub/Sub.
