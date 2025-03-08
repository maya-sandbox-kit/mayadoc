**White Paper: Core Concepts of Maya – A Modular and Dynamic MFE Framework**

## **Introduction**
Maya is a **Micro Front-End (MFE) framework** designed to create **modular, scalable, and dynamic** web applications. The name **Maya** comes from **Vedic philosophy**, where Maya represents the **illusion of reality**, suggesting that what we see is just a **projection of a deeper, complex system**. Similarly, the Maya framework enables web applications to **appear as a single unit** while actually being made up of many independent, self-contained components.

Maya is built on the principles of **slots, dynamic loading, fractal MFEs, multi-instance support, and state management via a centralized store**. This document breaks down these core concepts in **simple, easy-to-understand terms**.

---
## **Micro Front-Ends (MFEs): The Building Blocks**
At its heart, Maya applications are built using **MFEs**. Think of MFEs as **small, self-contained mini-apps** that can be loaded into a larger application **independently**.

### **Why MFEs?**
- Each MFE **owns its own functionality** and can be **developed, deployed, and updated separately**.
- Multiple MFEs can work together to form a **fully functional web application**.
- MFEs prevent **code conflicts** by encapsulating their own styles and logic.

**Example:** A web dashboard may have **separate MFEs** for:
- User profile
- Notifications
- Reports
- Task management

These MFEs **load dynamically** and do not interfere with each other.

---
## **Slot-Based Dynamic Loading**
Instead of hardcoding components, Maya uses **slots** as placeholders where MFEs can be dynamically loaded. **Think of slots as empty containers** that hold MFEs as needed.

### **Key Features of Slots:**
- **Predefined slots** (`main`, `header`, `left`, `right`, `center`, `overlay`, `menu`, `submenu`).
- **Custom slots** can be created inside MFEs.
- MFEs are dynamically loaded into slots using `Maya.Load()`.
- **Floating slots** (`menu`, `submenu`) allow MFEs to appear as dropdowns or context menus.
- **Positioned slots** (`Maya.LoadAtPosition()`) let MFEs appear where the user clicks.

### **Example:**
```html
<maya-slot name="main"></maya-slot>
```
Now an MFE can be loaded dynamically:
```javascript
Maya.Load('user-profile?target=main');
```
This loads the `user-profile` MFE inside the `main` slot.

---
## **Multi-Instance MFEs**
By default, a slot **holds only one MFE at a time**. However, Maya allows slots to **support multiple MFEs simultaneously** by setting `multi=true`.

### **Why Multi-Instance Support?**
- **Allows multiple components of the same type** to be active at once.
- Useful for features like **multiple chat windows, notifications, or search results**.

### **Example:**
```html
<maya-slot name="notifications" multi="true"></maya-slot>
```
Now multiple notification MFEs can load into this slot.
```javascript
Maya.Load('new-notification?target=notifications');
```
To remove a specific MFE from a multi-slot:
```javascript
Maya.Close({ target: 'notifications', key: '12345' });
```

---
## **Fractal Child MFEs: Infinite Expansion**
One of Maya’s unique aspects is **Fractal MFEs**—MFEs can contain **other MFEs** inside them. This allows **nested UI structures** that expand dynamically.

### **How It Works:**
- **Parent MFEs** can define slots.
- **Child MFEs** can load into those slots.
- This process can continue infinitely, creating a **self-replicating UI** structure.

### **Example:**
```html
<maya-panel>
    <maya-slot name="sub-panel"></maya-slot>
</maya-panel>
```
Now, an MFE can be loaded inside this `sub-panel` slot, which itself can contain **more slots for additional MFEs**.

---
## **State Management via a Singleton Store**
Maya manages state using **a single shared store** (`Maya.Store`). Each MFE has its **own namespace within this store**.

### **How the Store Works:**
- Data is stored in `Maya.Store.<mfe>.data`.
- All **event handlers are grouped under** `Maya.Store.<mfe>.events`.
- **Global shared data** is managed via `Maya.Store.SetSharedData()`.
- **MFEs communicate via Publish-Subscribe (`PubSub`).**

### **Example Store Structure:**
```javascript
Maya.Store.userProfile = {
    name: 'userProfile',
    data: {},
    events: {
        onLoad: async (ev) => {
            let userData = await fetchUserData(ev.key);
            Maya.Store.SetData({ store: 'userProfile', key: ev.key })(userData);
        }
    }
};
```
- `data` holds **instance-specific** state.
- `events` contain all event handlers **for that MFE**.
- `onLoad()` is triggered whenever the MFE loads.

---
## **PubSub (Publish-Subscribe) Communication**
Maya allows MFEs to **send messages to each other asynchronously** using **Publish-Subscribe (PubSub)**.

### **Example:**
#### **Publisher (Sending a Message)**
```javascript
Maya.Store.Publish({ topic: 'user-updated' })({ userId: '123' });
```
#### **Subscriber (Listening for Messages)**
```javascript
Maya.Store.Subscribe({ topic: 'user-updated' })(this);
```
Now, whenever the `user-updated` event is triggered, all **subscribed MFEs** will receive the update.

---
## **Shared Data and Caching**
To **store global state** across MFEs, Maya provides `Maya.Store.SetSharedData()`. This helps cache frequently used data **to avoid unnecessary API calls**.

### **Example:**
```javascript
Maya.Store.SetSharedData({ key: 'theme', value: 'dark' });
```
Now, any MFE can **access this shared data**:
```javascript
let theme = Maya.Store.GetSharedData({ key: 'theme' });
```

---
## **Conclusion**
Maya is a **powerful and flexible** MFE framework that **makes building modular web applications easy**. With **slot-based loading, multi-instance support, fractal MFEs, and a centralized store**, Maya allows developers to create **highly scalable, efficient, and dynamic applications**.

### **Key Takeaways:**
✅ **MFEs** break apps into independent, reusable components.
✅ **Slots** dynamically load MFEs without full-page refreshes.
✅ **Multi-instance MFEs** allow multiple components in a single slot.
✅ **Fractal MFEs** enable infinite nesting and expansion.
✅ **State management** is handled via a singleton store (`Maya.Store`).
✅ **PubSub** allows MFEs to talk to each other seamlessly.
✅ **Caching and shared data** improve performance and efficiency.

By following the **principles of Maya**, developers can create **fast, scalable, and modular web applications** that grow and evolve effortlessly—just like the Vedic concept of **Maya’s illusionary, yet infinite nature**.

