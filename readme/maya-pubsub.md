# 📄 Whitepaper: Maya Pub/Sub – Decoupled MFE Communication

## Abstract
Maya introduces a lightweight, loosely-coupled **Publish/Subscribe (Pub/Sub)** communication model for Micro Frontends (MFEs), enabling scalable inter-component messaging without direct references. This whitepaper explains the architecture, usage, best practices, and common pitfalls of the Pub/Sub model in Maya.

---

## 1. Introduction

In Maya, MFEs are isolated, independently deployable units. Communication across these units is necessary for building cohesive applications. The Pub/Sub pattern is used for **decoupled, asynchronous messaging**, allowing MFEs to **listen to events or broadcast actions** without dependency chains.

---

## 2. Core Concepts

### 🔁 Pub/Sub API

| Function | Description |
|---------|-------------|
| `Maya.Store.Publish({ topic })({ msg })` | Sends a message to all MFEs subscribed to the topic. |
| `Maya.Store.Subscribe({ topic })(mfe)` | Subscribes an MFE to a topic to receive future messages. |
| `Maya.Store.UnSubscribe({ topic })(mfe)` | Removes an MFE from a topic to prevent message receipt. |

---

## 3. Example Workflow

### ✅ Subscribing to a Topic

```js
onLoad = async () => {
  await Maya.Store.Subscribe({ topic: 'search:update' })(this);
}
```

### ✅ Handling the Message

```js
onMessage = (option) => async (msg) => {
  switch(option.topic) {
    case 'search:update':
      Maya.Store.SetData({ store: 'results', key: option.key })(msg);
      break;
  }
}
```

### ✅ Publishing the Message

```js
await Maya.Store.Publish({ topic: 'search:update' })({ query: 'pizza' });
```

---

## 4. Use Cases

| Scenario | Description |
|---------|-------------|
| Dashboard → Widget Sync | A dashboard publishes a date range → all widgets refresh. |
| Master → Detail View | Selecting a record in a list publishes an `item:selected`. |
| Modal Triggers | A form submission publishes `form:submitted`, prompting confirmation MFE to show. |
| Notifications | Background tasks publish `notification:new` messages to the notification bell. |

---

## 5. ✅ Patterns

| Pattern | Description |
|--------|-------------|
| **One Topic, Many Listeners** | Several MFEs listen to a shared topic, e.g. `report:refresh`. |
| **Topic-Based Routing** | Use topic names as namespaces (`task:`, `user:`, `report:`) for semantic clarity. |
| **Scoped State Transfer** | Transmit only what's needed (e.g. `{ id, name }` instead of full objects). |
| **Subscription in `onLoad`**, Cleanup in `onUnload` | Keeps lifecycle clean and prevents memory leaks. |
| **Single Responsibility Per Topic** | Keep topics focused; e.g. `user:login` vs `user:update` vs `user:logout`. |

---

## 6. ❌ Anti-Patterns

| Anti-Pattern | Why to Avoid |
|--------------|--------------|
| ❌ **Overloaded Topics** (`"global"` or `"message"`) | Causes confusion, hard to trace message flow. |
| ❌ **Subscribing in Constructor** | Subscriptions should happen **after component is mounted** (`onLoad`). |
| ❌ **Not Unsubscribing** | Leads to **duplicate events** or **ghost listeners** after MFE is removed. |
| ❌ **Publishing Too Frequently** | Avoid flooding system with rapid-fire messages. Use debounce/throttle. |
| ❌ **Using SharedData Instead of Pub/Sub** | SharedData is for **global config/state**, not eventing. |

---

## 7. Best Practices

- ✅ Use semantic topic names (`"user:created"`, `"task:archived"`).
- ✅ Always unsubscribe on `onUnload`.
- ✅ Avoid business logic in `onMessage`—delegate to `store.events`.
- ✅ Prefer scoped messages; avoid publishing full state trees.
- ✅ Log subscriptions/messages in debug mode during dev.

---

## 8. Conclusion

Maya’s Pub/Sub system is simple, elegant, and **built for scale**. It empowers MFEs to remain **isolated** yet **collaborative**, enabling rich, event-driven user interfaces.

By following established patterns and avoiding common pitfalls, teams can build **cleaner**, **faster**, and more **maintainable** MFE applications.

---

Would you like this turned into a **visual PDF**, or should I generate a **diagram** (like Pub → Topic → Sub) for documentation or onboarding use?