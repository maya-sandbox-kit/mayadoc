**White Paper: MFE Loading in Maya – Static & Dynamic Methods**

## **Introduction**
Maya is a **Micro Front-End (MFE) framework** that enables flexible and modular application development. One of its key features is its **dynamic loading capability**, allowing MFEs to be loaded **statically (via declaration)** or **dynamically (via `Load`, `Route`, and `LoadAtPosition`)**.

This white paper explores:
1. **Static vs. Dynamic Loading of MFEs**
2. **Understanding `Load` vs. `Route` vs. `LoadAtPosition`**
3. **How `Maya.Route()` Works with Query Parameters**
4. **Menu and Submenu Dynamic Slots**
5. **Using Multi-Slot for Multiple MFE Instances**

---
## **1. Static vs. Dynamic Loading of MFEs**
### **Static Loading – Declared in HTML**
MFEs can be **statically declared in HTML**, meaning they are **always present in the UI** when the application loads.

### **Example: Static MFE Declaration**
```html
<maya-maya ns="albert">
    <albert-header slot="header"></albert-header>
    <albert-dashboard slot="main"></albert-dashboard>
</maya-maya>
```
### **Pros & Cons of Static Loading**
✅ **Ensures core components (e.g., headers, sidebars) are always present**.
✅ **Simplifies layout by predefining UI structure**.
❌ **Can lead to unnecessary initial load time if too many MFEs are included**.

### **Dynamic Loading – Loaded on Demand**
Instead of preloading all MFEs, Maya allows **dynamic loading** into slots **only when needed**, reducing initial load time and improving performance.

### **Example: Dynamically Loading an MFE**
```javascript
Maya.Load('dashboard?target=main');
```
This **injects the `dashboard` MFE** into the `main` slot **only when required**.

---
## **2. Understanding `Load` vs. `Route` vs. `LoadAtPosition`**
Maya provides **three methods** for dynamically loading MFEs:

### **1️⃣ `Maya.Load()` – Load an MFE Without Changing URL**
- **Used to dynamically insert an MFE into a slot**.
- **Does NOT update the browser URL**.

#### **Example: Load a User Profile MFE**
```javascript
Maya.Load('user-profile?target=right');
```
✅ **Best for:** Loading transient components like side panels, widgets, or modals.

---
### **2️⃣ `Maya.Route()` – Load an MFE & Update URL**
- Similar to `Maya.Load()`, but **changes the URL**, making it trackable.
- This is equivalent to using `#<mfe>/<view>?query=<data>`.
- Useful for routing changes, enabling deep linking and browser navigation.

#### **Example: Navigating to a Settings Page**
```javascript
Maya.Route('settings?target=main');
```
**Equivalent to:**
```plaintext
#settings/main?userId=12345
```
✅ **Best for:** Page-like transitions where the URL should reflect the loaded MFE.

---
### **3️⃣ `Maya.LoadAtPosition()` – Load MFEs at Specific Positions**
- Used to **position floating MFEs** dynamically.
- Can **bind MFEs to a clicked element’s position**.

#### **Example: Loading a Tooltip Next to a Clicked Button**
```javascript
Maya.LoadAtPosition({
    mfe: 'tooltip',
    target: 'menu',
    x: event.clientX,
    y: event.clientY,
    bound: true
});
```
- `bound: true` → The MFE aligns with the **clicked element’s bounding rectangle**.
- `bound: false` → The MFE appears at **the cursor position instead**.

✅ **Best for:** Context menus, tooltips, floating actions.

---
## **3. How `Maya.Route()` Works with Query Parameters**
Maya allows **query parameters** to be passed dynamically when loading an MFE. The loaded view **must process the query and render the view accordingly**, either by:
- **Calling an API to fetch relevant data**
- **Using local data or computation**

### **Example: Navigating with Query Parameters**
```javascript
Maya.Route('user-profile/main?userId=12345');
```
This passes `userId=12345` to the `main` view of `user-profile`.

### **How the MFE Handles the Query**
Inside the MFE, `onQuery` handles the incoming query:
```javascript
Maya.Store.userProfile.events.onQuery = async (query) => {
    let userId = query.userId;
    let userData = await fetch(`/api/user/${userId}`).then(res => res.json());
    
    Maya.Store.SetData({ store: 'userProfile', key: userId })(userData);
};
```
### **Best Practice for Query Processing**
✅ **Extract query parameters dynamically**
✅ **Use the parameters to fetch data or apply local computation**
✅ **Ensure the view updates correctly based on new data**

---
## **4. Menu & Submenu Dynamic Slots**
Maya provides `menu` and `submenu` as **floating slots**, allowing dynamic MFE loading inside them.

### **Example: Loading a Context Menu Dynamically**
```javascript
Maya.Load('context-menu?target=menu');
```

### **Example: Loading a Submenu Inside a Menu**
```javascript
Maya.Load('submenu-options?target=submenu');
```
✅ **Why This Matters:**
- Enables **context-aware menus** that **dynamically load based on user actions**.
- Avoids **preloading menus unnecessarily**, improving performance.

---
## **5. Using Multi-Slot for Multiple MFE Instances**
By default, a **slot can hold only one MFE at a time**. However, using `mu