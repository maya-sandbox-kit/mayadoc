## 👋 Maya for React Developers: Mindset Shift

| Concept        | React                           | Maya                                  |
|----------------|----------------------------------|----------------------------------------|
| Components     | JSX + JS logic                   | HTML + Mustache + store in `index.js`  |
| State          | `useState`, Redux, Context       | `Maya.Store` per-MFE                   |
| Lifecycle      | `useEffect`, `componentDidMount` | `onLoad`, `onRender`, `onQuery`        |
| Routing        | React Router                     | `Maya.Route()`, `Maya.Load()`          |
| Communication  | Props, Context, Events           | Pub/Sub (`Maya.Store.Publish/Subscribe`) |
| Composition    | Nested Components                | Nested MFEs via `<maya-slot>`          |
| Deployment     | Webpack build                    | Static JS/HTML via CDN, versioned      |

---

## 📦 MFE = Web Component

In React, you’d define a component:
```jsx
function Inventory() {
  return <div>...</div>
}
```

In Maya, you'd define a **custom element**:
```js
class InventoryMFE extends MayaMFE {
  constructor() {
    super();
    this.setStore(Maya.Store.inventory);
    this.setView('main');
  }

  onLoad = async (options) => {
    return Maya.Store.inventory.events.onLoad(options);
  };
}
window.customElements.define('albert-inventory', InventoryMFE);
```

And your UI is driven by a **Mustache template**, not JSX.

---

## 🔁 State & Data Flow

Instead of `useState`, Maya uses:

```js
Maya.Store.inventory = {
  name: 'inventory',
  data: {},
  events: {
    onLoad: async (options) => {
      const data = await API.fetchInventory();
      Maya.Store.SetData({ store: 'inventory', key: options.key })(data);
    }
  }
}
```

That triggers rendering of the view template, where you’d write:

```html
<h1>{{maya.title}}</h1>
{{#items}}
  <p>{{name}}</p>
{{/items}}
```

No `setState`, just `SetData`.

---

## 🔗 Child MFEs = Child Components

In React:
```jsx
<ChildComponent data={someData} />
```

In Maya:
```html
<albert-childinventory slot="main"></albert-childinventory>
```

Loaded dynamically via:
```js
Maya.Load('childinventory/main?target=main');
```

---

## 🧠 How I’d Teach Step-by-Step:

### 1. Translate React mindset:
- "Forget JSX. We’re templating via Mustache."
- "Forget `useState`, we use `Maya.Store.SetData`."

### 2. Build a mini CRUD app:
- A simple MFE (`index.js`, `main.html`)
- Define a `Store`, setup `onLoad`, wire up `maya-button`

### 3. Teach slots:
- Load MFEs dynamically via `<maya-slot>` or `Maya.Load()`

### 4. Teach communication:
- Use `Maya.Store.Publish()` and `Subscribe()` for MFE-to-MFE talking

### 5. Deploy to CDN:
- Show how MFEs are versioned (`version.json`), deployed independently

---

## 🔥 TL;DR for React Devs

> Maya is like React **without JSX**, **with native Web Components**, and **built-in dynamic routing + store + templates** — it’s React + Redux + Router + Deployment, all in one but simpler and more decoupled.

