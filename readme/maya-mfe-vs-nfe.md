### **Maya MFE & NFE Prompt**  

#### **MFE (Micro Front-End) in Maya**  
- MFEs are **self-contained, independent** front-end modules.  
- Each MFE has:  
  - **index.js** → Contains logic, store, and event handling.  
  - **view/** → Mustache-based HTML templates (`main.html`, `detail.html`).  
- MFEs are dynamically loaded into **slots** within the parent application.  
- **State management** via **Maya.Store.SetData()**.  
- Communication via **Publish/Subscribe** (`Maya.Store.Publish()`, `Maya.Store.Subscribe()`).  
- **Lifecycle Hooks**:  
  - `onLoad` → Initialization, API calls, subscriptions.  
  - `onRender` → After rendering, used to load child MFEs.  
  - `onMessage` → Message handling between MFEs.  
  - `onQuery` → Handles query parameter updates without unloading.  
  - `onUnload` → Cleanup, unsubscribe events.  
- MFEs **can have child MFEs**, which do not have their own `index.js` but rely on the parent’s logic.  
- MFEs **can use** Maya Components (`maya-button`, `maya-input`, `maya-panel`, etc.) for UI elements but are **not directly tied** to them.  

#### **NFE (Nano Front-End) in Maya**  
- NFEs are **lighter, more granular frontend units** than MFEs.  
- Unlike MFEs, **NFEs do not have their own `index.js`**; they **piggyback on the parent MFE’s logic**.  
- NFEs **are NOT directly tied to Maya Components**, but they **can use** both **Maya Components and custom UI components**.  
- They **do not use slots** like MFEs; instead, they are **embedded directly** within MFEs.  
- **State Management** happens via the **parent MFE’s store** (`Maya.Store.SetData`).  
- NFEs typically handle **specific functional areas within an MFE**, such as:  
  - **Form sections**  
  - **Small data display components**  
  - **Interactive elements**  

#### **Key Differences Between MFE & NFE**  

| Feature       | MFE (Micro Front-End) | NFE (Nano Front-End) |
|--------------|----------------------|----------------------|
| **Encapsulation** | Independent web component | Embedded within an MFE |
| **Own `index.js`** | ✅ Yes | ❌ No (uses parent MFE's logic) |
| **Uses Slots?** | ✅ Yes | ❌ No |
| **State Management** | `Maya.Store.SetData` (Independent) | Uses parent MFE's store |
| **Event Communication** | Publish/Subscribe across MFEs | Direct communication within parent MFE |
| **Use Case** | Larger functional modules (e.g., Inventory Management, Dashboard) | Smaller UI components inside MFEs |

---

### **General Maya Architecture Summary**  
- **MFEs** → Modular, independent front-end units with their own logic and views.  
- **NFEs** → Smaller, embedded frontend components **inside MFEs**, which can use Maya or custom UI components.  
- Uses **slots** for MFE nesting but **not for NFEs**.  
- **State management** via `Maya.Store`.  
- **Communication** via events (`Publish/Subscribe`).  
- **Lifecycle hooks** to manage component behavior.  

