# technical-writing-pacman

## 🎯 Obiettivi del progetto

- Studiare e implementare i **principi fondamentali del game loop** (input → update → render).  
- Ricreare fedelmente le **meccaniche classiche di Pac-Man**: movimento, AI dei fantasmi, raccolta dei punti, power-up.  
- Progettare un **codice modulare e leggibile**, adatto all’estensione futura.  
- Documentare ogni fase del **processo di sviluppo**, con particolare attenzione a:
  - Scelte architetturali
  - Pattern di design adottati
  - Gestione delle risorse (sprite, suoni, mappe)
  - Debug e testing

---

## 🧩 Stack Tecnologico

| Componente | Strumento / Linguaggio | Note |
|-------------|------------------------|------|
| Motore di gioco | Python + Pygame *(o altro motore se differente)* | Implementazione low-level del loop e del rendering |
| Grafica | Sprite 2D | Creazione o riutilizzo di asset open source |
| Audio | WAV/OGG | Effetti sonori classici in stile retrò |
| Gestione del progetto | Git + GitHub | Versionamento, issue tracking e documentazione |
| Documentazione | Markdown | Report di sviluppo e note tecniche |

---

## 🗂️ Struttura della Repository

pacamna/
│
├── src/ # Codice sorgente del gioco
│ ├── core/ # Game loop, gestione input, collisioni
│ ├── entities/ # Pacamna, fantasmi, frutti, ecc.
│ ├── levels/ # Mappe e logica dei livelli
│ └── utils/ # Funzioni di supporto
│
├── assets/ # Sprite, suoni, font
│
├── docs/ # Documentazione tecnica e di sviluppo
│ ├── design_notes.md
│ ├── changelog.md
│ └── roadmap.md
│
├── tests/ # Test automatici e prototipi
│
└── README.md # Questo file

yaml
Copy code

---

## 🧠 Processo di sviluppo

Il progetto segue un approccio **incrementale**:

1. **Prototipo base:** movimento del giocatore e mappa statica.  
2. **Gestione collisioni e punteggio.**  
3. **Implementazione AI dei fantasmi.**  
4. **Power-up e comportamento alternato (caccia/fuga).**  
5. **Ottimizzazione e pulizia del codice.**  
6. **Aggiunta di grafica, audio e UI.**

Ogni fase sarà documentata nei file sotto `docs/`, con spiegazioni sulle scelte implementative e analisi delle alternative scartate.

---
