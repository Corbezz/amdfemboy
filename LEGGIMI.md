# Configuratore PC con prezzi automatici

## Stato: già configurato sul tuo Supabase
- Tabelle create: `parts` (78 componenti), `part_sources`, `price_history`, `builds`, `app_config`.
- Funzione `update-prices` pubblicata. Parte ogni 30 minuti e controlla i 40 prezzi controllati meno di recente.
- Il sito (`index.html`) è già collegato: legge i prezzi da Supabase e salva le build in `builds`.

## Cosa resta da fare a te
1. **Pubblicare il sito**: carica `index.html` (e `prices.json`, che resta come copia di riserva) su GitHub, poi collega il repository a Vercel.
2. **Aggiungere le pagine dei negozi**: finché `part_sources` è vuota, i prezzi non si muovono. Per ogni componente, uno o più link di negozi italiani:
   ```sql
   insert into part_sources (part_id, url, store)
   select id, 'https://www.negozio.it/pagina-del-prodotto', 'Nome negozio'
   from parts where name = 'AMD Ryzen 5 7600';
   ```
   (da Supabase → SQL Editor, oppure dal Table Editor, tabella `part_sources`).
3. **Impostare i tuoi dati** in `app_config` (Table Editor):
   - `bot_contact`: la tua email, che i negozi vedono nel nome del programma. Ora c'è `info@example.com`.
   - `markup_percent`: il tuo ricarico in % sul prezzo del negozio (ora 0).

## Dove vedi le cose (Table Editor di Supabase)
- `builds`: le build inviate dai clienti, con pezzi, prezzi, totale e contatto. Cambia `status` per seguirle.
- `parts`: prezzi e componenti. Puoi cambiare un prezzo a mano o aggiungere pezzi. Compila `specs` (es. `{"sock":"AM5","w":65}`), altrimenti compatibilità e build automatica non li usano bene.
- `part_sources`: `last_price` e `last_error` ti dicono se la lettura di una pagina funziona.
- `parts.needs_review`: avviso quando un prezzo cambia di oltre il 50%, che il sistema non applica da solo.

## Controlli e limiti
- Legge i dati strutturati (schema.org) delle pagine prodotto, o in alternativa il tag meta del prezzo.
- Rispetta `robots.txt`, aspetta 1,5 secondi tra le richieste, e ignora prezzi non riconfermati da più di 3 giorni.
- **Non ho potuto provarla su negozi veri**: l'ho collaudata solo a vuoto (risponde correttamente). Dopo aver aggiunto i primi link, controlla `last_price` e `last_error`.
- Molti negozi vietano nei loro termini la lettura automatica: leggi quelli che scegli.
