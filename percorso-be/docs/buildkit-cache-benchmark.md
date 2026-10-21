# BuildKit Cache Mount Benchmark

## Configurazione
- Immagine: node:20.11.1-alpine3.19
- Cache: /root/.npm
- Installazione: npm ci

## Risultati

| Build | Tempo |
|---|---|
| Prima build |0.047 ___ secondi |
| Seconda build | 0.044___ secondi |

## Conclusioni
La seconda build ha impiegato ___ secondi.
La cache npm ha [ridotto / non ha ridotto significativamente]
il tempo di installazione.

I tempi sono stati misurati durante le build locali.
