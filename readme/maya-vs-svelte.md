### 🧠 Maya vs. Svelte: The Smoothest Bridge

Svelte and Maya both *look lightweight*, feel declarative, and try to **remove the need for frameworks at runtime** — just with totally different philosophies.

Let’s break this down, especially for a Svelte dev.

---

## 🥷 Core Philosophy

| Concept            | Svelte                                 | Maya                                        |
|--------------------|----------------------------------------|---------------------------------------------|
| **Framework type** | Compile-time framework                 | Runtime micro frontend engine               |
| **UI rendering**   | Compiled to DOM ops                    | Mustache templates + Shadow DOM             |
| **Component style**| Single-file (`.svelte`)                | Split files (`index.js`, `main.html`)       |
| **State handling** | `$: reactive`, `stores`                | `Maya.Store.SetData()`                      |
| **Scoped CSS**     | Built-in                               | Via Shadow DOM                              |
| **Dynamic routing**| `svelte-routing` or manual             | `Maya.Route()` + slot-based loading         |
| **Communication**  | Props, stores, context                 | Pub/Sub (`Publish/Subscribe`)               |

---

## 🔄 Translate Svelte ➡️ Maya

### 🧩 Svelte Chatbot Component

```svelte
<script>
  let messages = [{ sender: 'bot', text: 'Hello!' }];
  let message = '';

  const sendMessage = () => {
    messages = [...messages, { sender: 'user', text: message }];
    message = '';
  };
</script>

<div class="chat-window">
  {#each messages as msg}
    <div>{msg.sender}: {msg.text}</div>
  {/each}
</div>

<input bind:value={message} placeholder="Type a message..." />
<button on:click={sendMessage}>Send</button>
```

---

### 🔁 Maya Version (Same Bot, Different Mindset)

**📁 `/mfe/chatbot/js/index.js`**
```js
Maya.Store.chatbot = {
  name: 'chatbot',
  data: {},
  self: {},
  events: {
    onLoad: async (options) => {
      Maya.Store.SetData({ store: 'chatbot', key: options.key })([
        { sender: 'bot', text: 'Hello!' }
      ]);
    },
    send: async (ev) => {
      const input = Maya.Store.chatbot.self[ev.key]?.msg;
      const prev = Maya.Store.chatbot.data[ev.key] || [];
      const updated = [...prev, { sender: 'user', text: input }];
      Maya.Store.SetData({ store: 'chatbot', key: ev.key })(updated);
      Maya.Store.chatbot.self[ev.key].msg = ''; // reset input
    }
  }
};

class ChatBot extends MayaMFE {
  constructor() {
    super();
    this.setStore(Maya.Store.chatbot);
    this.setView('main');
  }
}
window.customElements.define('albert-chatbot', ChatBot);
```

---

**📄 `/view/main.html`**
```html
<h2>Bot UI</h2>

<div class="chat-window">
  {{#Items}}
    <div>{{sender}}: {{text}}</div>
  {{/Items}}
</div>

<maya-input store="chatbot" name="msg">
  <input placeholder="Your message..." />
</maya-input>

<maya-button store="chatbot" operationId="send">Send</maya-button>

<style>
.chat-window {
  border: 1px solid #ccc;
  height: 200px;
  overflow-y: auto;
  padding: 8px;
  margin-bottom: 10px;
}
</style>
```

---

## 🧠 Svelte Dev's Takeaway

| You Know This in Svelte     | Use This in Maya                  |
|-----------------------------|-----------------------------------|
| `$store`, `bind:value`      | `maya-input` with `store` + `name` |
| `{#each}`                   | `{{#Items}}` in Mustache         |
| `<script>` logic            | `Maya.Store.events` + `onLoad`   |
| `<Component />` nesting     | Nested MFEs via `<maya-slot>`    |
| Dynamic views               | `Maya.Route('mfe/view?target=slot')` |

---

## 💡 Best Part for Svelte Devs?

- Maya still lets you write in **pure HTML + JS**, zero bundlers
- You can plug in Maya MFEs **next to Svelte apps** (if needed)
- And it’s **perfect for dashboard-style UIs or CRUD-heavy apps**
