## 🧠 Philosophy Clash

| Concept           | **Angular**                                         | **Maya**                                               |
|-------------------|-----------------------------------------------------|--------------------------------------------------------|
| Framework Type     | Full framework (opinionated, monolithic)           | Micro Frontend engine (modular, decentralized)         |
| Rendering          | Declarative with `*ngFor`, `{{}}`, change detection| Mustache templates + manual data trigger via `SetData` |
| Component Style    | Decorators (`@Component`)                          | Web Components (`customElements.define`)               |
| State Management   | Services, RxJS, NgRx, etc.                         | `Maya.Store.<name>` per MFE                            |
| Routing            | Angular Router (`<router-outlet>`)                 | `Maya.Route()`, slot-based with `<maya-slot>`          |
| Communication      | Input/Output, Services, EventEmitters              | Pub/Sub (`Publish` / `Subscribe`), Shared Data         |
| CSS Isolation      | ViewEncapsulation / Shadow DOM                     | Native Shadow DOM in each MFE                          |
| Deployment         | Single-bundle or monorepo builds                   | Independent CDN-deployed MFEs                          |

---

## 🔄 Angular ➡️ Maya: What Changes?

### ✅ Similarities
- You still build modular UI with encapsulated logic
- You still bind data to templates
- You still wire UI → logic → state

### 🚨 Key Differences

| Feature                | Angular                            | Maya                            |
|------------------------|-------------------------------------|----------------------------------|
| Component Data Binding | Auto via `[(ngModel)]`              | Manual via `maya-input` and `Store.self` |
| View Rendering         | Auto with change detection          | Triggered by `Maya.Store.SetData()`     |
| Services / DI          | Built-in Dependency Injection       | No DI — everything is just JS + Store   |
| Navigation             | Angular Router + Routes             | URL hash + `Maya.Route()`              |
| Lifecycles             | `ngOnInit`, `ngAfterViewInit`, etc. | `onLoad`, `onRender`, `onQuery`         |

---

## 👀 Example: Angular Chatbot vs Maya Chatbot

### 📦 Angular:
```ts
@Component({ selector: 'chatbot', templateUrl: './chatbot.component.html' })
export class ChatbotComponent {
  messages = [{ sender: 'bot', text: 'Hi' }];
  input = '';

  send() {
    this.messages.push({ sender: 'user', text: this.input });
    this.input = '';
  }
}
```

```html
<div *ngFor="let msg of messages">{{msg.sender}}: {{msg.text}}</div>
<input [(ngModel)]="input" />
<button (click)="send()">Send</button>
```

---

### 🪄 Maya:

**/js/index.js**
```js
Maya.Store.chat = {
  name: 'chat',
  data: {},
  self: {},
  events: {
    onLoad: async (opt) => Maya.Store.SetData({ store: 'chat', key: opt.key })([
      { sender: 'bot', text: 'Hi' }
    ]),
    send: async (ev) => {
      const msg = Maya.Store.chat.self[ev.key]?.text;
      const prev = Maya.Store.chat.data[ev.key] || [];
      Maya.Store.SetData({ store: 'chat', key: ev.key })([...prev, { sender: 'user', text: msg }]);
    }
  }
}

class Chatbot extends MayaMFE {
  constructor() {
    super();
    this.setStore(Maya.Store.chat);
    this.setView('main');
  }
}
window.customElements.define('albert-chat', Chatbot);
```

**/view/main.html**
```html
{{#Items}}
  <div>{{sender}}: {{text}}</div>
{{/Items}}

<maya-input store="chat" name="text"><input /></maya-input>
<maya-button store="chat" operationId="send">Send</maya-button>
```

---

## 🧩 Teaching Maya to Angular Devs

### 📘 Concepts to Emphasize:
| Angular Dev Thinks...            | Maya Response                        |
|----------------------------------|--------------------------------------|
| “Where’s my Component Decorator?”| Web Components via `customElements` |
| “How do I bind form inputs?”     | `maya-input` + `Store.self`         |
| “How do I navigate between views?”| `Maya.Route('mfe/view?target=slot')`|
| “What’s the equivalent of services?”| `Store.events` or pub/sub          |

---

## 💡 TL;DR for Angular Devs

> Maya gives you the freedom of Web Components with structure like Angular — but no decorators, no modules, and no CLI. Just **slots, stores, templates, and logic**.

