
# ![🧠](https://fonts.gstatic.com/s/e/notoemoji/16.0/1f9e0/72.png) Documento di Analisi Funzionale

## Progetto: **Pac-Man Web Remake**

---

## 1. ![🎯](https://fonts.gstatic.com/s/e/notoemoji/16.0/1f3af/72.png) Scopo del Documento

Questo documento descrive **le funzionalità operative, le logiche interne e i flussi di interazione** tra utente e sistema del gioco _Pac-Man Web Remake_.  
Integra il PRD, specificando **cosa deve fare il sistema**, **come deve comportarsi** e **quali dati o eventi determinano le transizioni di stato**.

---

## 2. ![⚙️](https://fonts.gstatic.com/s/e/notoemoji/16.0/2699_fe0f/72.png) Architettura Logica del Sistema

Il gioco è strutturato in **moduli funzionali** principali:

|Modulo|Descrizione|
|---|---|
|**Core Engine**|Gestisce la logica di movimento, collisioni e fisica base.|
|**Game Entities**|Gestisce Pac-Man, fantasmi, punti, frutti, power-up.|
|**AI System**|Implementa i comportamenti differenziati dei fantasmi.|
|**UI / HUD System**|Visualizza punteggio, vite, livello, messaggi.|
|**Audio System**|Gestisce musica, effetti e feedback sonoro.|
|**Persistence Layer**|Salva punteggi e preferenze localmente o online.|
|**Input Manager**|Interpreta i comandi da tastiera o touch.|
|**State Manager**|Coordina gli stati di gioco (menu, partita, pausa, game over).|

---

## 3. ![🕹️](https://fonts.gstatic.com/s/e/notoemoji/16.0/1f579_fe0f/72.png) Flussi Funzionali Principali

### 3.1 Avvio del Gioco

1. L’utente apre la pagina web.
    
2. Il sistema carica risorse grafiche, audio e configurazioni.
    
3. Si presenta la **schermata iniziale** con musica e menu.
    
4. L’utente può scegliere: “Gioca”, “Classifica”, “Impostazioni”, “Guida”.
    

**Output atteso:** visualizzazione interfaccia iniziale pronta all’input.

---

### 3.2 Avvio Partita

1. L’utente seleziona **Gioca**.
    
2. Il sistema:
    
    - Genera o carica la **mappa del livello 1**.
        
    - Inizializza **Pac-Man e fantasmi** nelle posizioni predefinite.
        
    - Aziona timer, punteggio e vite.
        
    - Mostra la schermata di gioco con HUD attivo.
        
3. Inizia il **loop principale** di gioco.
    

**Output atteso:** partita avviata, personaggi in movimento, punteggio a 0.

---

### 3.3 Movimento e Collisioni

- Il **Player Input** aggiorna la direzione desiderata.
    
- L’engine verifica se la cella successiva è libera:
    
    - Se sì → aggiorna posizione.
        
    - Se no → mantiene posizione attuale.
        
- Collisioni rilevate tramite distanza o intersezione tile:
    
    - **Punto** → incremento punteggio (+10), rimozione punto.
        
    - **Power Pellet** → cambio stato IA (fuga).
        
    - **Fantasma (attivo)** → perdita vita.
        
    - **Fantasma (in fuga)** → punteggio bonus.
        

**Output atteso:** feedback immediato visivo e sonoro.

---

### 3.4 Gestione Stati di Gioco

|Stato|Evento di Entrata|Evento di Uscita|Azioni|
|---|---|---|---|
|**Menu**|Avvio app|Click “Gioca”|Caricamento mappa|
|**In Gioco**|Avvio livello|Tutti i punti raccolti / vite = 0|Transizione a Next Level o Game Over|
|**Pausa**|Input ESC/P|ESC/P|Sospensione loop e audio|
|**Game Over**|Vite esaurite|Click “Riprova” / “Menu”|Mostra punteggio finale|
|**Victory**|Tutti i livelli completati|Input utente|Mostra schermata finale|

---

### 3.5 Logiche dei Fantasmi

Ogni fantasma ha:

- **Target dinamico (coordinate x,y)** che varia secondo lo stato.
    
- **Stati principali:**
    
    - _Chase (caccia)_ → segue Pac-Man.
        
    - _Scatter (pattugliamento)_ → torna nell’area designata.
        
    - _Frightened (fuga)_ → si allontana da Pac-Man.
        
    - _Eaten (respawn)_ → ritorna alla base.
        
- Le transizioni tra stati sono temporizzate o dipendono da eventi (power-up, timer, distanza).
    

---

### 3.6 Punteggio e Progressione

- Ogni evento aggiorna il punteggio globale.
    
- Superata una soglia o raccolti tutti i punti → livello successivo.
    
- A ogni livello:
    
    - Velocità di Pac-Man e fantasmi aumenta.
        
    - Durata del power-up diminuisce.
        
    - Layout mappa può cambiare.
        

**Condizione di vittoria:** tutti i livelli completati o punteggio massimo raggiunto.  
**Condizione di sconfitta:** vite = 0.

---

