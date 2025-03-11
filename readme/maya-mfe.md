**White Paper: Understanding Maya MFE – Structure, Views, State, and Lifecycle**

## **Introduction**
Maya is a **Micro Front-End (MFE) framework** designed to enable modular and scalable web applications. MFEs are the **building blocks** of Maya, allowing developers to break down complex applications into **independent, reusable components** that dynamically load into predefined slots.

This white paper covers:
1. **How MFEs are declared and structured**
2. **What are views, and how MFEs use them**
3. **State management and event handling using Maya.Store**
4. **Lifecycle events and their role in MFE execution**
5. **The importance of query parameters (`query`)**
6. **Using child MFEs to break down complex pages**
7. **Using `setDependency` to load related MFEs dynamically**

---
## **1. Declaring an MFE in Maya**
Each MFE in Maya is a **self-contained web component** that can be dynamically loaded into a slot. An MFE is declared as a **custom HTML tag** in Maya.

### **Example: Declaring an MFE in an HTML Page**
```html
<maya-maya ns="albert">
    <albert-header slot="header"></albert-header>
    <albert-dashboard slot="main"></albert-dashboard>
</maya-maya>
```
### **Key Components:**
- **Namespace (`ns`)** → Defines the application scope (e.g., `albert`).
- **Slots (`header`, `main`)** → Predefined areas where MFEs are loaded.
- **MFE Declaration (`<albert-header>`, `<albert-dashboard>`)** → MFEs dynamically load into the assigned slots.

---
## **2. Views in MFEs**
A **view** in an MFE represents the **UI structure** that gets rendered when the MFE loads. Views are defined as **Mustache templates** and are responsible for displaying data.

### **Example: MFE View Structure**
```html
<maya-panel>
    <maya-slot name="sub-content"></maya-slot>
</maya-panel>
```
The `sub-content` slot allows a child MFE to be dynamically loaded within the parent MFE.

### **How Views Work:**
1. When an MFE is loaded, it loads a **default view** (e.g., `main.html`).
2. Developers can **switch views dynamically** based on user interaction or state updates.
3. The view **fetches data and updates the UI using Maya.Store**.

### **Example: Loading a View Dynamically**
```javascript
Maya.Store.SetData({ store: 'userProfile', key: '12345', view: 'profile' })({ name: 'Alice', age: 30 });
```
This **re-renders the view** with new data without reloading the page.

---
## **3. State Management & Event Handling Using `Maya.Store`**
Every MFE in Maya has a **dedicated store** that manages **state and event handling**.

### **Example Store Declaration**
```javascript
Maya.Store.userProfile = {
    name: 'userProfile',
    data: {},
    events: {
        onLoad: async (options) => {
            let userData = await fetch(`/api/user/${options.key}`).then(res => res.json());
            Maya.Store.SetData({ store: 'userProfile', key: options.key })(userData);
        }
    }
};
```
### **How This Works:**
- **`data` stores the state** of the MFE.
- **`events` define event handlers**, such as `onLoad` for fetching data.
- **`SetData` updates the store and triggers a re-render.**

---
## **4. Lifecycle Events in an MFE**
Maya MFEs have **lifecycle events** that allow developers to hook into different stages of the MFE execution.

| **Lifecycle Event** | **Purpose** |
|----------------|-------------|
| `onLoad` | Runs when the MFE is loaded (good for fetching data) |
| `onRender` | Runs after the view is rendered (good for UI updates) |
| `onQuery` | Handles query parameter changes dynamically |
| `onMessage` | Listens for inter-MFE communication via Pub/Sub |
| `onUnload` | Runs when the MFE is removed from the DOM |

### **Example: Using `onQuery` to Handle Dynamic Changes**
```javascript
Maya.Store.userProfile.events.onQuery = async (query) => {
    let userData = await fetch(`/api/user/${query.userId}`).then(res => res.json());
    Maya.Store.SetData({ store: 'userProfile', key: query.userId })(userData);
};
```
✅ **This allows an MFE to dynamically update its view based on URL parameters.**

---
## **5. Importance of Query Parameters (`query`)**
Query parameters in Maya **allow MFEs to receive dynamic input** and update their content accordingly.

### **How Query Parameters Are Used**
```plaintext
#user-profile/main?userId=12345
```
This passes `userId=12345` to the `main` view of `user-profile`.

### **Example: Using Query Parameters in an MFE**
```javascript
Maya.Route('user-profile/main?userId=12345');
```
✅ **This allows dynamic page rendering without refreshing the entire application.**

---
## **6. Using Child MFEs to Break Down Complex Pages**
A **complex page** should be **broken down into multiple child MFEs** to improve performance and maintainability.

### **Example: Structuring a Dashboard with Child MFEs**
```html
<maya-panel>
    <maya-slot name="dashboard-metrics"></maya-slot>
    <maya-slot name="dashboard-reports"></maya-slot>
</maya-panel>
```
Each child MFE **renders separately**, ensuring that updates only affect **specific sections** rather than the entire page.

### **Benefits:**
✅ **Improves performance** by only re-rendering necessary components.
✅ **Encourages modular design** for easy maintenance.
✅ **Enables independent updates** without affecting the entire UI.

---
## **7. Using `setDependency` to Load Other MFEs Dynamically**
MFEs **often depend on other MFEs** for additional functionality. Maya allows MFEs to declare dependencies dynamically.

### **Example: Declaring Dependencies**
```javascript
class DashboardMFE extends MayaMFE {
    constructor() {
        super();
        this.setDependency(['reports', 'metrics']);
    }
}
```
✅ **This ensures that `reports` and `metrics` MFEs are loaded before `dashboard` initializes.**

---
## **Conclusion**
Maya’s MFE architecture enables **scalable, modular, and dynamic web applications**.

### **Key Takeaways:**
✅ **MFEs are self-contained web components declared dynamically.**
✅ **Each MFE has views, which update automatically based on store changes.**
✅ **State and event handling are managed via `Maya.Store`.**
✅ **Lifecycle events (`onLoad`, `onQuery`, etc.) enable dynamic interactions.**
✅ **Query parameters (`query`) allow MFEs to fetch and display relevant data dynamically.**
✅ **Child MFEs break down complex pages into manageable components.**
✅ **Dependencies can be declared using `setDependency` to ensure required MFEs load together.**

By following these principles, developers can build **efficient, scalable, and maintainable Maya applications.** 🚀

