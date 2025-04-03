
# 🧾 White Paper  
## **Unified Messaging Strategy in Maya**  
### 📡 Direct Invocation vs Publish/Subscribe – Patterns, Use Cases, and Best Practices

---

## 🧠 Introduction

Modern Maya applications are composed of multiple micro frontends (MFEs), each encapsulating specific functionality. As applications scale, **inter-MFE communication** becomes critical.

Maya offers **two primary communication models**:

1. **Direct Event Invocation** – Synchronous, scoped, tight coupling  
2. **Publish/Subscribe (Pub/Sub)** – Asynchronous, loosely coupled broadcasting

This paper explores both patterns, compares their usage, and defines a **unified strategy** for choosing the right tool based on intent and architecture.

---

## 🔁 Messaging Models in Maya

### 1. 📬 **Direct Event Invocation**
Directly calls a child MFE’s event handler, passing the full `ev` object. Ideal when parent and child share context and state.

**How it works:**
- Parent invokes event from child’s store
- `ev.key` is shared (via key inheritance)
- Data is written to the same scoped store
- Rendering is automatic via `Maya.Store.SetData`

**Example:**
```js
await Maya.Store.childstore.events.load({
  key: this.getKey(),
  parent: this,
  view: 'summary'
});

// OR 
await Maya.Store.childstore.events.load(ev)
```

**Benefits:**
- Minimal boilerplate
- High performance
- Tight coordination between parent and child
- Natural view rendering via shared key

---

### 2. 📣 **Publish/Subscribe (Pub/Sub)**
Uses `Maya.Store.Publish` to send messages and `Subscribe` to react to them asynchronously.

**How it works:**
- Sender publishes on a topic
- Any subscriber listening on that topic receives the message
- Message handled via `onMessage(option)`

**Example:**
```js
Maya.Store.Publish({ topic: 'filters.updated' })({ filter: 'recent' });

Maya.Store.Subscribe({ topic: 'filters.updated' })(this);

this.onMessage = (option) => async (msg) => {
  // Update local state or trigger rendering
};
```

**Benefits:**
- Decoupled architecture
- Many-to-many communication
- Excellent for global state or signals
- Non-blocking and fault-tolerant

---

## 🧮 When to Use What

| Use Case | Direct Invocation | Pub/Sub |
|----------|-------------------|---------|
| Parent rendering multiple child MFEs | ✅ Yes | ❌ No |
| Sibling or unrelated MFEs sharing a signal | ❌ No | ✅ Yes |
| One-time broadcasts (e.g. alert, success msg) | ❌ No | ✅ Yes |
| Layout-specific, stateful child updates | ✅ Yes | ❌ No |
| Reactive global filters | ❌ No | ✅ Yes |
| Modal ↔ Content Panel communication | ✅ Yes (if within same view) | ✅ Yes (if across views) |

---

## 🧩 Design Patterns

### 🔄 Direct Messaging Pattern

```js
// Parent triggers
await Maya.Store.chart.events.renderChart({ key: this.getKey(), view: 'bar', query: { id: 123 } });

// Child handles
onLoad = async (ev) => {
  const data = await API.fetchChart(ev.query.id);
  Maya.Store.SetData({ store: 'chart', key: ev.key })(data);
};
```

### 🧭 Pub/Sub Pattern

```js
// Publisher
Maya.Store.Publish({ topic: 'user.changed' })({ id: 42 });

// Subscriber (inside child MFE)
onMessage = (option) => async (msg) => {
  const data = await fetchUser(msg.id);
  Maya.Store.SetData({ store: 'user', key: option.key })(data);
};
```

---

## 🏗️ Hybrid Use: Combining Both

Often, an application benefits from using **both models in tandem**:

> ✅ Use **direct calls** when the parent knows who it’s invoking.  
> ✅ Use **Pub/Sub** to **notify** others who may or may not act on it.

### Example: Task Panel Loads Assignee Selector

```js
// Parent
await Maya.Store.assigneeselector.events.loadAssignees({ key: this.getKey(), parent: this });

// Meanwhile, broadcast to others
Maya.Store.Publish({ topic: 'assignee.panel.opened' })({ context: 'tasks' });
```

---

## ⚖️ Comparison Summary

| Feature                     | Direct Invocation         | Pub/Sub                     |
|----------------------------|---------------------------|-----------------------------|
| Coupling                   | Tight                     | Loose                       |
| Communication Direction    | 1-to-1 (caller knows callee) | 1-to-N (unknown receivers) |
| Performance                | High (sync)               | Medium (async)              |
| Context Propagation        | Full (`ev`, `key`, `parent`) | Must be included in message |
| View Updates               | Automatic via `SetData`   | Manual via `onMessage`      |
| Typical Use Case           | Parent-child control flow | Cross-MFE signaling         |

---

## 📌 Best Practices

- ✅ Always use **key inheritance** to scope child MFEs to the same `key`.
- ✅ Use `Maya.Store.SetData()` inside child to persist context-sensitive data.
- ✅ Prefer direct invocation when building **MFE-based layouts** or flows.
- ✅ Use Pub/Sub for **global alerts**, **notifications**, or **decoupled workflows**.
- ❌ Don’t mix the two models in a single message flow unless it’s justified by context separation.

---

## 📘 Summary

Maya's flexible eventing system offers **two robust communication models**:

- **Direct Invocation** – Precision, performance, and clarity for parent-to-child interactions.
- **Publish/Subscribe** – Flexibility, scalability, and decoupling for broader system signals.

> **By aligning intent with mechanism**, teams can reduce complexity, improve traceability, and ensure MFEs remain modular and predictable.
