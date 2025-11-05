# ![🎮](https://fonts.gstatic.com/s/e/notoemoji/16.0/1f3ae/72.png) **Pac-Man Web Remake — Documento di Analisi Tecnica**

## ![📘](https://fonts.gstatic.com/s/e/notoemoji/16.0/1f4d8/72.png) **1. Obiettivo del progetto**

Creare un remake moderno, fedele e performante di _Pac-Man_, completamente **web-based**, con **TypeScript + Phaser 3**, mantenendo un codice **pulito, modulare e testabile**.

Il focus tecnico è:

- _Separare la logica di gioco dal rendering_ (principio **domain-first**)
    
- _Garantire prestazioni stabili (60 FPS)_ su desktop e mobile
    
- _Offrire una DX fluida_ grazie a tool moderni (Vite, Vitest, Prettier, ESLint)
    

---

## ![⚙️](https://fonts.gstatic.com/s/e/notoemoji/16.0/2699_fe0f/72.png) **2. Stack Tecnologico**

|Categoria|Strumento|Motivazione|
|---|---|---|
|**Linguaggio**|TypeScript (strict mode)|Tipi forti, autocompletamento, sicurezza|
|**Game Engine**|Phaser 3 (Tilemap + Arcade Physics)|Maturo, supporta tilemap e collisioni 2D efficaci|
|**Bundler / Dev Server**|Vite|Hot Module Reload veloce, build ottimizzata|
|**Lint & Format**|ESLint (airbnb + TS plugin) + Prettier|Codice coerente e leggibile|
|**Test**|Vitest (unit/integration), Playwright (E2E)|Copertura completa e test headless|
|**CI/CD**|GitHub Actions|Lint + test + build automatizzati|
|**Assets**|TexturePacker (atlas), Tiled (.json mappe), audio .ogg/.mp3|Gestione efficiente risorse e compatibilità browser|

