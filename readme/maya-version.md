# 📄 Whitepaper: Maya Versioning – Safe and Dynamic MFE Deployment

## Abstract

Maya is a modular micro frontend (MFE) framework optimized for speed and scalability. To support dynamic, independent deployment of each MFE, Maya incorporates a **lightweight yet powerful versioning system**. This system allows seamless updates, cache control, and backward compatibility without redeploying the entire application.

This paper outlines the **design, workflow, and best practices** of versioning in Maya.

---

## 1. Introduction

In traditional monoliths, any change requires a full redeploy. Maya's **MFE-first architecture** breaks this model by letting each MFE:
- Be developed and deployed independently
- Pull the latest assets dynamically
- Ensure cache-busting using commit-based versioning

Maya uses a **version.json** file that maps each MFE to its unique deployment hash or commit ID. The framework then uses this mapping to load the **correct and latest version of each MFE**.

---

## 2. How Maya Versioning Works

### 📦 `version.json`

This file acts as the **single source of truth** for which version of each MFE is live. It is auto-generated during deployment.

```json
{
  "mfe-inventory": "3caf35a0afba26559785ccad12c77213832fd560",
  "mfe-login": "54f2d64a1a5ef1179c72077c880581c9a54ba2c8",
  "mfe-dashboard": "a17e4568dbacd098723a90acb88f9f313f93f812"
}
```

Each key is the MFE name, and the value is the **commit ID or hash** of its current version.

---

### 🔁 Dynamic MFE Loading with Versioning

When Maya dynamically loads an MFE using `Maya.Route()` or `Maya.Load()`, it uses:

```js
`mfe/${mfe}/js/index.js?ver=${version[mfe]}`
```

This ensures the browser fetches **version-specific assets**, bypassing cached versions.

---

### 🧠 LocalStorage Caching

On first load, Maya fetches the version file:

```js
HttpGET()('/version.json').then(res => {
  localStorage.setItem('mayaversion', JSON.stringify(res.data));
});
```

This ensures:
- All MFEs load with the correct version ID
- Cached versions are only invalidated when updated
- Version data is available across page loads

---

## 3. Benefits of Maya’s Versioning Strategy

| Feature | Benefit |
|--------|---------|
| **Independent MFE Deployment** | Teams can deploy their MFEs without affecting others. |
| **Zero Downtime Updates** | New versions are fetched and applied live. |
| **Cache Invalidation** | Version-based URLs bust CDN/browser caches cleanly. |
| **Rollback Safe** | Revert to any version just by updating `version.json`. |
| **Performance Optimized** | Assets are cached per version → faster repeat loads. |

---

## 4. Deployment Workflow

### ✅ Deployment Steps

1. Developer pushes changes to Git.
2. CI/CD pipeline builds `/mfe/<name>/js/index.js` and `/view/*.html`.
3. Assets are uploaded to CDN (e.g., S3 + CloudFront).
4. `version.json` is updated with the latest commit hash.
5. CloudFront cache is invalidated for `version.json`.

### ✅ Auto-refresh Behavior

Maya checks version every 60 seconds via background polling:
```js
setInterval(() => {
  HttpGET()('/version.json').then(res => {
    localStorage.setItem('mayaversion', JSON.stringify(res.data));
  });
}, 60000);
```

If a version changes, the new asset is fetched on next MFE load.

---

## 5. Best Practices

| Practice | Why it Matters |
|---------|----------------|
| ✅ Use commit hashes | Guarantees uniqueness and traceability |
| ✅ Invalidate CloudFront cache post-deploy | Ensures users don’t get stale code |
| ✅ Automate version.json updates | Prevents human error |
| ✅ Keep versions per environment | Separate `version.json` per stage/prod/test |
| ✅ Load assets with `?ver=<hash>` | Leverages browser & CDN cache correctly |

---

## 6. Anti-Patterns to Avoid

| Anti-Pattern | Risk |
|--------------|------|
| ❌ Hardcoding JS paths without version | Leads to stale asset loading |
| ❌ Manual version.json edits | Easy to mismatch commit → MFE |
| ❌ Single version for all MFEs | Breaks independent deployment model |
| ❌ Not clearing cache after update | Users may load old JS unexpectedly |

---

## 7. Visual Architecture

Here’s a simplified flow:

```
[version.json]
     |
     V
[Maya Parent MFE] ---> [mfe-login/index.js?ver=abc123]
                    ---> [mfe-dashboard/index.js?ver=def456]
                    ---> [mfe-task/index.js?ver=ghi789]
```

---

## 8. Conclusion

Maya’s versioning system is designed for **modern frontend engineering**:
- Supports **independent teams**
- Delivers **faster updates**
- Ensures **cache safety**
- Provides **resilience at scale**

It’s a battle-tested strategy that lets apps stay lean, modular, and always up to date.