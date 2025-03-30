### Core Principles of Maya

#### 1. Native Web Component Foundation
At its core, Maya utilizes **Web Components** to encapsulate functionality and styles via the Shadow DOM. Each MFE is a custom HTML element, declared using `customElements.define()`, ensuring self-contained behavior and appearance.

![Maya - slot](assets/maya-slot2.png)

#### 2. Independent Git Repositories
Every MFE is stored in its own **dedicated Git repository**, offering:
- Complete autonomy for feature teams
- Isolated version control and change history
- Cleaner, conflict-free collaboration

> Maya MFE : [inventory](https://github.com/maya-dev-kit/inventory),  [lot](https://github.com/maya-dev-kit/lot) etc...

This structure promotes **bounded contexts** and allows each MFE to evolve independently.

#### 3. Decentralized CI/CD Pipelines
Maya enables each MFE to have its own CI/CD pipeline with independent configurations for:
- Code linting and formatting
- Unit and integration tests
- Artifact packaging and deployment to CDN or S3

Updates are propagated using a `version.json` manifest that ensures the parent Maya app always resolves the latest or specific version of a given MFE.

---

### Anatomy of a Maya MFE

A typical Maya MFE adheres to a simple, declarative folder structure:
```
/mfe/<mfe-name>/
  /js/
    index.js       # Contains store, events, lifecycle hooks
  /view/
    main.html      # Mustache-based HTML view
    detail.html    # Optional additional view
```

In `index.js`, the MFE registers its store, binds event handlers, and declares its lifecycle methods (`onLoad`, `onRender`, `onQuery`, etc.).

---

### Dynamic Integration via Slots
Maya applications are composed using **slots**—placeholders in the DOM where MFEs can be loaded dynamically. This allows for runtime flexibility:
- Static: `<albert-inventory slot="main"></albert-inventory>`
- Dynamic: `Maya.Load('inventory/main?target=main')`

This model enables deep nesting of MFEs and supports real-time layout composition without full page reloads.

---

### Communication & State Management

Maya MFEs communicate using:
- **Pub/Sub Messaging**: `Maya.Store.Publish/Subscribe` for loose coupling
- **Shared Data**: Global app state accessible via `Maya.Store.SetSharedData`
- **Scoped Store**: Each MFE manages its own internal state keyed by unique identifiers, ensuring safe concurrent instances

---

### Deployment Model

Maya MFEs are deployed as static assets (JS and HTML) to a CDN or S3. The versioning model ensures:
- Atomic deployments per MFE
- Cache-busting via commit hashes
- Continuous delivery with minimal coordination

Each deployment updates a `version.json` file. The host app polls this file and ensures all MFEs are synced with their appropriate versions.

---

### Advantages

- **Independent Deployments**: MFEs can be updated without redeploying the host app
- **Encapsulation**: Native Web Components eliminate global style and script conflicts
- **Performance**: Lazy loading and minimal coupling reduce bundle sizes
- **Autonomy**: Teams move faster with clear ownership and CI/CD pipelines

---

### Use Cases

Maya is particularly well-suited for:
- Enterprise SaaS platforms
- Dashboards with role-based feature sets
- Multi-product portals requiring isolation
- Teams adopting domain-driven front-end development

---

### Conclusion

Maya delivers a robust, scalable approach to front-end modularity using native browser technologies. By empowering teams to develop, test, and ship front-end features independently, Maya significantly reduces complexity and boosts velocity. With its adherence to standards, slot-based composition, and decentralized CI/CD strategy, Maya positions itself as a leading choice for organizations embracing the future of front-end architecture.



