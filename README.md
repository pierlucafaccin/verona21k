# Verona 21K — piano di allenamento (PWA)

App web installabile con il piano di allenamento e il diario delle corse verso la
**Verona Run Marathon 21K del 15 novembre 2026**.

Funziona offline, si installa sulla home screen dell'iPhone e salva i dati in locale.

---

## Deploy su GitHub Pages

1. **Crea il repo** (pubblico o privato — Pages funziona su privato solo con piano a pagamento):

   ```bash
   git init
   git add .
   git commit -m "Piano allenamento Verona 21K"
   git branch -M main
   git remote add origin https://github.com/<tuo-utente>/verona21k.git
   git push -u origin main
   ```

2. **Attiva Pages**: repo → *Settings* → *Pages* → Source: `Deploy from a branch`,
   Branch: `main`, folder: `/ (root)` → *Save*.

3. Dopo 1-2 minuti l'app è su:
   `https://<tuo-utente>.github.io/verona21k/`

   HTTPS è automatico, ed è **obbligatorio** perché il service worker funzioni.

> I percorsi sono tutti relativi (`./`), quindi funziona anche in una sottocartella
> come quella dei project site di GitHub Pages. Non serve configurare nulla.

---

## Installazione su iPhone

1. Apri l'URL **in Safari** (non Chrome: l'Aggiungi a Home è più affidabile da Safari).
2. Tasto *Condividi* → **Aggiungi a Home**.
3. Conferma il nome ("Verona 21K") → *Aggiungi*.

Da quel momento si apre a tutto schermo, senza barra del browser, e funziona anche
senza rete. Su Android: Chrome mostra direttamente il prompt di installazione.

---

## Aggiornare l'app dopo una modifica

Il service worker usa una strategia *network-first* sull'HTML, quindi una nuova
versione viene raccolta al primo avvio con rete. Se cambi asset (icone, manifest),
**incrementa `CACHE_VERSION` in `sw.js`**:

```js
const CACHE_VERSION = 'v2';   // era v1
```

Poi `git push`. Senza questo, i file statici restano quelli in cache.

---

## Dove finiscono i dati — leggi questo

| | |
|---|---|
| **Dove** | `localStorage` del browser, sotto il prefisso `verona21k:` |
| **Chiave** | `verona21k:training-log` (JSON con km, ritmo, FC, note, spunte) |
| **Portata** | **Per-dispositivo.** iPhone e PC hanno dati separati. |

Per spostare i dati usa i bottoni **Esporta progressi** / **Importa progressi**
dentro l'app: generano e rileggono un JSON da copiare/incollare.

### Attenzione: `localStorage` su iOS non è eterno

Safari può cancellare lo storage scrivibile da script dopo alcune settimane di
inutilizzo del sito (le regole di Apple sono cambiate più volte e le web app
installate sono trattate meglio dei siti normali, ma non c'è una garanzia
formale). Quindi:

- **fai un Esporta ogni tanto** e salvati il JSON da qualche parte, oppure
- collega un backend vero (sotto).

## Passo 2 opzionale: sync cloud con login

Tutta la persistenza è isolata in un unico adapter in cima a `index.html`
(`window.storage` con `get`/`set`/`delete`/`list`). Per avere sync multi-dispositivo
basta sostituire quel blocco con chiamate a un backend, senza toccare il resto.

Opzione consigliata: **Supabase** (Postgres + Auth, piano gratuito).

```sql
create table training_log (
  user_id uuid references auth.users not null,
  date date not null,
  done boolean default false,
  km numeric,
  pace text,
  hr integer,
  note text,
  updated_at timestamptz default now(),
  primary key (user_id, date)
);
alter table training_log enable row level security;
create policy "solo i propri dati" on training_log
  for all using (auth.uid() = user_id);
```

La chiave `anon public` di Supabase **non è un segreto**: può stare nel codice
client, perché la sicurezza la fa la policy RLS qui sopra, non la chiave.
Auth via magic link evita di gestire password.

---

## File

| File | Ruolo |
|---|---|
| `index.html` | App completa: piano, calendario, diario, export/import. Self-contained. |
| `manifest.webmanifest` | Nome, icone, `display: standalone`. |
| `sw.js` | Service worker: offline + aggiornamenti. |
| `apple-touch-icon.png` | Icona home screen iOS (180×180). |
| `icon-192.png`, `icon-512.png`, `icon-512-maskable.png` | Icone manifest. |
| `favicon-32.png` | Favicon. |

---

## Sviluppo in locale

Il service worker richiede `http://localhost` o HTTPS: aprire il file con
`file://` non basta.

```bash
python3 -m http.server 8000
# poi apri http://localhost:8000
```

---

Il piano è indicativo e non sostituisce il parere di un medico o fisioterapista.
