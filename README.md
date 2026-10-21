# Code Blueprint G1 — Percorso BE

App campione: REST API Node.js + Postgres + Redis.

## Setup

Pre-requisito: Docker Desktop o Docker Engine installato.

```bash
docker compose up -d --build
docker compose ps
```

## Smoke test

```bash
curl http://localhost:3000/healthz
# atteso: {"status":"ok","deps":["db","cache"]}

curl http://localhost:3000/accounts
# atteso: array di 3 accounts dal DB
```

## Tear down

```bash
docker compose down            # mantiene il volume DB
docker compose down -v         # rimuove anche il volume DB
```

## Struttura

```
g01_be/
├── docker-compose.yml
├── README.md
├── .gitignore
└── percorso-be/
    ├── Dockerfile
    ├── .dockerignore
    ├── package.json
    ├── package-lock.json
    ├── db/
    │   └── init.sql
    └── src/
        └── index.js
```

## Cosa imparare leggendo questo blueprint

- Il `Dockerfile` è multi-stage (`builder` + `runtime`) — vedi sezione Deep Dive del Giorno 1
- `USER node` prima di `CMD` (no root in produzione)
- `HEALTHCHECK` definito dentro l'immagine
- `CMD ["node", "src/index.js"]` in **exec form** per gestione corretta SIGTERM
- Compose ha `healthcheck:` su Postgres e `depends_on: condition: service_healthy` sull'API
- Il volume `postgres_data` persiste i dati anche dopo `docker compose down` (senza `-v`)
- `init.sql` viene eseguito al primo avvio del container Postgres
- L'API gestisce `SIGTERM` chiudendo gracefully connessioni DB e Redis

## Avvia il project work

Quando hai finito di leggere il blueprint, procedi al `project_work_esercizi_studente.md` per gli esercizi 🟢 CORE del Giorno 1.
