**Maya Core Concepts: A Simple Explanation**

## **Introduction**
Maya is a **Micro Front-End (MFE) framework** designed to make building **modular, scalable, and dynamic** web applications easier. 

Inspired by **Vedic philosophy**, Maya represents **illusion**—things may appear as a single unit but are actually composed of many independent parts. Similarly, in software, Maya lets developers create applications made up of **small, self-contained parts (MFEs)** that work together but can be managed separately.

Maya enables:
- **Independent, reusable components (MFEs)**
- **Dynamic loading of features using slots**
- **Support for multiple instances of components**
- **Nested structures that can infinitely expand**
- **A centralized system for managing data and communication**
- **Event-driven communication and seamless data syncing**

This document explains Maya’s core concepts in **simple terms.**

---
## **1. Micro Front-Ends (MFEs) – The Building Blocks**
Think of an application as a **house**. Instead of building it all at once, you construct it **room by room**. Each room serves a different function and can be modified without affecting the others.

Similarly, **Maya applications are built using MFEs**—small, independent parts that handle specific tasks like:
- User profiles
- Notifications
- Reports
- Task management

Each **MFE is self-contained** and can be developed, deployed, and updated without breaking the whole application.

---
## **2. Slot-Based Dynamic Loading – Plugging in Components**
Instead of hardcoding components into an app, Maya provides **slots** where MFEs can be plugged in when needed.

Think of a **slot like an empty socket** in a wall. You can plug in different appliances (MFEs) based on what you need. Some common slots in Maya include:
- **Main Slot** → The primary content area
- **Header Slot** → The top navigation bar
- **Left Slot** → Side menu or filters
- **Right Slot** → Notifications or additional details
- **Overlay Slot** → Pop-ups or modals

Developers can **define their own slots** inside MFEs, making it easy to **load components dynamically** when needed.

> [maya-slot](maya-slot.md) - deep dive

---
## **3. Multi-Instance MFEs – Running Multiple Copies**
By default, a slot **holds only one MFE at a time**, but Maya allows some slots to **hold multiple MFEs at once** using `multi=true`.

For example, in a **notification panel**, new alerts keep appearing without replacing old ones. This is because the **notifications slot supports multiple MFEs.**

If needed, a **specific MFE can be removed** without affecting others:
```javascript
Maya.Close({ target: 'notifications', key: '12345' });
```
This removes **only the notification with key `12345`**, while others remain.

> [maya-key](maya-key.md) - deep dive

---
## **4. Event Handling & One-Way Data Binding**
Maya follows a **one-way data binding model**, where input fields automatically sync data to the store (`Maya.Store.<mfe>.self<key>`). This ensures **real-time state management** without requiring manual event listeners.

### **Example: Auto-Syncing User Input**
```html
<maya-input store="userProfile" name="email">
    <input type="email" placeholder="Enter email">
</maya-input>
```
Whenever a user types into the input field, Maya automatically updates:
```javascript
Maya.Store.userProfile.self[<key>].email = 'test@example.com';
```
### **Handling Click Events with Operation ID**
Instead of calling `onclick`, Maya invokes the **`operationId`** specified in the component:
```html
<maya-button store="userProfile" operationId="saveUser"></maya-button>
```
```javascript
Maya.Store.userProfile.events.saveUser = async (event) => {
    let email = Maya.Store.userProfile.self[event.key].email.trim();
    let response = await fetch('/api/saveUser', {
        method: 'POST',
        body: JSON.stringify({ email })
    });
    console.log('User saved:', response);
};
```
✅ **No need for `document.getElementById()`, data is auto-synced and accessible in the event handler.**

---
## **5. State Management via Maya Store – Keeping Everything in Sync**
Maya uses a **single shared store (`Maya.Store`)** to manage data across MFEs. 

Think of `Maya.Store` like a **shared notebook** where each MFE writes its own information, making it available to others.

### **How It Works:**
- **Each MFE has a section in the store** (`Maya.Store.<mfe>.data`).
- **All event handlers** are grouped (`Maya.Store.<mfe>.events`).
- **Shared data is stored centrally**, avoiding duplicate API calls.

Example structure:
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
- **Data is stored separately for each instance** (`ev.key`).
- **Events like `onLoad()` update data when needed.**

---
## **6. Shared Data & Caching – Making Apps Faster**
Instead of **fetching the same data repeatedly**, Maya **caches shared data** for all MFEs.

For example, if a user’s theme preference is stored:
```javascript
Maya.Store.SetSharedData({ key: 'theme', value: 'dark' });
```
Now, any MFE can **retrieve the theme** without making an API call:
```javascript
let theme = Maya.Store.GetSharedData({ key: 'theme' });
```
This **reduces server requests** and makes the application **faster.**

---
## **Conclusion – Why Maya?**
Maya simplifies **complex applications** by breaking them into **small, reusable parts** that work together dynamically.

✅ **MFEs** make applications modular & reusable.
✅ **Slots** dynamically load MFEs without full-page refreshes.
✅ **Multi-instance support** allows multiple MFEs in a slot.
✅ **Event-driven handling ensures seamless UI updates.**
✅ **One-way data binding eliminates manual input management.**
✅ **Maya.Store centralizes state management and caching.**

With Maya, developers can build **fast, flexible, and scalable** applications that grow effortlessly—just like the Vedic idea of **Maya’s illusionary, yet infinite nature.** 🚀

