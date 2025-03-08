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
## **4. Fractal Child MFEs – Components Inside Components**
Maya allows MFEs to **contain other MFEs inside them**. This enables **nested structures** where one MFE can load another, forming a tree-like hierarchy.

Think of this like a **folder system** on your computer:
- A **main folder** contains subfolders
- Those **subfolders contain more subfolders**
- This can continue infinitely, organizing everything neatly

For example, a **task management MFE** might contain:
- A **task list MFE** inside it
- Each **task MFE** might contain a **subtask MFE**

This **self-replicating system** makes applications **expandable and reusable** without extra work.

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
## **6. PubSub – How MFEs Talk to Each Other**
Since MFEs are **independent**, they need a way to **communicate**. Maya uses **Publish-Subscribe (PubSub)** to send and receive messages between MFEs.

Imagine a **news channel**:
- A **publisher** (news reporter) sends updates.
- **Subscribers** (viewers) listen and receive updates.

Example:
- **Publisher:**
```javascript
Maya.Store.Publish({ topic: 'user-updated' })({ userId: '123' });
```
- **Subscriber:**
```javascript
Maya.Store.Subscribe({ topic: 'user-updated' })(this);
```
Whenever **user data changes**, all MFEs listening for `user-updated` will **automatically update**.

---
## **7. Shared Data & Caching – Making Apps Faster**
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
✅ **Fractal MFEs** enable infinite nesting.
✅ **Maya.Store** centralizes state management.
✅ **PubSub & caching** improve performance.

With Maya, developers can build **fast, flexible, and scalable** applications that grow effortlessly—just like the Vedic idea of **Maya’s illusionary, yet infinite nature.**

