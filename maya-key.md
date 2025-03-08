**White Paper: Leveraging the Maya Component/MFE Key Attribute for Instance-Specific Data Management**

## Introduction
The Maya Micro Front-End (MFE) architecture is designed for modularity, scalability, and independence. One of the most critical aspects of this architecture is the **key attribute**, which enables multiple instances of an MFE to operate concurrently without data conflicts. This white paper explores the **Maya key attribute**, its role in **state management**, its importance in maintaining instance-specific data, and how it facilitates child MFEs to inherit and cache parent data efficiently.

## The Role of the Key Attribute in MFE Instance Management
Every MFE in Maya operates as an independent, encapsulated component with its own state and lifecycle. However, multiple instances of the same MFE may exist simultaneously within an application. To prevent data corruption and ensure each instance maintains its own state, Maya assigns a **unique key** to each instance.

### Key Features of the Key Attribute:
1. **Instance-Specific Data Storage:**
   - `Maya.Store.<mfe>.data.<key>` ensures that each MFE instance stores data under its unique key.
   - Prevents one instance from overwriting another’s data.

2. **Lifecycle Events and Key Propagation:**
   - Every action and lifecycle event (`onLoad`, `onRender`, `onMessage`, `onQuery`, `onUnload`) always includes the MFE key.
   - Ensures that operations are executed only on the relevant instance.

3. **Cross-Instance Isolation:**
   - Since each MFE instance has a distinct key, data leaks between instances are prevented.
   - MFEs can be dynamically instantiated multiple times without conflict.

## MFE Declaration and Key Assignment in the Browser
When an MFE is initiated in the browser, it is typically declared as a **custom web component** using the `<maya-input>` or other Maya component tags. Below is an example of an MFE declaration:

```html
<maya-input slot="body" name="select" placeholder="Search..." store="smartsearch" operationid="onSearch" key="1741386681020.856" value="" entity="project" view="input">
    <input>
</maya-input>
```

### How the Key is Assigned:
1. **Key Generation:**
   - When the MFE is instantiated, it checks for a `key` attribute.
   - If not explicitly set, it is auto-generated based on `Date.now() + Math.random()` to ensure uniqueness.
   - If the MFE is nested inside a parent, it may **inherit the parent's key**.

2. **MFE Instantiation in JavaScript:**
   - Each MFE extends `MayaMFE`, a base class that manages its lifecycle.
   - The `connectedCallback` method assigns the key and ensures state isolation.

```javascript
class MayaInput extends MayaMFE {
    constructor() {
        super();
        this.setView('input');
    }
    onLoad = async (options) => {
        this.setKey(options.key || Date.now() + Math.random());
        return Maya.Store.smartsearch.events.onLoad(options);
    };
}
window.customElements.define('maya-input', MayaInput);
```

3. **Key Handling in Lifecycle Methods:**
   - The key is always passed along with events.
   - Example of setting data instance-specifically:

```javascript
Maya.Store.SetData({ store: 'smartsearch', key: options.key })(data);
```

## Store Management as a Prototype Extension
Each MFE **inherits from `MayaMFE`**, providing core lifecycle methods. However, the **store itself is an extension of the `Maya.Store` prototype**. This ensures that:
- **State and event handlers** are encapsulated per MFE.
- **Manipulators such as `SetData` and `Publish`** handle state updates and event-based communication.
- **Multiple MFEs do not interfere** with each other's data as they are stored under distinct keys.

### Example of a Store Declaration
```javascript
Maya.Store.smartsearch = {
    name: 'smartsearch',
    data: {},
    events: {
        onSearch: async (ev) => {
            let results = await fetchResults(ev.query);
            Maya.Store.SetData({ store: 'smartsearch', key: ev.key })(results);
        }
    }
};
```

## Slot-Based MFE Management
Maya employs **slots** to dynamically position MFEs within the application. However, unless explicitly configured for **multi-instance mode**, a slot can **hold only one MFE at a time**. 

### Slot Behavior:
1. **Default Single-MFE Mode:**
   - When a new MFE is loaded into a slot, the existing MFE is **removed and replaced**.
   - Example:

```javascript
Maya.Load('search-mfe?target=body');
```

2. **Multi-Instance Mode (Optional):**
   - To allow multiple MFEs in the same slot, the `multi` attribute must be explicitly set.

```html
<maya-slot name="body" multi="true"></maya-slot>
```

## Child MFE Data Inheritance and Caching
### Key Inheritance in Child MFEs
A major advantage of the **key attribute** is its inheritance mechanism. When a child MFE is instantiated within a parent MFE, it automatically inherits the parent’s key unless explicitly overridden. This has several benefits:

- **Shared State Between Parent and Child MFEs:**
  - Since the child MFE stores its data under the same parent key inside the child MFE store, all related data remains interconnected.
  - The parent and child can seamlessly interact without redundant state management.

- **Efficient Data Retrieval and Persistence:**
  - When a child MFE is reloaded, it continues using the same parent key.
  - Cached data under the inherited key allows child MFEs to retain state across reloads, reducing API calls and improving performance.

### Example Implementation
```javascript
class ParentMFE extends MayaMFE {
    constructor() {
        super();
        this.setStore(Maya.Store.parentmfe);
    }

    onLoad = async (options) => {
        this.setView(options.view || 'main');
        return Maya.Store.parentmfe.events.onLoad(options);
    };
}
window.customElements.define('parent-mfe', ParentMFE);

class ChildMFE extends MayaMFE {
    constructor() {
        super();
        this.setParent('parentmfe'); // Inheriting parent key
        this.setStore(Maya.Store.childmfe);
    }

    onLoad = async (options) => {
        this.setView(options.view || 'main');
        return Maya.Store.childmfe.events.onLoad(options);
    };
}
window.customElements.define('child-mfe', ChildMFE);
```

### Expected Behavior:
- When `parent-mfe` loads, it assigns a unique key.
- When `child-mfe` loads within it, it inherits the parent’s key.
- The child’s state is stored under `Maya.Store.childmfe.data.<parent-key>`.
- If reloaded, it retrieves data from the same parent-keyed state, allowing for caching and persistence.

## Conclusion
The **key attribute** in Maya MFEs is a fundamental mechanism for ensuring **instance-specific state management, efficient child MFE inheritance, and optimized caching mechanisms**. By leveraging this approach, developers can build **scalable, high-performance, and modular** micro front-ends without compromising on state integrity or performance efficiency.

