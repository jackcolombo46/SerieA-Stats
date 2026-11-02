# Serie A Stats

> **Questa app non è più aggiornata.** Ora fa parte di **Sports Stats** (NBA e Serie A in un'unica app):
> https://jackcolombo46.github.io/Sports-Stats/seriea/ · codice: https://github.com/jackcolombo46/Sports-Stats

Web app che stima le statistiche più probabili dei calciatori di Serie A nella prossima partita,
in base all'avversario. Funziona dal telefono e dal computer, senza installare nulla.
È la versione calcio di [NBA Stats](https://github.com/jackcolombo46/NBA-Stats), con la stessa struttura.

- **Giocatore**: cerchi un nome, l'app trova la prossima partita e stima gol, assist, gol+assist, tiri,
  tiri in porta, falli commessi e subiti, probabilità di ammonizione (per i portieri: parate e porta
  inviolata), con la probabilità di superare ogni soglia.
- **Squadra**: gol attesi, probabilità 1-X-2, Over 2,5 e Gol/Gol; per ogni statistica chi è più probabile
  che la faccia (anche tra gli avversari).
- **Classifica**: calcolata dai risultati, con la forma delle ultime 5. Tocca una squadra per le proiezioni.
- **Osservati**: i giocatori che segui, con il tabellino dell'ultima partita (o di quella in corso).
- Avvisi automatici: diffidati, probabili squalifiche (rosso o 5ª/10ª/14ª ammonizione), assenze,
  infortuni quando ESPN li segnala, cambio di squadra.

## Come funziona

```
GitHub Actions (3 volte al giorno)        GitHub Pages
tools/update_data.py  ── ESPN ──►  docs/data/*.json  ──►  docs/index.html + model.js
                                                           (i calcoli girano nel browser)
```

- `tools/update_data.py` scarica da ESPN (campionato `ita.1`) partite, tabellini dei giocatori, risultati,
  calendario delle prossime 3 settimane e rose. La prima volta scarica anche le 2 stagioni precedenti
  (pochi minuti), poi solo le partite nuove. I minuti giocati vengono ricavati da titolari, sostituzioni
  ed espulsioni.
- `docs/model.js` è il modello: rendimento ogni 90 minuti pesato sulle partite recenti × minuti attesi ×
  quanto concede l'avversario rispetto alla media × fattore campo + precedenti diretti. Chi ha giocato poco
  viene avvicinato alla media del suo ruolo. Le probabilità usano Poisson / binomiale negativa; il risultato
  della partita usa i gol attesi delle due squadre.
- `docs/app.js` è l'interfaccia. All'apertura scarica direttamente da ESPN le partite finite
  dopo l'ultimo aggiornamento automatico.

### Verso un'app multi-sport

Tutto ciò che è specifico del calcio nel modello sta nell'oggetto `SPORT` in `docs/model.js`
(statistiche, etichette, soglie, regole delle squalifiche); lo scaricatore ha il campionato in `LEAGUE`.
Cambiando `LEAGUE` (es. `eng.1`, `esp.1`) funziona già per altri campionati di calcio ESPN.

## Prima configurazione

1. **Settings → Pages**: Source "Deploy from a branch", branch `main`, cartella `/docs`.
2. **Actions → Aggiorna dati Serie A → Run workflow** per il primo scaricamento (parte anche da solo al primo push).
3. L'app sarà su `https://jackcolombo46.github.io/SerieA-Stats/`.

Gli aggiornamenti programmati si fermano se il repository resta senza attività per 60 giorni
(es. in estate): basta riattivarli dalla tab Actions.

## Limiti

ESPN non ha un'API ufficiale: se cambia formato, `tools/update_data.py` va aggiornato.
Il modello non conosce formazioni ufficiali, turnover, decisioni del giudice sportivo o infortuni
non segnalati: le proiezioni valgono se il giocatore gioca i minuti attesi.
