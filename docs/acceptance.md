# Acceptance v0 — Criteri di collaudo MVP

## A. Requisiti funzionali
1) **Slim bar**: finestra frameless, always-on-top, altezza ~1 riga; ridimensionabile orizzontalmente.
2) **Riga attività**: input titolo + Play/Pausa/Stop + Timer doppio (Elapsed ↑ / Remaining ↓) + chiudi riga.
3) **Timer proattivo**: all’avvio posso impostare target; a zero → notifica; se non stoppo, Remaining va < 0 e salvo `overrunSeconds`.
4) **Un solo timer attivo**: avviare una nuova attività chiude la sessione corrente (operazione atomica) e apre la nuova.
5) **Menu on-hover (testa sinistra)**:
   - Liste **Oggi** e **Da fare**.
   - **Icona stato** per ogni attività: running / paused / stopped / mai partita.
   - Totale odierno per riga; click = attiva quell’attività.
6) **Classificazioni**: due pulsanti quadrati a sinistra del titolo.
   - Spenti = solo contorno; accesi = pieni (colore deterministico) con **tooltip** [Dim: Valore].
   - Click = dropdown con **ricerca progressiva** e **creazione inline** (no pannello dedicato).
7) **Hotkeys globali** minime: attiva app, play/pausa, nuova attività.
8) **Sleep policy** configurabile: `auto-pause | keep-running | ask` con tracciamento `wasAutoPaused`.

## B. Dati & Persistenza (SQLite)
9) **Sessioni**: ogni Pausa/Stop crea una Session chiusa {startAt, endAt}; `targetSeconds` e `overrunSeconds` memorizzati se presenti.
10) **Export**: CSV + XLSX mese corrente con:
    - Foglio “Sessioni”: Data, OraStart, OraEnd, DurataMin, OverrunMin, Attività, Dim1, Dim2, Note.
    - Foglio “Riepilogo”: Attività, Dim1, Dim2, TotMin, N.Sessioni, TotOverrun.

## C. Non regressione (manuale)
- Avvio app → nessun timer attivo; avvio attività A con target 5m → scade, notifica, continuo 1m in overrun → stop: `overrunSeconds ≈ 60`.
- Avvio attività B mentre A è running → A viene chiusa e B parte; non esistono due running contemporanee.
- Dropdown classificazioni: ricerca “cli…”, crea voce nuova, seleziona; tooltip mostra la coppia Dimensione:Valore.