![🎯](https://fonts.gstatic.com/s/e/notoemoji/16.0/1f3af/72.png) **Obiettivo tecnico:** Build leggere (<400KB gz), test coverage ≥ 85%, gameplay deterministico e fluido.

---

## ![🏗️](https://fonts.gstatic.com/s/e/notoemoji/16.0/1f3d7_fe0f/72.png) **3. Architettura e Struttura del Codice**

### **Principio chiave:** _Domain-first Architecture_

Il dominio del gioco (regole, entità, AI, punteggio) è completamente **indipendente dal rendering** (Phaser è solo un adapter).

`/src  ├─ /app            → bootstrap, scene registry, dependency injection  ├─ /domain         → ECS, IA, regole, FSM, punteggio  │   ├─ /components  │   ├─ /entities  │   ├─ /systems  │   ├─ /ai  │   └─ /state  ├─ /adapters       → ponte verso librerie esterne  │   ├─ /render     → Phaser renderer  │   ├─ /input      → tastiera, touch, gamepad  │   ├─ /audio  │   └─ /persistence  ├─ /ui             → HUD, menu, overlay  ├─ /config         → costanti e bilanciamento  ├─ /assets         → manifest, loader  └─ /tests          → unit, integration, e2e`

---

## ![🧩](https://fonts.gstatic.com/s/e/notoemoji/16.0/1f9e9/72.png) **4. Pattern Architetturali**

### ![🧱](https://fonts.gstatic.com/s/e/notoemoji/16.0/1f9f1/72.png) **ECS (Entity-Component-System)**

- **Entity:** solo ID (nessuna logica)
    
- **Component:** dati puri
    
- **System:** funzioni pure che leggono/scrivono componenti
    

`// domain/components.ts export type Position = { x: number; y: number }; export type Velocity = { dx: number; dy: number }; export type PacmanTag = {}; export type Ghost = { type: 'blinky'|'pinky'|'inky'|'clyde'; state: GhostState };`

`// domain/systems/movement.ts export function movement(dt: number, world: World) {   for (const e of world.query<Position & Velocity>(['Position','Velocity'])) {     e.x += e.dx * dt;     e.y += e.dy * dt;   } }`

![✅](https://fonts.gstatic.com/s/e/notoemoji/16.0/2705/72.png) **Vantaggi:** Composizione, testabilità, riuso logico, separazione chiara da Phaser.

---

### ![🔁](https://fonts.gstatic.com/s/e/notoemoji/16.0/1f501/72.png) **FSM (Finite State Machine)**

Utilizzata per:

- Stati di gioco globali (Menu → Playing → Paused → GameOver)
    
- Stati dei fantasmi (Scatter / Chase / Frightened / Eaten)
    

`type GhostState = 'scatter'|'chase'|'frightened'|'eaten';  const transitions: Record<GhostState, (ctx: Ctx) => GhostState> = {   scatter: ctx => ctx.tScatter.done ? 'chase' : 'scatter',   chase: ctx => ctx.powerUp.active ? 'frightened' : 'chase',   frightened: ctx => ctx.eaten ? 'eaten' : (ctx.powerUp.active ? 'frightened' : 'chase'),   eaten: ctx => ctx.atHome ? 'scatter' : 'eaten', };`

---

### ![📡](https://fonts.gstatic.com/s/e/notoemoji/16.0/1f4e1/72.png) **Event Bus**

Sistema di eventi centralizzato per comunicare tra sistemi indipendenti:

`eventBus.emit('PELLET_COLLECTED', { points: 10 }); eventBus.on('LIFE_LOST', updateHUD);`

Permette:

- Nessuna dipendenza diretta tra moduli
    
- Integrazione con HUD e analytics
    

---

### ![🧠](https://fonts.gstatic.com/s/e/notoemoji/16.0/1f9e0/72.png) **Strategy Pattern (IA Fantasmi)**

Ogni fantasma implementa una strategia di _targeting_ diversa:

|Fantasma|Strategia|
|---|---|
|**Blinky**|Segue posizione di Pac-Man|
|**Pinky**|Anticipa 4 tile nella direzione di Pac-Man|
|**Inky**|Calcola vettore tra Blinky e Pac-Man|
|**Clyde**|Alterna comportamento se vicino/lontano|

---

### ![🎮](https://fonts.gstatic.com/s/e/notoemoji/16.0/1f3ae/72.png) **Command Pattern (Input)**

Gestione input tramite comandi accodati:

`inputQueue.push(new TurnLeftCommand());`

➡ Permette replay, remapping e input deterministici.

---

## ![🧰](https://fonts.gstatic.com/s/e/notoemoji/16.0/1f9f0/72.png) **5. Regole di Codice**

|Regola|Descrizione|
|---|---|
|TypeScript strict|Nessun `any` implicito|
|Funzioni pure|Tutta la logica di dominio|
|Niente logica in Phaser objects|Phaser = solo adapter|
|No singletons globali|Usare dependency injection leggera|
|File < 200 righe|SRP (Single Responsibility Principle)|
|Lint + Prettier|Esecuzione automatica pre-push|
|JSDoc|Documentazione API pubbliche|

---

## ![⏱️](https://fonts.gstatic.com/s/e/notoemoji/16.0/23f1_fe0f/72.png) **6. Game Loop e Determinismo**

Pipeline frame:

`1️⃣ Input System 2️⃣ Logic Systems (AI, movimento, collisioni) 3️⃣ Event Bus flush 4️⃣ Rendering (Phaser)`

Tick fisso a 60FPS, `dt` normalizzato in secondi.

---

## ![🧩](https://fonts.gstatic.com/s/e/notoemoji/16.0/1f9e9/72.png) **7. Gestione Collisioni e Tilemap**

- Mappa generata con **Tiled**, layer collisione con bitmask.
    
- Movimento _grid-based_ (snap a centro tile per cambio direzione).
    
- Warp portal (wrap-around).
    
- Bounding box tile-size → accuratezza sufficiente per gameplay 2D.
    

---

## ![💾](https://fonts.gstatic.com/s/e/notoemoji/16.0/1f4be/72.png) **8. Persistenza e Integrazioni**

- **LocalStorage:** preferenze, best score
    
- **Leaderboard API:** `POST /scores`, `GET /leaderboard`
    
- DTO validati con **zod**
    
- HTTPS + rate-limit + validazione anti-cheat
    

---

## ![🧪](https://fonts.gstatic.com/s/e/notoemoji/16.0/1f9ea/72.png) **9. Testing e Qualità**

|Tipo|Strumenti|Obiettivo|
|---|---|---|
|Unit test|Vitest|ECS, FSM, IA|
|Integration|Vitest + mock|Mappa, collisioni, caricamento|
|E2E|Playwright|Flow completo (boot → partita → submit score)|

![✅](https://fonts.gstatic.com/s/e/notoemoji/16.0/2705/72.png) Coverage ≥ 85% su `src/domain`

Esempio:

`it('adds score when collecting pellet', () => {   const world = makeWorldWithPelletAt(10, 10);   collectPelletSystem(world, {x:10, y:10});   expect(world.player.score).toBe(10); });`

---

## ![🧩](https://fonts.gstatic.com/s/e/notoemoji/16.0/1f9e9/72.png) **11. Accessibilità e UX Tecnica**

- Rimappo tasti, pausa con Esc
    
- HUD leggibile (alto contrasto)
    
- Audio toggle + vibrazione mobile opzionale
    
- Safe frame-skip su tab inattivo
    

---

## ![🔐](https://fonts.gstatic.com/s/e/notoemoji/16.0/1f510/72.png) **12. Sicurezza Leaderboard**

- HTTPS + nonce + rate-limit
    
- Heuristica: validazione velocità media e durata partita
    
- Nessun secret nel client
    

---

## ![✅](https://fonts.gstatic.com/s/e/notoemoji/16.0/2705/72.png) **13. Definition of Done**

|Criterio|Requisito|
|---|---|
|Tipi completi|Nessun `any` lasciato|
|Test verdi|Coverage ≥ 85%|
|Performance|Nessun frame < 55 FPS|
|Lint/Format|Zero warning console|
|Doc|Aggiornata (README + JSDoc)|
|Eventi HUD|Emessi e gestiti|

---

## ![🗺️](https://fonts.gstatic.com/s/e/notoemoji/16.0/1f5fa_fe0f/72.png) **14. Roadmap Tecnica (Alto Livello)**

![1️⃣](https://fonts.gstatic.com/s/e/notoemoji/16.0/0031_fe0f_20e3/72.png) **Foundation:** Boot, asset loader, ECS base  
![2️⃣](https://fonts.gstatic.com/s/e/notoemoji/16.0/0032_fe0f_20e3/72.png) **Core Gameplay:** Movimento, tilemap, collisioni, punteggio  
![3️⃣](https://fonts.gstatic.com/s/e/notoemoji/16.0/0033_fe0f_20e3/72.png) **AI:** FSM + strategie fantasma  
![4️⃣](https://fonts.gstatic.com/s/e/notoemoji/16.0/0034_fe0f_20e3/72.png) **UX:** Menu, pausa, game over, audio  
![5️⃣](https://fonts.gstatic.com/s/e/notoemoji/16.0/0035_fe0f_20e3/72.png) **Persistenza:** LocalStorage + leaderboard adapter  
![6️⃣](https://fonts.gstatic.com/s/e/notoemoji/16.0/0036_fe0f_20e3/72.png) **Hardening:** test, profiling, accessibilità, ottimizzazioni

---

## ![📊](https://fonts.gstatic.com/s/e/notoemoji/16.0/1f4ca/72.png) **15. Struttura Dati Principale (runtime)**

`type GameState = {   player: { x: number; y: number; direction: Direction };   ghosts: Ghost[];   pellets: Pellet[];   score: number;   level: number;   gamePhase: 'menu' | 'playing' | 'paused' | 'gameover'; };`

Lo stato viene aggiornato dai sistemi ECS e sincronizzato tramite gli adapter (render, input, audio).

