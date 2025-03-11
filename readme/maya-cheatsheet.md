**Maya Developer Cheat Sheet**

## **1. Core Concepts**
- **Micro Front-End (MFE):** Modular web components with encapsulated functionality.
- **Slots:** Dynamic placeholders where MFEs load inside an application.
- **State Management:** `Maya.Store` manages MFE data and event handling.
- **Pub/Sub Communication:** MFEs communicate via `Maya.Store.Publish/Subscribe()`.
- **Dynamic Loading:** MFEs load/unload dynamically using `Maya.Load()`.
- **One-Way Data Binding:** Inputs automatically sync to `Maya.Store.<mfe>.self<key>`.
- **Namespace (`ns`):** Defines the unique scope of an MFE inside Maya.

---
## **2. Maya Directory Structure**
```
/mfe/<mfe-name>/
    /js/
        index.js       # Main logic, store, events, MFE declaration
    /view/
        main.html      # Main view
        detail.html    # Optional detail view
```
---
## **3. MFE Declaration**
### **Static Declaration**
```html
<maya-maya ns="albert">
    <albert-header slot="header"></albert-header>
    <albert-dashboard slot="main"></albert-dashboard>
</maya-maya>
```
### **Dynamic Loading**
```javascript
Maya.Load('dashboard?target=main');
Maya.Route('settings?target=main');
Maya.LoadAtPosition({ mfe: 'tooltip', target: 'menu', x: 100, y: 200, bound: true });
```

---
## **4. State Management with `Maya.Store`**
### **Setting Data**
```javascript
Maya.Store.SetData({ store: 'userProfile', key: '12345' })({ name: 'Alice', age: 30 });
```
### **Getting Data**
```javascript
let user = Maya.Store.GetData({ store: 'userProfile', key: '12345' });
```
### **Shared Data (Persistent Storage)**
```javascript
Maya.Store.SetSharedData({ key: 'theme', value: 'dark', cache: true });
```

---
## **5. Lifecycle Events**
| **Event**   | **Purpose** |
|------------|-------------|
| `onLoad`   | Runs when the MFE is loaded, useful for fetching data. |
| `onRender` | Runs after the view is rendered. |
| `onQuery`  | Handles query parameter changes dynamically. |
| `onMessage`| Handles Pub/Sub messages. |
| `onUnload` | Runs when the MFE is removed. |

Example:
```javascript
Maya.Store.userProfile.events.onLoad = async (options) => {
    let userData = await fetch(`/api/user/${options.key}`).then(res => res.json());
    Maya.Store.SetData({ store: 'userProfile', key: options.key })(userData);
};
```

---
## **6. Event Handling & Pub/Sub**
### **Publishing an Event**
```javascript
Maya.Store.Publish({ topic: 'user-updated' })({ userId: '123' });
```
### **Subscribing to an Event**
```javascript
Maya.Store.Subscribe({ topic: 'user-updated' })(this);
```
### **Handling Click Events**
```html
<maya-button store="userProfile" operationId="saveUser"></maya-button>
```
```javascript
Maya.Store.userProfile.events.saveUser = async (event) => {
    let email = Maya.Store.userProfile.self[event.key].email.trim();
    await fetch('/api/saveUser', { method: 'POST', body: JSON.stringify({ email }) });
};
```

---
## **7. Mustache Templates vs. JSX / Angular Templates**
| **Feature** | **Maya (Mustache Templates)** | **React (JSX)** | **Angular Templates** |
|------------|----------------|-------------|----------------|
| **Syntax Complexity** | Simple, logic-less | Requires JavaScript expressions | Uses Angular-specific syntax |
| **Learning Curve** | Easy to learn | Requires understanding JSX and component lifecycle | Complex (Directives, Pipes, Two-way binding) |
| **Logic Placement** | Only for templating, separate logic in JS | Mixing UI with JS logic | Templating mixed with TypeScript |
| **Performance** | Lightweight, pre-compiled | Uses Virtual DOM, additional reconciliation | Heavy runtime processing |

✅ **Why Mustache Templates?**
- **Logic-less approach** keeps templates clean and readable.
- **No need to learn JSX or Angular-specific syntax**.
- **Pre-compiled for efficiency**, reducing runtime processing.
- **No unnecessary re-rendering** like React’s Virtual DOM.

---
## **8. Multi-Instance & Multi-Slot Support**
### **Allowing Multiple MFEs in a Slot**
```html
<maya-slot name="notifications" multi="true"></maya-slot>
```
```javascript
Maya.Load('new-notification?target=notifications');
```
### **Removing a Specific MFE from Multi-Slot**
```javascript
Maya.Close({ target: 'notifications', key: 'notif-123' });
```

---
## **9. Slot System & UI Composition**
| **Slot Name** | **Purpose** |
|-------------|-------------|
| `main` | Primary content area. |
| `header` | Navigation/header. |
| `left` | Sidebar content. |
| `right` | Context panels. |
| `overlay` | Modals or pop-ups. |
| `menu` | Floating menus. |
| `submenu` | Nested menus. |

Example:
```html
<maya-panel>
    <maya-slot name="dashboard-metrics"></maya-slot>
    <maya-slot name="dashboard-reports"></maya-slot>
</maya-panel>
```

---
## **10. Deployment & Versioning**
### **Tracking MFE Versions**
Each MFE has its own version:
```json
{
    "mfe-name": "commit-id"
}
```
### **Independent Deployments**
MFEs can be deployed independently without affecting others.

✅ **Why Maya?**
- Scalable **Micro Front-End** architecture.
- **Dynamic slot-based** UI composition.
- **Event-driven Pub/Sub** communication.
- **Simplified state management** with `Maya.Store`.
- **Lightweight Mustache templating** instead of JSX or Angular syntax.
- **Independent deployment & versioning** for large teams.

🚀 **Maya enables modular, efficient, and scalable front-end development!**

