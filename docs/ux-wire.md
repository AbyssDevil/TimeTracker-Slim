# UX Wire v0 — Slim bar e Menu hover

## Slim bar (finestra principale)
- **Finestra**: frameless, always-on-top, altezza ~48–56px, larghezza ridimensionabile.
- **Layout (da sinistra a destra)**:
  1) **Handle/Menu trigger** (icona ≡, 24px). Hover → apre pannello “Oggi/Da fare”.
  2) **Dim1 Button** (quadrato 18–20px, solo bordi quando OFF; pieno colorato quando ON; tooltip: "Dim1Name: Valore").
  3) **Dim2 Button** (come Dim1).
  4) **Input titolo** (autocomplete; suggerisce recenti e to-do).
  5) **Play/Pausa/Stop** (un solo timer attivo tra tutte le attività).
  6) **Timer doppio**:
     - Elapsed ↑ (mm:ss)
     - Remaining ↓ (mm:ss; se target raggiunto, mostra `-mm:ss` in overrun)
  7) **Chiudi riga** (×: archivia l’attività corrente della riga).
- **Interazioni**:
  - Click su **Dim1** / **Dim2** → dropdown sotto l’input:
    - campo ricerca (focus immediato), lista valori recenti + risultati, pulsante “+ Crea ‘…’”.
    - Navigazione tastiera: ↑/↓, Invio seleziona/crea, Esc chiude.
  - **Play** su una riga:
    - se esiste un’altra riga in running → la chiude (chiusura sessione) e avvia questa.
  - **Pausa** → chiude sessione (start/end) e rimane la riga visibile con stato “pausa”.
  - **Stop** → chiude sessione e setta attività a “stopped”.

## Menu on-hover (pannello a sinistra)
- **Apertura**: hover sull’handle; ritardo apertura ~150–200ms, chiusura ~250ms e su blur.
- **Contenuti**:
  - Tab **Oggi** (default):
    - elenco attività ordinate: running (in alto), poi recenti; ogni riga mostra:
      - icona stato: ● running / ⏸ paused / ◼ stopped / ◻ mai partita
      - titolo
      - badge minuti totali oggi (p.es. “42m”)
      - (opz.) chip Dim1/Dim2 colorati
    - click su riga: attiva l’attività (chiude eventuale running altrove e apre nuova sessione).
  - Tab **Da fare**:
    - lista to-do con ricerca, click = compila titolo e avvia timer.
- **Azioni rapide** (footer del pannello):
  - “+ Nuova attività” → focus su input titolo nella slim bar.
  - “+ Nuovo to-do” → dialog inline minimal (solo titolo).

## Timer proattivo (start dialog)
- Avviando Play su titolo vuoto:
  - inline prompt minimal per **Target** (mm:ss); default vuoto = nessun target.
  - conferma → parte il timer con target associato alla **sessione** corrente.

## Stati e colori
- **Pulsanti Dim1/Dim2**:
  - OFF: bordo 1px (contrasto AA).
  - ON: pieno con colore deterministico (hash label → HSL) + tooltip con “DimName: Valore”.
- **Stato attività**: colori/tinte diverse per ●/⏸/◼/◻ (accessibili).
- **Errori**: toast non invasivi (es. “chiusa sessione precedente e avviata nuova attività”).

## Hotkeys (minime)
- Attiva/porta in primo piano: `Win+Alt+Space`
- Play/Pausa: `Win+Alt+P`
- Nuova attività (focus input): `Win+Alt+N`
- Apri Dim1/Dim2 dropdown: `Alt+1` / `Alt+2`

## Note di implementazione
- Timer vero lato backend (Rust, clock monotono). La UI riceve deltas e li rende.
- Un solo running globale: enforcement a livello DB (transazioni).
- Creazione inline dei valori di classificazione con normalizzazione e dedup (merge suggerito).
