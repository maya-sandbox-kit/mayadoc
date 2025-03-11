**White Paper: Maya Naming Conventions & Directory Structure**

## **Introduction**
Maya is a **Micro Front-End (MFE) framework** that provides a structured approach to building modular applications. To maintain **consistency, scalability, and maintainability**, Maya enforces strict **naming conventions** for **MFE names, store names, directory structures, and event communication (PubSub topics).**

This white paper outlines:
1. **MFE Naming Conventions**
2. **Directory Structure & Organization**
3. **Store Name Conventions**
4. **PubSub Topic Naming Rules**

---
## **1. MFE Naming Conventions**
Each MFE in Maya follows a **consistent naming pattern** to ensure **clarity and uniqueness** within a namespace.

### **MFE Naming Rule:**
```plaintext
<store name>
```
- **`store name`** → Represents the functionality of the MFE (e.g., `dashboard`, `profile`, `tasks`).
- **MFE names are always **lowercase** and **contain no spaces or special characters**.
- **The namespace (`ns`) is used internally by Maya** to declare and load web components, allowing multiple MFEs from different namespaces to coexist without conflict.

### **Example MFE Names:**
| MFE Name | Purpose |
|-------------|-------------|
| `dashboard` | Dashboard MFE |
| `reports` | Sales reporting module |
| `tracker` | Inventory tracking system |

This ensures that **all MFEs remain unique** and **easy to locate within their directory**.

---
## **2. Directory Structure & Organization**
A Maya app follows a structured directory format to keep **MFEs, views, and assets organized.**

### **Standard MFE Directory Structure:**
```plaintext
/mfe/<store-name>/
    /js/
        index.js       # Main MFE logic, store, and event handlers
    /view/
        main.html      # Primary view file for the MFE
        detail.html    # Optional detailed view
```

### **Example Directory for `dashboard` MFE:**
```plaintext
/mfe/dashboard/
    /js/
        index.js       # Contains MFE store, events, and component logic
    /view/
        main.html      # Main dashboard view
        detail.html    # Detailed view (optional)
```
This organization ensures:
✅ **Easy maintainability** by keeping logic and views separate.
✅ **Consistency across different MFEs**, making onboarding easier.
✅ **Seamless integration with the Maya framework.**

---
## **3. Store Name Conventions**
Each MFE has its own **dedicated store** in `Maya.Store`, ensuring **data encapsulation and isolation**.

### **Store Naming Rule:**
```plaintext
<store name> = <mfe-name>
```
- **Store names are always lowercase**.
- **Must match the MFE name** for consistency.

### **Example Store Declarations:**
```javascript
Maya.Store.dashboard = {
    name: 'dashboard',
    data: {},
    events: {
        onLoad: async (options) => {
            // Fetch data and initialize
        }
    }
};
```

✅ **Why Match Store Name with MFE Name?**
- Ensures **consistency** across all MFEs.
- Makes it **easier to locate data** for debugging.
- Avoids **conflicts between different MFEs**.

---
## **4. PubSub Topic Naming Rules**
Maya uses **Publish-Subscribe (PubSub) mechanisms** for event-driven communication between MFEs. To avoid conflicts, PubSub topics follow a **clear naming structure**.

### **PubSub Topic Naming Rule:**
```plaintext
<store-name>.<event>
```
- **`store-name`** → The MFE store name (e.g., `dashboard`, `inventory`).
- **`event`** → The event name that is being published or subscribed to.

### **Example PubSub Topics:**
| Topic Name | Purpose |
|-------------|-------------|
| `dashboard.refresh` | Refreshes the dashboard |
| `reports.export` | Triggers an export event in reports |
| `tracker.low-stock` | Notifies when stock is low |

### **Example PubSub Implementation:**
#### **Publishing an Event:**
```javascript
Maya.Store.Publish({ topic: 'dashboard.refresh' })({ userId: '12345' });
```
#### **Subscribing to an Event:**
```javascript
Maya.Store.Subscribe({ topic: 'dashboard.refresh' })(this);
```
✅ **Why Use This Naming Pattern?**
- Prevents **conflicts** between different MFEs.
- Keeps event handling **organized and structured**.
- Ensures that MFEs **only listen to relevant topics**.

---
## **5. Best Practices for Naming & Structure**
### ✅ **DO:**
✔ Use **lowercase names** for MFEs, stores, and PubSub topics.
✔ Keep MFE names **unique**.
✔ Structure directories **properly** to separate logic (`js/`) and views (`view/`).
✔ Use **clear and structured PubSub topic names** to prevent conflicts.

### ❌ **DON'T:**
✖ Use spaces or special characters in names (e.g., `inventory Tracker`).
✖ Create overly generic MFE names (`dashboard` instead of `sales-dashboard`).
✖ Store everything globally—use **separate stores for each MFE**.
✖ Hardcode event names without **store prefixes** (e.g., just `refresh`).

---
## **Conclusion**
Following these naming conventions ensures that Maya applications **remain modular, scalable, and easy to maintain**.

### **Key Takeaways:**
✅ **MFE names follow the `<store name>` format.**
✅ **Store names must match MFE names** and be lowercase.
✅ **Directory structures keep logic (`js/`) and views (`view/`) separate.**
✅ **PubSub topics use `<store-name>.<event>` for clear event management.**
✅ **Proper naming avoids conflicts, improves debugging, and enhances scalability.**

By adhering to these best practices, developers can build **efficient, well-structured, and maintainable Maya applications**. 🚀