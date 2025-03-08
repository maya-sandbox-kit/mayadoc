**White Paper: Maya Child MFEs & The Fractal Concept**

## **Introduction**
Maya is a **Micro Front-End (MFE) framework** that allows applications to be built from **small, independent components** that work together. One of its most powerful features is the **Fractal MFE Concept**, where **child MFEs** can be dynamically added inside **parent MFEs**, creating a **self-expanding, scalable structure**.

Think of this like **nested Russian dolls**—each doll (MFE) contains a smaller doll (child MFE), which in turn can contain another doll, and so on. This allows applications to grow dynamically while keeping everything **organized and reusable**.

In this document, we’ll explain how Maya’s **multi-slot system** can be used to dynamically add child components, and how **inheriting keys** helps in keeping data organized.

---
## **1. Understanding Child MFEs & Fractal Architecture**
A **Child MFE** is simply an MFE that **exists inside another MFE**. In Maya, an MFE can **dynamically load and control other MFEs** within it.

### **Real-World Example:**
Imagine you’re building a **Project Management App**:
- The **Project MFE** is the main component.
- Inside it, you load **Task MFEs** (one for each task in the project).
- Each **Task MFE** contains **Subtask MFEs** (small steps inside a task).
- This structure can continue infinitely, just like a **tree with branches**.

This makes applications **modular and expandable** without extra effort.

---
## **2. Using Multi-Slot to Add Child Components Dynamically**
Maya provides a feature called **multi-slot**, which allows a parent MFE to **hold multiple child MFEs at the same time**.

### **How Multi-Slot Works:**
- Normally, a **slot can hold only one MFE** at a time.
- If `multi=true` is added, the slot can hold **multiple MFEs simultaneously**.
- Each child MFE can be added dynamically, meaning **new components can appear as needed**.

### **Example:**
#### **Defining a Parent MFE with a Multi-Slot**
```html
<maya-panel>
    <maya-slot name="tasks" multi="true"></maya-slot>
</maya-panel>
```
This means that **multiple child MFEs** can be added inside the `tasks` slot.

#### **Dynamically Adding Child MFEs**
```javascript
Maya.Load('task-mfe?target=tasks');
```
Every time this function is called, **a new Task MFE appears** inside the `tasks` slot **without replacing the existing ones**.

This is useful when you need to dynamically add new UI elements, like:
- A **list of comments in a discussion**
- **Tasks inside a project**
- **Multiple notifications appearing at once**

---
## **3. Key Inheritance: Keeping Data Organized**
When a **child MFE is added**, it can **inherit a key from its parent**. This is important because:
- It keeps **all related data grouped together**.
- When the child loads again, it retrieves the correct **parent-related data**.
- It helps with **caching**—previously loaded child MFEs don’t need to fetch data again.

### **How Key Inheritance Works**
Every MFE in Maya is assigned a **unique key** when loaded:
```javascript
Maya.Load('project-mfe?key=12345');
```
Now, if a **Task MFE** is loaded inside this Project MFE, it can **inherit the key**:
```javascript
Maya.Load('task-mfe?target=tasks&key=12345');
```
This means **all tasks inside the project will be stored under the same key**.

---
## **4. Storing Data Using Inherited Keys**
Maya keeps track of MFE data inside `Maya.Store`. When a child MFE **inherits a key**, its data is stored under the same **parent key**.

### **Example: Organizing Data in the Store**
```javascript
Maya.Store.project = {
    name: 'project',
    data: {
        '12345': {
            title: 'New Website Project',
            tasks: {
                'task1': { title: 'Design Homepage', status: 'In Progress' },
                'task2': { title: 'Build API', status: 'Pending' }
            }
        }
    }
};
```
This structure ensures that **all tasks are linked to their project**.

Now, if the **Task MFE reloads**, it can simply retrieve its data using the inherited key:
```javascript
let projectData = Maya.Store.project.data['12345'];
```
This prevents **data from being mixed up** between different instances.

---
## **5. Benefits of Using Fractal MFEs & Key Inheritance**
### ✅ **Dynamic & Scalable Applications**
- New components can be added **without rewriting code**.
- Applications can grow **without performance issues**.

### ✅ **Organized Data Structure**
- Everything related to a **single project or entity** stays **under one key**.
- Helps in **avoiding conflicts between multiple instances**.

### ✅ **Efficient Caching & Faster Load Times**
- Previously loaded child MFEs **don’t need to fetch data again**.
- Reduces **unnecessary API calls**.

### ✅ **Easier Communication Between Parent & Child MFEs**
- Since child MFEs **share the parent key**, they can easily **access or update parent data**.

---
## **6. Real-World Example: A Task Manager App**
Let’s put everything together:

### **Scenario:**
You are building a **Task Manager App** with the following features:
- A **Project MFE** that contains multiple **Task MFEs**.
- Each **Task MFE** contains multiple **Subtask MFEs**.
- Users can **dynamically add tasks and subtasks**.
- Data stays organized under **one key per project**.

### **How It Works in Maya:**
1. **Load the Project MFE:**
   ```javascript
   Maya.Load('project-mfe?key=001');
   ```
2. **Dynamically Add Task MFEs Inside the Project:**
   ```javascript
   Maya.Load('task-mfe?target=tasks&key=001');
   ```
3. **Dynamically Add Subtasks Inside Each Task:**
   ```javascript
   Maya.Load('subtask-mfe?target=subtasks&key=001-task1');
   ```
4. **All data stays under the `001` project key**, and every task and subtask is correctly linked.

---
## **Conclusion**
Maya’s **Fractal MFE Concept** and **multi-slot feature** provide a **powerful way to build modular, scalable applications**. By inheriting keys, child MFEs remain **linked to their parent**, keeping data **organized and efficient**.

### **Key Takeaways:**
✅ **Child MFEs enable infinite nesting, making applications flexible**.
✅ **Multi-slot allows adding multiple child MFEs dynamically**.
✅ **Key inheritance keeps data linked and prevents duplication**.
✅ **Caching improves performance by reducing redundant API calls**.

By following this approach, developers can build **highly interactive, scalable, and efficient applications with Maya.** 🚀

