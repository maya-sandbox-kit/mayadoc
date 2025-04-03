### **Maya Multi-Instance + Store Mapping Diagram**

```
Frontend (UI Layer)
─────────────────────────────────────────────────────
<maya-maya>
 ├── <maya-slot name="main">
 │    ├── <albert-inventory key="123" view="main">
 │    └── <albert-inventory key="789" view="detail">
 └── <maya-slot name="right">
      └── <albert-task key="456" view="detail">


Store (Data Layer)
─────────────────────────────────────────────────────
Maya.Store.inventory = {
  name: 'inventory',
  data: {
    '123': { Items: [...] },        <-- for key 123 (main view)
    '789': { name: 'Pen', qty: 50 } <-- for key 789 (detail view)
  },
  self: {
    '123': {},                      <-- self data for form inputs (if any)
    '789': {}
  },
  state: {
    '123': { tab: 'info' },         <-- optional UI state
    '789': { tab: 'audit' }
  }
}

Maya.Store.task = {
  name: 'task',
  data: {
    '456': { id: 1, title: 'Fix Bug' }
  },
  self: {
    '456': {}
  },
  state: {
    '456': {}
  }
}
```

---

### **Mapping Breakdown**

| MFE Instance             | Store Used      | Key  | Mapped To                         |
|--------------------------|-----------------|------|-----------------------------------|
| `albert-inventory` (main) | `inventory`      | 123  | `Maya.Store.inventory.data['123']` |
| `albert-inventory` (detail) | `inventory`   | 789  | `Maya.Store.inventory.data['789']` |
| `albert-task` (detail)   | `task`           | 456  | `Maya.Store.task.data['456']`     |

---

This **key-based scoping** ensures:
- Multiple instances of the same MFE won’t interfere.
- Each MFE behaves like its own sandboxed component.
- The store can handle dozens of instances with different keys simultaneously.
