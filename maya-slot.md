**White Paper: Understanding Maya Slots – The Backbone of Maya's Modular Architecture**

## Introduction
Maya is a **Micro Front-End (MFE) framework** designed to build **modular, scalable, and dynamic** web applications. It breaks down large applications into smaller, independent pieces called **MFEs**, each responsible for a specific functionality. The name **Maya** comes from **Vedic philosophy**, where **Maya** means **illusion**—the idea that what we see is just a part of a larger, hidden reality. Similarly, the Maya framework allows applications to appear as one unified system, while under the hood, they are made up of many independent components working together seamlessly. 

Maya’s **slot-based architecture** is what makes it so flexible and scalable. Slots are like **placeholders** where MFEs can be dynamically loaded and removed, enabling applications to grow and change without needing a full reload. Think of slots as **designated spaces** where different MFEs can plug in, making the UI **modular, efficient, and expandable**.

Maya defines **initial slots** (`main`, `header`, `left`, `right`, `center`, `overlay`, `menu`, `submenu`), while also allowing **custom slots** within MFEs through the `maya-slot` component. This document explores the role of **slots**, their configuration, and how they facilitate MFE management, including **floating slots**, **multi-instance support**, and **positional loading**.

---
## Maya's Predefined Slots
Maya provides a set of **default slots** that serve as **entry points** for loading MFEs dynamically.

| **Slot Name** | **Description** |
|-------------|---------------|
| `main` | The primary content area for loading MFEs |
| `header` | The top navigation/header slot |
| `left` | Left side panel for navigation or auxiliary features |
| `right` | Right side panel for context-aware data or actions |
| `center` | The core content section, often used in **dashboard layouts** |
| `overlay` | A fullscreen slot for modals or overlays |
| `menu` | A floating slot, typically for dropdown menus |
| `submenu` | A secondary floating slot, useful for nested dropdowns |

Each of these slots is automatically recognized by Maya and can dynamically accept MFEs based on user interactions or routing changes.

---
## **Custom Slots Inside MFEs: The `maya-slot` Component**
Maya allows MFEs to **define their own slots** using the `<maya-slot>` component. This enables MFEs to load **child MFEs** inside their own context, maintaining a **modular and hierarchical structure**.

### **Example: Defining a Custom Slot in an MFE**
```html
<maya-panel>
    <maya-slot name="sub-content"></maya-slot>
</maya-panel>
```
In this example, a **sub-content slot** is defined inside an MFE. Other MFEs can now be dynamically loaded into this slot.

---
## **Multi-Instance Slots (`multi=true`)**
By default, a **slot can hold only one MFE** at a time. However, using the `multi=true` attribute, a slot can hold **multiple MFEs serially**.

### **Example: Enabling Multiple MFEs in a Slot**
```html
<maya-slot name="notifications" multi="true"></maya-slot>
```
Now, multiple MFEs can exist within this slot simultaneously. 

To remove an MFE from a **multi-instance slot**, you can **target it by its `key`** using `Maya.Close`.

```javascript
Maya.Close({ target: 'notifications', key: '1741386681020.856' });
```
This removes **only the MFE with the specified key**, while others in the slot remain active.

---
## **Removing MFEs from Slots with `Maya.Close`**
Maya provides a simple mechanism to remove MFEs from slots.

### **Closing an Entire Slot:**
```javascript
Maya.Close({ target: 'overlay' });
```
This removes **all MFEs from the `overlay` slot**.

### **Removing a Specific MFE Using `key`:**
```javascript
Maya.Close({ target: 'notifications', key: '123456' });
```
This ensures **only the MFE with the specified key** is removed from a multi-instance slot.

---
## **Floating Slots: `menu` and `submenu`**
Maya provides `menu` and `submenu` as **floating slots**, which can be dynamically positioned relative to the **click event**.

These floating slots allow Maya to create **dynamic menus, popups, and context-aware MFEs**.

### **Loading an MFE into a Floating Slot**
```javascript
Maya.Load('context-menu?target=menu');
```
This loads the **`context-menu` MFE** into the **floating `menu` slot**.

---
## **Positional Loading with `LoadAtPosition`**
Maya introduces a powerful way to **load MFEs at specific positions** using `Maya.LoadAtPosition`. This method **binds the loaded MFE to the clicked element’s bounding rectangle**.

### **Example: Positioning an MFE Near a Clicked Element**
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

This is particularly useful for **tooltips, popovers, and contextual actions.**

---
## **Maya Slots and the Fractal Nature of Maya**
Maya’s architecture is inspired by the **holographic nature of reality**—just as small patterns repeat in nature, Maya allows **MFEs to create and contain other MFEs**, enabling **infinite nesting** and **dynamic expansion**.

### **How Slots Enable a Fractal System:**
1. **MFEs Can Create Their Own Slots:**
   - An MFE can define custom slots (`maya-slot`), allowing **nested MFEs**.
2. **Slots Are Recursive:**
   - A slot inside an MFE can hold another MFE that **also contains slots**, enabling **infinite nesting**.
3. **Dynamic Loading Makes the System Expandable:**
   - Maya can **dynamically load new MFEs into slots**, making the UI **self-growing** and adaptable.

### **Example: Nested MFE Slots**
```html
<maya-panel>
    <maya-slot name="first-level">
        <maya-slot name="second-level"></maya-slot>
    </maya-slot>
</maya-panel>
```
This structure **allows for deeply nested MFE hierarchies**, where each component can **expand dynamically**.

---
## **Conclusion**
Maya’s **slot-based architecture** is the foundation of its **scalable, modular, and dynamic nature**. Slots enable **seamless MFE integration, multi-instance support, floating UI elements, and fractal-like UI expansion**. With the ability to dynamically load MFEs into predefined or custom slots, **developers gain immense flexibility in UI composition and state management.**

