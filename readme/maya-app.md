**White Paper: Understanding Maya App (`maya-maya`) & Initial Slot Architecture**

## **Introduction**
Maya is a **Micro Front-End (MFE) framework** that provides a dynamic and modular approach to building web applications. At the core of every Maya application is **`maya-maya`**, the **root MFE** that defines the structure and initializes the **primary slots** where other MFEs are dynamically loaded.

This white paper explores:
1. **What is `maya-maya` and how it initializes a Maya App**
2. **How slots (`main`, `header`, `left`, `right`, etc.) act as anchors for MFEs**
3. **How Maya itself is an MFE, with `maya.html` defining its structure**

---
## **1. `maya-maya`: The Root of Every Maya App**
The `maya-maya` component serves as the **entry point of a Maya App**. It is declared in `<namespace>.html` and **sets the namespace (`ns`)** for the application.

### **Example: Declaring a Maya App**
```html
<maya-maya ns="albert">
    <albert-alheader slot="header" store="alheader"></albert-alheader>
    <albert-alhome slot="main"></albert-alhome>
</maya-maya>
```
### **How This Works:**
1. `maya-maya` is declared with the **namespace (`ns="albert"`)**, which tells Maya **where to find the application’s entry point**.
2. The app **loads initial MFEs** (`albert-alheader` for the header and `albert-alhome` for the main slot).
3. These MFEs are placed inside **predefined slots**, allowing for **dynamic component replacement**.

---
## **2. Slots: The Anchors for Loading MFEs**
In Maya, slots act as **placeholders** where MFEs dynamically load. These slots allow applications to be **modular and flexible**.

### **Predefined Slots in Maya**
| **Slot Name** | **Purpose** |
|-------------|-------------|
| `header` | Holds the main navigation/header MFE |
| `main` | The primary content area where the main MFE loads |
| `left` | Sidebar or additional navigation components |
| `right` | Contextual panels for notifications, settings, or tools |
| `menu` | Floating menu for quick actions |
| `submenu` | Secondary floating menu, nested inside `menu` |
| `overlay` | Full-screen modal for pop-ups |
| `center` | Centralized slot for dialog boxes or modals |
| `footer` | Footer content |

These slots are dynamically populated with **different MFEs as needed**, making Maya applications **adaptive and lightweight**.

### **Example: How Slots Work in a Maya App**
```html
<div id="header" class="header"><slot name="header"></slot></div>
<div id="main" class="main"><slot name="main"></slot></div>
<div id="left" class="left"><slot name="left"></slot></div>
<div id="right" class="right"><slot name="right"></slot></div>
<div id="menu" class="top"><maya-slot name="menu"></maya-slot></div>
<div id="overlay" class="overlay"><slot name="overlay"></slot></div>
```
Each slot acts as an **anchor** where MFEs are dynamically inserted.

### **Dynamically Loading MFEs into Slots**
Maya allows developers to dynamically insert MFEs into slots using `Maya.Load()`:
```javascript
Maya.Load('dashboard?target=main');
Maya.Load('navigation?target=left');
Maya.Load('user-profile?target=right');
```
This ensures **only necessary components are loaded**, optimizing performance.

---
## **3. Maya as an MFE (`maya.html`)**
Maya itself is an **MFE**, meaning it has its **own view (`maya.html`)** that defines the default layout structure.

### **Example: `maya.html` Structure**
```html
<style>
    .float { position: absolute; }
    .center { position: absolute; top: 50%; left: 50%; transform: translate(-50%, -50%); z-index: 10; }
    .overlay { position: absolute; top: 50%; left: 50%; transform: translate(-50%, -50%); z-index: 100; }
    .right { position: absolute; top: 0%; right: 0%; height: 100%; background-color: #fff; border-left: solid 1px #e6eaf0; }
    .left { position: absolute; top: 0%; left: 0%; }
    .header { width: 100%; }
    .main { font-size: 14px; width: 100%; }
    .notification { position: absolute; top: 54px; right: 0; height: auto; }
</style>

<maya-slot class="top" name="menu"></maya-slot>
<maya-slot class="top" name="submenu"></maya-slot>
<div id="header" class="header"><slot name="header"></slot></div>
<div id="notification" class="notification"><slot name="notification"></slot></div>
<div id="main" class="main"><slot name="main"></slot></div>
<div id="footer"><slot name="footer"></slot></div>
<div id="left" class="left"><slot name="left"></slot></div>
<div id="right" class="right"><slot name="right"></slot></div>
<div id="center" class="center"><slot name="center"></slot></div>
<div id="overlay" class="overlay"><slot name="overlay"></slot></div>
```

### **What This Means:**
- **The `maya-maya` component itself is an MFE** with a structured layout.
- **All slots (`header`, `main`, `left`, etc.) are predefined** in `maya.html`, ensuring a consistent UI structure.
- **The view automatically adjusts based on loaded MFEs**, keeping the UI dynamic and adaptable.

---
## **4. How Maya Launches the Initial Slot**
When `maya-maya` initializes, it performs the following actions:
1. **Loads the `ns`-specific index.html file** (e.g., `mfe/albert.html`).
2. **Initializes slots** and assigns MFEs based on the configuration.
3. **Loads the first MFEs dynamically**, such as:
   ```javascript
   Maya.Load('albert-alheader?target=header');
   Maya.Load('albert-alhome?target=main');
   ```
4. **Allows further MFE loading based on user interaction**.

This ensures that **Maya Apps remain modular, scalable, and adaptable**.

---
## **Conclusion**
Maya’s **slot-based architecture** and **namespace-driven initialization** make it a **powerful and flexible** MFE framework.

### **Key Takeaways:**
✅ **`maya-maya` is the root component of every Maya App**.

✅ **Slots (`main`, `left`, `right`, etc.) act as anchors for MFEs**.

✅ **Maya itself is an MFE with `maya.html` defining its layout**.

✅ **Apps initialize dynamically using namespaces (`ns`) to locate the correct index file**.

✅ **Only required MFEs are loaded initially, ensuring better performance**.
