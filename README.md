# Lenny's Podcast Summarizer

Cron GitHub Actions che ogni ora controlla il canale YouTube di Lenny's Podcast, riassume i nuovi episodi via Claude Sonnet 4.5 e manda la sintesi via mail.

## Architettura

```
GitHub Actions cron (orario)
  → fetch RSS canale YouTube
  → diff vs state.json
  → per ogni nuovo videoId:
      Apify transcript → OpenRouter Sonnet 4.5 → SMTP Gmail
  → commit state.json aggiornato
```

## Secrets richiesti (Settings → Secrets and variables → Actions)

| Nome | Valore |
|---|---|
| `APIFY_TOKEN` | Apify API token (apify.com → Settings → Integrations) |
| `OPENROUTER_KEY` | OpenRouter API key (openrouter.ai → Keys) |
| `GMAIL_USER` | Indirizzo Gmail mittente (es. `pierpaolo.maggio84@gmail.com`) |
| `GMAIL_APP_PASSWORD` | App password Gmail dedicata (16 caratteri, generata su [myaccount.google.com/apppasswords](https://myaccount.google.com/apppasswords) — richiede 2FA attivo) |

## Primo avvio

1. Push del repo con i secrets configurati.
2. Vai su Actions → "Lenny's Podcast Summarizer" → Run workflow (manuale).
3. La prima run **non manda mail**: registra in `state.json` tutti i videoId presenti nel feed RSS come "già visti" (seed). Da quel momento processa solo i nuovi.

## Costo

- Claude Sonnet 4.5 input/output: ~$0.12/episodio
- Apify transcript: ~$0.0004/episodio
- GitHub Actions: minuti gratuiti (~30s/run × 24 run/giorno = ~360 min/mese, sotto i 2000 free)
- Gmail SMTP: gratis

A regime (~2-3 episodi/settimana): **~$1/mese**.

## Modifiche

Il prompt sta in `scripts/summarize.mjs` nella funzione `summarize()`. Per cambiare modello, modifica il campo `model` nel body OpenRouter. Per cambiare destinatario o canale, modifica le costanti in cima al file.
