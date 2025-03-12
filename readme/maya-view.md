### **How Maya Enables True Front-End Rendering Separation Using Views and Data**

Maya's architecture is built around **separating data from the view**, which allows dynamic front-end rendering without modifying the underlying logic. This is achieved through:
1. **Decoupling Views and Logic** - UI templates (`.html`) and logic (`index.js`) are separate.
2. **Centralized State Management** - The store (`Maya.Store`) manages data independently.
3. **Dynamic Rendering** - Data updates trigger view re-renders without modifying the view files.
4. **Slot-Based Component Loading** - UI components (`maya-*`) are dynamically placed and updated.

---

## **1. Separation of Views and Logic in Maya**
Unlike traditional front-end frameworks where UI logic and HTML are tightly coupled, Maya enforces a strict separation:

| **Aspect**      | **Handled By** |
|---------------|--------------|
| **View (UI Layout)** | Mustache templates (`.html`) |
| **Logic (Event Handling, Data Fetching, State Updates)** | JavaScript (`index.js`) |
| **State Management** | `Maya.Store` (Handles API calls, stores data) |

This separation allows **UI changes** to be made independently of logic updates.

### **Example: List and ListItem Components**
In a **traditional framework**, UI updates may require changing **both the JavaScript logic and HTML**.  
In **Maya**, we can change the UI by modifying only the `.html` view while keeping the logic intact.

---

## **2. Centralized Store for State Management**
Maya uses **`Maya.Store`** to manage state and API calls separately from UI rendering.

### **How `Maya.Store` Separates Data and View**
- **Data is updated in `Maya.Store`** using `Maya.Store.SetData`.
- **View (`.html`) dynamically pulls data** from `Maya.Store`.
- **View does not store its own state**—it only reflects data from the store.

### **Example: Updating List Data Without Changing the View**
#### **1️⃣ Initial Data in `Maya.Store`**
```javascript
Maya.Store.SetData({ store: 'example', key: 'myList' })({
    items: ['item1', 'item2', 'item3'],
    item1: { self: 'Item One' },
    item2: { self: 'Item Two' },
    item3: { self: 'Item Three' }
});
```

#### **2️⃣ `list.html` (No JavaScript Changes Needed)**
```html
<h2>{{name}} List</h2>
<ul>
    {{#items}}
    <maya-listitem store="{{store}}" key="{{../key}}" name="{{.}}"></maya-listitem>
    {{/items}}
</ul>
```

#### **3️⃣ View Updates Automatically When Data Changes**
```javascript
// Updating the list dynamically
Maya.Store.SetData({ store: 'example', key: 'myList' })({
    items: ['itemA', 'itemB'],
    itemA: { self: 'New Item A' },
    itemB: { self: 'New Item B' }
});
```
✅ **No need to modify `list.html`**—the UI updates automatically.

---

## **3. Dynamic Rendering Without Hardcoding**
Maya allows dynamic UI updates based on the **data structure**, ensuring **full separation**.

### **Example: Swapping Views Without Modifying Logic**
Let's say we want to **switch from a list view to a table view** without changing the logic.

#### **Option 1: Default List View (`list.html`)**
```html
<ul>
    {{#items}}
    <maya-listitem store="{{store}}" key="{{../key}}" name="{{.}}"></maya-listitem>
    {{/items}}
</ul>
```

#### **Option 2: Switch to Table View (`table.html`)**
```html
<table>
    <tr>
        <th>Item</th>
    </tr>
    {{#items}}
    <tr>
        <td><maya-listitem store="{{store}}" key="{{../key}}" name="{{.}}"></maya-listitem></td>
    </tr>
    {{/items}}
</table>
```
🚀 **No JavaScript changes needed!** Just change the view file.

#### **Dynamic View Switching Using `Maya.Store`**
```javascript
// Change the view dynamically
document.querySelector('maya-list').setAttribute('view', 'table');
```
This **instantly switches the UI to a table format**, without altering the logic.

---

## **4. Slot-Based Component Loading for Modular UI**
Maya uses **slots** (`maya-slot`) to dynamically inject components.

### **Example: Dynamically Load a New Component**
```html
<maya-slot name="main"></maya-slot>
```
```javascript
Maya.Load('inventory/main?target=main');
```
🔹 This replaces the **current component in `main`** without reloading the page.

---

## **5. Real-World Benefits of Maya's View-Data Separation**
### ✅ **1. Faster UI Development**
- Designers can update `list.html` without modifying JavaScript.
- Developers only focus on **data transformations** in `Maya.Store`.

### ✅ **2. Instant UI Changes Without Logic Updates**
- UI structure can change dynamically (e.g., List → Table) by modifying the view file.
- No need to modify event handlers or recompile JavaScript.

### ✅ **3. Performance Optimization**
- **Only data changes trigger UI updates**, avoiding unnecessary re-renders.
- Views are **lazy-loaded** (only when needed), reducing initial load time.

### ✅ **4. Extensibility**
- **New themes/layouts** can be introduced **without breaking existing functionality**.
- **Multiple views** can use the same store logic without duplicating code.

---

## **6. Summary**
| **Feature** | **Maya’s Approach** |
|------------|------------------|
| **Data Management** | Centralized in `Maya.Store` |
| **View Management** | Mustache Templates (`.html`) |
| **Rendering** | Fully dynamic, data-driven |
| **Component Communication** | Uses `Maya.Store.Publish` and `Maya.Store.Subscribe` |
| **UI Customization** | Modify `.html` files without changing JavaScript |

🚀 **Final Takeaway:**  
Maya **completely separates UI rendering from data** using **Mustache templates, a centralized store, and dynamic components**, making UI development **faster, scalable, and maintainable**.  

Would you like to see an advanced example (e.g., real-time updates with `onMessage`)? 😊