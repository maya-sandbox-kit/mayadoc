**White Paper: Deep Dive into Maya State Management**

## **Introduction**
Maya is a **Micro Front-End (MFE) framework** that makes applications **modular, scalable, and efficient**. One of the key reasons Maya is so powerful is **how it manages state**—which means how it handles and stores data.

Unlike traditional applications where state is often tightly linked to a component, Maya **separates state from components**. This means:
- **Data can exist beyond the life of an MFE** (so when an MFE is removed, its data is not lost).
- **Components can share state easily**, especially in a parent-child relationship.
- **Re-rendering happens only when needed**, improving performance.

Maya’s state management revolves around **three types of state**, each with a different purpose:
1. **Data (`SetData`) – For Storing & Rendering Data**
2. **Component State (`SetState`) – For Internal Component Logic**
3. **Shared Data (`SetSharedData`) – For Caching & Cross-MFE Communication**

Let’s break each of these down in **simple terms**.

---
## **1. Data (`SetData`) – The Main Source of Truth**
This is the **primary way** MFEs store data in Maya. **When you use `SetData`, the MFE re-renders automatically.**

### **How It Works:**
- Every MFE has a **store** (`Maya.Store.<mfe>.data`).
- Calling `SetData` **saves new data** and **triggers a re-render of the MFE view**.
- You can **specify which view to render**, or it will **default to the last loaded view**.

### **Example: Updating User Profile Data**
```javascript
Maya.Store.SetData({ store: 'userProfile', key: '12345', view: 'profile' })({ name: 'Alice', age: 30 });
```
✅ **This updates the store AND re-renders the view automatically.**

If no view is specified, it re-renders the **last used view**:
```javascript
Maya.Store.SetData({ store: 'userProfile', key: '12345' })({ name: 'Alice', age: 30 });
```

### **Why Use `SetData`?**
- **Ensures data is up-to-date.**
- **Triggers re-rendering**, so UI updates automatically.
- **Keeps data separate from the MFE**, meaning it can **persist** even if the MFE is removed and reloaded later.

---
## **2. Component State (`SetState`) – Internal Logic Without Re-Rendering**
Sometimes, you need to store **temporary information** that shouldn’t trigger a re-render. This is where `SetState` comes in.

### **How It Works:**
- `SetState` stores **component-specific** data.
- Unlike `SetData`, **it does NOT re-render the view**.
- Useful for **saving small bits of data like form inputs, toggles, or UI settings.**

### **Example: Saving a User’s Input Without Re-Rendering**
```javascript
Maya.Store.SetState({ store: 'searchBar', key: 'input' })({ query: 'Maya Framework' });
```
✅ **The search input is saved, but the view is NOT refreshed.**

If you later need to access the stored data:
```javascript
let searchData = Maya.Store.GetState({ store: 'searchBar', key: 'input' });
```

### **Why Use `SetState`?**
- **Avoids unnecessary re-renders**, improving performance.
- **Useful for temporary, UI-related data** (e.g., toggles, input fields).
- **Keeps logic within the component without affecting global state.**

---
## **3. Shared Data (`SetSharedData`) – Caching & Global State**
Some data needs to be **shared across multiple MFEs** or **persist even after refresh**. `SetSharedData` is used for **global storage and caching**.

### **How It Works:**
- Stores data **globally**, so all MFEs can access it.
- If `cache=true`, it saves the data to **local storage**, meaning it survives **page reloads**.
- Unlike `SetData`, this **does not trigger a re-render automatically**.

### **Example: Storing Theme Settings in Shared Data**
```javascript
Maya.Store.SetSharedData({ key: 'theme', value: 'dark', cache: true });
```
✅ **Now, all MFEs can access this theme setting, even after a refresh.**

To retrieve shared data later:
```javascript
let theme = Maya.Store.GetSharedData({ key: 'theme' });
```

### **Why Use `SetSharedData`?**
- **Persists important settings even after a page reload.**
- **Avoids redundant API calls by caching data locally.**
- **Useful for app-wide settings or frequently used data.**

---
## **Why Is State Separated from Components?**
Unlike traditional frameworks where state is tied to a component, **Maya separates state from MFEs**. Here’s why:

### ✅ **State Outlives the Component**
- If an MFE is removed and reloaded, its data **is still available**.
- This means **faster loading times** and **less unnecessary API calls**.

### ✅ **Easy Data Sharing Between Parent & Child MFEs**
- When child MFEs **inherit the parent key**, they can **retrieve the same data** without needing to fetch it again.
- Example: A **Task MFE** inside a **Project MFE** can use the same key as the project.

### ✅ **Better Performance by Avoiding Unnecessary Re-Renders**
- `SetData` triggers a re-render, but `SetState` and `SetSharedData` **do not**, reducing UI flickering.

---
## **Real-World Example: A Shopping Cart MFE**
Let’s put everything together:

### **Scenario:**
- A user adds items to their shopping cart.
- The cart is **displayed** (requires re-rendering).
- The cart **remembers UI preferences** (e.g., “expanded” or “collapsed”).
- The cart state **persists across page reloads**.

### **How Maya Handles This:**
1. **Storing Cart Items (`SetData`)** → Triggers UI updates
   ```javascript
   Maya.Store.SetData({ store: 'cart', key: 'user123' })({ items: [{ name: 'Laptop', price: 1000 }] });
   ```

2. **Storing UI Preferences (`SetState`)** → Does NOT trigger re-render
   ```javascript
   Maya.Store.SetState({ store: 'cartUI', key: 'panel' })({ expanded: true });
   ```

3. **Saving Cart Data Between Sessions (`SetSharedData`)** → Caches data
   ```javascript
   Maya.Store.SetSharedData({ key: 'cartItems', value: [{ name: 'Laptop', price: 1000 }], cache: true });
   ```

Now, if the page reloads:
- The cart items **persist** (`SetSharedData` cached them).
- The UI preferences **remain unchanged** (`SetState