## 4. ![💾](https://fonts.gstatic.com/s/e/notoemoji/16.0/1f4be/72.png) Dati e Persistenza

### 4.1 Strutture Dati Principali

```
{
  "player": { "x": 120, "y": 240, "lives": 3, "score": 540 },
  "ghosts": [
    {"id": "blinky", "state": "chase", "x": 200, "y": 150},
    {"id": "pinky", "state": "scatter", "x": 220, "y": 150}
  ],
  "map": { "tiles": [...], "points": [...], "powerUps": [...] },
  "settings": { "volume": 0.8, "theme": "retro" }
}
```

### 4.2 Persistenza Locale

- `localStorage` → salvataggio punteggio massimo e impostazioni.
    
- `sessionStorage` → stato temporaneo della partita (non persistente).
    

### 4.3 Leaderboard Online (opzionale)

- API REST:
    
    - `POST /score` → invio punteggio.
        
    - `GET /leaderboard` → recupero top 10.
        
- Dati inviati: `{ username, score, date }`.
    

---

## 5. ![🎨](https://fonts.gstatic.com/s/e/notoemoji/16.0/1f3a8/72.png) Interfaccia e UX

### 5.1 Obiettivi UX

- Feedback immediato (audio/visivo).
    
- Comandi responsivi (latenza < 50 ms).
    
- Navigazione intuitiva e coerente.
    

### 5.2 Layout Principali

|Schermata|Elementi|Azioni Utente|
|---|---|---|
|**Menu**|Logo, pulsanti, animazione|Click / Tap|
|**Gioco**|Mappa, HUD, Pac-Man, fantasmi|Controlli direzionali|
|**Pausa**|Overlay semitrasparente, opzioni|Riprendi / Menu|
|**Game Over**|Punteggio finale, pulsanti|Riprova / Menu|
|**Leaderboard**|Lista punteggi|Scorrimento / Chiudi|

---

## 6. ![🧩](https://fonts.gstatic.com/s/e/notoemoji/16.0/1f9e9/72.png) Requisiti Funzionali

|ID|Nome Funzione|Descrizione|Priorità|
|---|---|---|---|
|F1|Movimento Pac-Man|Muovere il personaggio in 4 direzioni|Alta|
|F2|Collisione Muri|Impedire attraversamento delle pareti|Alta|
|F3|Raccolta Oggetti|Incremento punteggio e rimozione oggetto|Alta|
|F4|Logica Fantasmi|IA di inseguimento e fuga|Alta|
|F5|Power-Up|Stato temporaneo con vantaggi|Media|
|F6|Punteggio e HUD|Aggiornamento in tempo reale|Alta|
|F7|Gestione Vite / Game Over|Controllo vite residue|Alta|
|F8|Leaderboard|Invio e visualizzazione punteggi|Media|
|F9|Salvataggi Locali|Memorizzazione punteggio e preferenze|Media|
|F10|Audio e Musica|Effetti e controlli volume|Bassa|

---

## 7. ![🧠](https://fonts.gstatic.com/s/e/notoemoji/16.0/1f9e0/72.png) Requisiti Non Funzionali

|Categoria|Requisito|
|---|---|
|**Performance**|60 FPS costanti, caricamento < 2 s|
|**Compatibilità**|Browser moderni, responsive mobile|
|**Accessibilità**|Colori leggibili, controlli rimappabili|
|**Manutenibilità**|Codice modulare, documentazione inline|
|**Scalabilità**|Supporto per nuovi livelli e IA aggiuntive|
|**Sicurezza**|Validazione input e limiti API leaderboard|

---

## 8. ![🧩](https://fonts.gstatic.com/s/e/notoemoji/16.0/1f9e9/72.png) Diagramma di Flusso (Sintesi)

```
flowchart TD
    A[Menu Principale] -->|Gioca| B[Inizio Partita]
    B --> C[Loop di Gioco]
    C --> D{Collisione con Fantasma?}
    D -->|Sì| E[Perdita Vita]
    E -->|Vite > 0| B
    E -->|Vite = 0| F[Game Over]
    D -->|No| G{Tutti i punti raccolti?}
    G -->|Sì| H[Livello Successivo]
    G -->|No| C
    H --> B
    F -->|Riprova| B
    F -->|Menu| A
```

---

## 9. ![✅](https://fonts.gstatic.com/s/e/notoemoji/16.0/2705/72.png) Criteri di Accettazione

- Tutte le funzionalità F1–F7 completate e testate.
    
- Gioco stabile ≥ 60 FPS in ambiente browser.
    
- Nessun bug critico durante 10 min di gioco continuo.
    
- Leaderboard funzionante con almeno 10 punteggi registrati.
    
- UX validata tramite sessioni di playtest.
    

---

## 10. ![🏁](https://fonts.gstatic.com/s/e/notoemoji/16.0/1f3c1/72.png) Conclusione

Il presente documento definisce **le specifiche funzionali e operative** necessarie per realizzare un _Pac-Man Web Remake_ fedele, stabile e moderno.  
Costituisce la base per le fasi successive di **progettazione tecnica, sviluppo e testing**.
