# Architecture

A browser-based WHOOP 4.0 client (Web Bluetooth, optionally wrapped with Capacitor on iOS). Data is stored locally in the browser; two small Cloudflare-style functions handle optional sync and coaching.

```mermaid
flowchart LR
    Strap[(WHOOP 4.0 strap)] <-->|BLE GATT| BLE

    subgraph Web["web/ (static app)"]
        BLE["js/ble/<br/>client · packet · crc · parsers · uuids<br/>capacitor-bridge"]
        Data["js/data/<br/>db · schema · queries · export · integrity"]
        Metrics["js/metrics/<br/>hrv · recovery · strain · sleep<br/>zones · workouts · insights"]
        Health["js/health/<br/>apple · scale · sync"]
        Sync[js/sync/client.js]
        UI["app.js / app-mvp.js<br/>index.html"]
        Dev["js/dev/<br/>capture · analyzer"]
    end

    DB[(Browser local DB)]
    Fn["functions/api/<br/>sync.js · coach.js"]
    LLM[(LLM provider)]
    Remote[(Sync store)]

    BLE --> Data --> DB
    Data --> Metrics --> UI
    Health --> Data
    Sync -->|/api/sync| Fn --> Remote
    UI -->|/api/coach| Fn --> LLM
    Dev -.debug captures.-> BLE
    Py["Python reference<br/>parser · sleep · zones (tests/)"] -.parity.-> Metrics
    Cap["Capacitor iOS shell<br/>capacitor.config.json"] -.wraps.-> Web
```
