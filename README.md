# Verona 21K

App installabile con il piano di allenamento e il diario corse verso la
**Verona Run Marathon 21K del 15 novembre 2026**.

Tre schermate: **Oggi** (la seduta del giorno, con il form per registrarla),
**Piano** (navigazione settimana per settimana), **Andamento** (grafici).

I dati restano su questo dispositivo (`localStorage`). Usa la rotellina in alto
a destra per esportarli, importarli o azzerarli.

---

## Deploy su GitHub Pages

```bash
git init
git add .
git commit -m "Verona 21K"
git branch -M main
git remote add origin https://github.com/<tuo-utente>/verona21k.git
git push -u origin main
```

Repo → *Settings* → *Pages* → Source `Deploy from a branch`, Branch `main`,
folder `/ (root)` → *Save*. Dopo 1-2 minuti:
`https://<tuo-utente>.github.io/verona21k/`

HTTPS è automatico ed è obbligatorio per il service worker. I percorsi sono
relativi, quindi la sottocartella non crea problemi.

## Installazione su iPhone

1. Apri l'URL **in Safari**
2. *Condividi* → **Aggiungi a Home**
3. *Aggiungi*

Si apre a tutto schermo, senza barra del browser, e funziona offline.

## Aggiornamenti

L'HTML usa *network-first*: dopo un `git push` la nuova versione arriva al primo
avvio con rete. Se cambi icone o manifest, alza `CACHE_VERSION` in `sw.js`.

## Sviluppo in locale

```bash
python3 -m http.server 8000
# http://localhost:8000
```

`file://` non basta: il service worker richiede localhost o HTTPS.

## File

| File | Ruolo |
|---|---|
| `index.html` | Tutta l'app: piano, diario, grafici. Self-contained. |
| `sw.js` | Service worker (offline + aggiornamenti). |
| `manifest.webmanifest` | Nome, icone, `display: standalone`. |
| `apple-touch-icon.png` | Icona home screen iOS (180×180). |
| `icon-192/512/512-maskable.png`, `favicon-32.png` | Icone e favicon. |

---

Il piano è indicativo e non sostituisce il parere di un medico o fisioterapista.
