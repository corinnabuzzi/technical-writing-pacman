# 📅 Verbale Riunione di Stato - 05/11/2025, ore 15:30

**Partecipanti:** Gloria, Nicole, Marialia, Corinna

---

## Decisioni Prese

- **Motore di gioco:** È stato deciso di abbandonare l’approccio con `setInterval()` per il loop di gioco e passare a `requestAnimationFrame()` per migliorare la fluidità e la sincronizzazione dei frame.
    
- **Gestione delle collisioni:** Le collisioni non saranno più calcolate tramite controllo per coordinate discrete (celle), ma con bounding boxes per consentire movimenti più precisi e animazioni fluide.
    
- **Intelligenza dei nemici:** Si introdurrà una semplice logica di _pathfinding_ basata su BFS (Breadth-First Search) per rendere il movimento dei fantasmi meno prevedibile e più realistico.
    
- **Struttura dati della mappa:** Confermata la migrazione da un array bidimensionale statico a una struttura dinamica basata su `Map()` per consentire la personalizzazione dei livelli futuri.
    
- **Interfaccia grafica:** È stato approvato un redesign della UI per includere un contatore di vite e una barra laterale con il punteggio e le informazioni sui potenziamenti.
    
- **Testing e debugging:** Si è deciso di integrare Jest per i test unitari e Playwright per i test end-to-end, in particolare sulle collisioni e sugli eventi di gioco.
    

---

## Action Items (To Do)

|Assegnato a|Compito|Deadline Simulata|
|:--|:--|:--|
|**Gloria**|Implementare il loop di gioco con `requestAnimationFrame()` e ottimizzare la logica di rendering.|08/11/2025|
|**Nicole**|Integrare il sistema di pathfinding BFS nel comportamento dei fantasmi.|10/11/2025|
|**Marialia**|Aggiornare il design della UI con la barra laterale e il nuovo layout del punteggio.|09/11/2025|
|**Corinna**|Scrivere test unitari per le funzioni di collisione e per la gestione delle vite.|11/11/2025|

---

## Note Aggiuntive

- Si valuterà la possibilità di introdurre livelli multipli con difficoltà crescente dopo il completamento della logica di base.
    
- Nella prossima riunione verranno analizzate le performance del motore grafico e la scalabilità della struttura dati `Map()`.
    
- È stato richiesto a tutte di aggiornare la documentazione tecnica in tempo reale su GitHub per mantenere la tracciabilità delle modifiche.
    
