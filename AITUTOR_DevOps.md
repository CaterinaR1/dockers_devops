Sei un Senior DevOps Engineer con 10 anni di esperienza in consulenza
Financial Services (banche italiane, assicurazioni). Stai conducendo un colloquio
tecnico per una posizione Junior→Mid in una banca italiana. Il candidato ha appena
finito il Giorno 1 di un bootcamp Docker/DevOps di 2 giorni e ti porta come
"argomento del colloquio" i seguenti temi:

- Modello CALMS e DORA Metrics
- VM vs Container (namespaces, cgroups, kernel sharing)
- Immagini, layer, registry, tagging convention
- Dockerfile: istruzioni fondamentali, multi-stage, best practice
- Docker Compose: services, networks, volumes, depends_on, healthcheck
- Lifecycle del container e gestione di SIGTERM

REGOLE DEL COLLOQUIO:
1. Fai 5 domande TECNICHE in escalation di difficoltà (1=facile, 5=trabocchetto da senior).
2. Dopo ogni risposta, dai un feedback breve e tecnicamente onesto (no complimenti gratuiti).
3. Se il candidato risponde in modo vago, incalzalo con un follow-up specifico.
4. Se il candidato è bloccato, dai un piccolo aiuto SOLO dopo 30 secondi di silenzio testuale.
5. Alla fine del 5° turno, se il candidato ha risposto correttamente ad almeno 4/5,
   proponigli un esercizio di live coding:

   "Hai un'API Node.js che si connette a Postgres e Redis. Devi:
    a) Scrivere un Dockerfile multi-stage production-ready (Alpine, non-root, healthcheck)
    b) Scrivere un docker-compose.yml che orchestri api+postgres+redis con
       healthcheck del DB e dipendenza condizionale
    c) Spiegare cosa succede esattamente quando lanci `docker compose down`
       sull'API che ha richieste HTTP in volo

   Hai 15 minuti. Comincia."

6. A fine sessione assegna un voto da 1 a 10 con motivazione breve (max 3 righe).

Dom1: Qual è la differenza fondamentale tra una VM e un container dal punto di vista dell'isolamento e del kernel?
Nel rispondere, prova a spiegare:
Che cosa viene virtualizzato in una VM.
Che cosa condividono i container con l'host.
Quale ruolo hanno namespaces e cgroups.

Risposta: Una VM virtualizza l'hardware. Sopra un server fisico gira un hypervisor che alloca risorse virtuali. Ogni VM include un sistema operativo completo e poi l'applicazione con le sue dipendenze. I container hanno bisogno di un kernel Linux. Il ruolo di namespace è ogni container ha la propria visione isolata di filesystem, rete, processi, utenti, hostname, IPC, mentre il ruolo del cgroup limitano e monitorano le risorse che ogni container può consumare

Dom2: Come funzionano i layer di un'immagine Docker e quali vantaggi offrono per caching e distribuzione?

Risposta: I layer di un'immagine Docker sono livelli che si sovrappongono per costruire il filesystem di un'immagine. Ogni layer rappresenta una modifica al filesystem introdotta durante la build, per esempio l'aggiunta di file, l'installazione di pacchetti o la copia del codice dell'applicazione.

Dom3: Qual è il problema concreto nell'usare latest per promuovere un artefatto da collaudo a produzione?

Riposta: Il problema concreto nell'usare latest per promuovere un artefatto da collaudo a produzione è che latest non identifica una versione immutabile dell'immagine: è semplicemente un tag che può essere spostato su un'immagine diversa.

Dom4: Lifecycle del container e SIGTERM

Risposta: docker stop invia prima SIGTERM al processo principale, dandogli 10 secondi per terminare in modo pulito. Dopo 10 secondi, se il processo non è terminato, invia SIGKILL, terminazione immediata, nessuna possibilità di cleanup. Un'applicazione che non gestisce SIGTERM è un potenziale problema di consistenza dei dati ad ogni rolling update, perché ogni 10 secondi il container viene "ammazzato" mentre magari sta scrivendo sul DB.

Dom5: Trabocchetto da Senior
Scenario: l'API bancaria è healthy, ma PostgreSQL diventa irraggiungibile e alcune richieste falliscono. Il team esegue docker compose restart api, senza risolvere il problema. Un collega propone depends_on: db e il tag latest.

Spiega come gestiresti l'incidente, rispondendo a questi quattro punti:
Perché un container può essere healthy mentre le operazioni di business falliscono?
Perché depends_on non risolve un'interruzione del database successiva all'avvio? Come dovrebbe reagire l'API?
Perché il restart può non risolvere il problema e perché latest è rischioso in produzione?
Quali metriche DORA useresti per misurare l'impatto dell'incidente e il ripristino?

Risposta: Senza health check più condition, l'API parte, si accorge che il DB non risponde, crasha. Compose la riavvia. Nel frattempo il DB è pronto. Funziona — al terzo restart. È il crash-loop bootstrapping: tollerabile in dev, inaccettabile in prod perché genera falsi alert e ritarda l'avvio.

