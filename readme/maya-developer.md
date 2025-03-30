# **Maya Developer Guide**

## **1. Overview**
Maya is a **Micro Front-End (MFE)** framework designed to create modular, scalable, and dynamic web applications by breaking the UI into **self-contained, independently deployable MFEs**. Each MFE is a web component with its **own Shadow DOM**, ensuring encapsulation.

## **2. Anatomy of an MFE**
A typical **Maya MFE** follows this structure:

```
/mfe/<mfe-name>/
  /js/
    index.js       # Main logic, store, events, MFE declaration
  /view/
    main.html      # Main view
    detail.html    # Optional detail view
```

### **Naming Conventions**
- **MFE Name:** All lowercase, no spaces, or special characters.
- **Store Name:** Same as the MFE name.
- **Child MFE Name:** Prefix with parent MFE name.

---

## **3. Core Lifecycle Hooks**
Each **MFE** goes through the following lifecycle methods:

| **Hook**       | **Purpose** |
|---------------|------------|
| `onLoad`     | Called when the MFE is loaded. Used for initializing data and subscriptions. |
| `onRender`   | Called after view is rendered. Ideal for post-render actions like loading child MFEs. |
| `onMessage`  | Triggered when an MFE receives a message from another MFE. |
| `onQuery`    | Handles changes in query parameters without unloading/reloading the MFE. |
| `onUnload`   | Called when the MFE is disconnected. Used for cleanup actions like unsubscribing events. |

### **Sample MFE Declaration**
```javascript
class InventoryMFE extends MayaMFE {
    constructor() {
        super();
        this.setStore(Maya.Store.inventory);
        this.setView('main');
    }

    onLoad = async (options) => {
        this.setView(options.view || 'main');
        return Maya.Store.inventory.events.onLoad(options);
    };
}
window.customElements.define('albert-inventory', InventoryMFE);
```

---

## **4. Dynamic MFE Loading**
There are **two ways** to load MFEs dynamically:

### **Static Slot Declaration (Hardcoded)**
```html
<albert-inventory slot="main"></albert-inventory>
```

### **Dynamic MFE Loading (Via Code)**
```javascript
Maya.Route('inventory[/main | detail]?target=main'); // Changes URL
Maya.Load('inventory[/main | detail]?target=main');  // Loads without changing URL
```

---

## **5. State Management**
### **Setting Data in Store**
```javascript
Maya.Store.SetData({ store: 'inventory', key: 'item-list' })(data);
```

### **Setting Shared Data (Accessible by All MFEs)**
```javascript
Maya.Store.SetSharedData({ key: 'global-state', value: someValue });
```

### **Event Declaration in a Store**
```javascript
Maya.Store.inventory = {
    name: 'inventory',
    data: {},
    events: {
        onLoad: async (options) => { /* Fetch data and update store */ },
        onDelete: async (id) => { /* Delete an item from inventory */ }
    }
};
```

---

## **6. Communication Between MFEs**
Maya provides **Publish/Subscribe** and **Shared Data** for inter-MFE communication.

### **Using Pub/Sub for Messaging**
```javascript
Maya.Store.Publish({ topic: 'inventory-updated' })({ msg: 'New item added' });

Maya.Store.Subscribe({ topic: 'inventory-updated' })(this);

onMessage = (option) => async (msg) => { 
    console.log('Received:', msg);
};
```

---

## **7. Sample MFE Implementation**
### **index.js (Defining the MFE and Store)**
```javascript
Maya.Store.inventory = {
    name: 'inventory',
    data: {},
    events: {
        onLoad: async (options) => {
            if (options.view === 'detail') {
                const data = await Maya.API.getInventoryById({ id: options.query });
                Maya.Store.SetData({ store: 'inventory', key: options.key })(data);
            } else {
                const data = await Maya.API.searchInventories({ limit: 50 });
                Maya.Store.SetData({ store: 'inventory', key: options.key })(data);
            }
        }
    }
};

class InventoryMFE extends MayaMFE {
    constructor() {
        super();
        this.setStore(Maya.Store.inventory);
    }

    onLoad = async (options) => {
        this.setView(options.view || 'main');
        return Maya.Store.inventory.events.onLoad(options);
    }
}
window.customElements.define('albert-inventory', InventoryMFE);
```

### **main.html (MFE View Using Mustache Templates)**
```html
<h1>{{maya.title}}</h1>
<table class="inventory-table">
    <thead>
        <tr>
            <th>Name</th>
            <th>Quantity</th>
        </tr>
    </thead>
    <tbody>
        {{#Items}}
        <tr>
            <td>{{name}}</td>
            <td>{{quantity}}</td>
            <td><a href="#/inventory/detail?id={{id}}">View Details</a></td>
        </tr>
        {{/Items}}
    </tbody>
</table>
```

---

## **8. Deploying Maya MFEs**
Each **MFE** is **independently deployed** via **CDN**.

### **Version Tracking (version.json)**
Every deployment updates `version.json` to keep track of the latest MFE versions.

```json
{
    "mfe-inventory": "3caf35a0afba26559785ccad12c77213832fd560",
    "mfe-login": "54f2d64a1a5ef1179c72077c880581c9a54ba2c8"
}
```

### **Dynamically Fetching MFE Version**
```javascript
let mayaversion = JSON.parse(localStorage.getItem('mayaversion') || "{}");

HttpGET()('/version.json').then(res => { 
    mayaversion = res.data; 
    localStorage.setItem('mayaversion', JSON.stringify(mayaversion));
}).catch(e => {});
```

---

## **9. Maya Components (UI Elements)**
Maya comes with **pre-built components** that handle UI interactions.

| **Component** | **Description** | **Usage** |
|--------------|---------------|---------|
| `maya-button` | Handles clicks | `<maya-button store="inventory" operationId="addItem"></maya-button>` |
| `maya-input`  | Handles input changes | `<maya-input store="inventory" name="itemName"></maya-input>` |
| `maya-list`  | Displays list | `<maya-list view="list-view" store="inventory"></maya-list>` |
| `maya-form`  | Handles form submission | `<maya-form store="inventory" operationId="submitForm"></maya-form>` |

---

## **10. Best Practices**
✅ **Use Slots for MFE Nesting** – Keeps MFEs modular.  
✅ **onQuery for Dynamic Content Updates** – Avoids unnecessary reloads.  
✅ **Use Mustache Templates** – Separates logic and presentation.  
✅ **Ensure Event Handlers Process Data Before Storing** – Avoids raw API responses in the UI.  

---

## **11. Debugging Maya Applications**
1. **Inspect the Store Data**
   ```javascript
   console.log(Maya.Store.inventory);
   ```
2. **Track MFE Events**
   ```javascript
   console.log('Dispatching event:', event);
   ```
3. **Ensure Proper Dependencies Are Loaded**
   ```javascript
   console.log(document.getElementById('maya-inventory'));
   ```

---

This guide provides a **solid foundation** for developing, managing, and deploying **Maya MFEs**. 🚀 Happy Coding!