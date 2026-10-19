# Mandello Matchday

Pagina web per la squadra **Polisportiva Mandello** (Serie D Femminile, girone C, FIPAV Lombardia).
Mostra la prossima partita con data, orario, avversario e palestra, l'orario di ritrovo
(1 ora e 15 minuti prima dell'inizio) e due pulsanti per aprire il navigatore:
**Google Maps** e **Waze**. Sotto c'è l'elenco delle partite successive.

La pagina passa da sola alla partita successiva 2 ore e 30 minuti dopo l'inizio di quella in corso.

## Come funziona

```
data/calendario_gare.xlsx   ← export del calendario dal portale FIPAV
scripts/converti.py         ← tiene solo le partite del Mandello e crea data/partite.json
data/partite.json           ← i dati letti dalla pagina
index.html                  ← la pagina (nessuna dipendenza, funziona su GitHub Pages)
.github/workflows/          ← rigenera partite.json quando carichi un nuovo Excel
```

I dati arrivano dall'export Excel ufficiale, non da uno scraping del sito:
il servizio calendari FIPAV Lombardia (TieBreakTech) vieta la raccolta automatica dei dati.

## Messa online (una volta sola)

1. Crea un repository su GitHub, per esempio `Partite-Mandello-Serie-D-2026-2027`, e carica tutti i file di questa cartella
   (anche la cartella nascosta `.github`).
2. In **Settings → Pages**, alla voce *Build and deployment* scegli **Deploy from a branch**,
   branch `main`, cartella `/ (root)`, e salva.
3. In **Settings → Actions → General**, sotto *Workflow permissions*, scegli **Read and write permissions**.
4. Dopo un minuto la pagina è su `https://<tuo-utente>.github.io/Partite-Mandello-Serie-D-2026-2027/`.
   Dal telefono puoi aggiungerla alla schermata Home.

## Quando il calendario cambia

Scarica di nuovo l'Excel del girone dal portale FIPAV e caricalo in `data/` con lo stesso nome
(`calendario_gare.xlsx`), sostituendo il vecchio. La GitHub Action rigenera `data/partite.json`
e la pagina si aggiorna da sola.

Per uno spostamento singolo puoi anche modificare direttamente `data/partite.json` dal sito di GitHub:
cambia il campo `inizio` (formato `2026-10-24T21:15`) o la palestra.

## Indirizzi delle palestre

Google Maps cerca nome della palestra + via + città, quindi di solito trova l'impianto giusto.
Waze cerca solo l'indirizzo, e nel calendario FIPAV molte palestre sono senza numero civico.
Per renderle precise, aggiungi una riga in `data/impianti.json`:

```json
"Nome Palestra - Città": { "indirizzo": "Via Esempio 1, Città LC" }
```

oppure le coordinate esatte (da Google Maps: tasto destro sul punto → copia le coordinate):

```json
"Nome Palestra - Città": { "lat": 45.9, "lon": 9.3 }
```

## Personalizzare

- **Anticipo del ritrovo**: in `data/partite.json`, campo `ritrovo_minuti_prima` (oggi 75 = 1h15).
  Per renderlo permanente cambia lo stesso valore in `scripts/converti.py`.
- **Altra squadra**: `python scripts/converti.py data/calendario_gare.xlsx "NOME SQUADRA"`
  e cambia lo stesso nome nel file della GitHub Action.

## Provare in locale

```bash
pip install openpyxl
python scripts/converti.py
python -m http.server 8000   # poi apri http://localhost:8000
```
