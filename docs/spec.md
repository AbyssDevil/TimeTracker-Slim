# Spec v0 – TimeTracker Slim

## Obiettivo
App desktop “slim bar” per tracciare attività giornaliere con timer proattivo, multi-riga, due classificazioni indipendenti e export mensile (CSV/XLSX).

## Funzioni MVP
- Finestra slim frameless, always-on-top, altezza ~1 riga, larghezza ridimensionabile.
- Riga attività con: input titolo, Play/Pausa/Stop, timer doppio (Elapsed ↑ / Remaining ↓) e chiusura.
- Timer proattivo: all’avvio si può impostare un target; a zero → notifica; se non stoppo continua in **overrun** (Remaining negativo salvato).
- Un solo timer attivo alla volta; avviare un’altra attività chiude la sessione corrente e apre la nuova (operazione atomica).
- Menu on-hover (maniglia a sinistra): liste **Oggi** e **Da fare** con **icona stato** (running/paused/stopped/mai partita) e totale odierno; click = attiva quell’attività.
- Due **classificazioni** indipendenti (nomi configurabili, es. Clienti/Progetti) con **due bottoni quadrati**:
  - spenti = solo contorno; accesi = pieni (colore deterministico); tooltip mostra [Dimensione: Valore].
  - click = dropdown con **ricerca** + **creazione inline** di nuovi valori.
- Export mensile: **CSV + XLSX** con “sessioni raw” e “riepilogo per attività” includendo **Dim1/Dim2**.
- Hotkeys globali base (attiva app, play/pausa, nuova attività).
- Sleep policy configurabile: `auto-pause | keep-running | ask`.

## Dati (SQLite)
- **Activity** { id, title, status, dim1ValueId?, dim2ValueId?, createdAt, updatedAt, defaultTargetSeconds?, notesSummary? }
- **Session** { id, activityId, startAt, endAt, targetSeconds?, elapsedAtStop, overrunSeconds?, note?, source, wasAutoPaused }
- **Todo** { id, title, pinned, sortIndex, createdAt }
- **Attachment** { id, activityId, sessionId?, displayName, storageKind, localPathOrUrl, addedAt }
- **ClassificationDimension** { id, code:DIM1|DIM2, displayName }
- **ClassificationValue** { id, dimensionId, label, slug, isArchived, usageCount, createdAt }
- **Settings** { id, autostart, exportDir, roundToMinutes, theme, sleepPolicy, dim1Name, dim2Name }

## Regole
- Ogni pausa/stop chiude una **Session** (unità base: start/stop).
- Timer vero lato backend (Rust) con clock monotono; la UI mostra deltas.
- Arrotondamento solo in export, mai in DB.
- Creazione valori classificazione inline con normalizzazione (trim, casefold) e dedup.

## Non obiettivi (v0)
- Niente Outlook, sync cloud, allegati/foto: verranno in release successive.
