Kubernetes - Backend Lipari
Scopo

Questi manifest descrivono il deployment Kubernetes del backend Node.js. Sono manifest di esempio: non vengono applicati a un cluster in questo esercizio.

Componenti
Deployment: mantiene tre repliche del backend.
Pod: ospita il container Node.js.
Service: espone il backend sulla porta 80 nel cluster.
ConfigMap: contiene la configurazione non sensibile.
Flusso del traffico
L'utente invia una richiesta HTTP.
In produzione, un Ingress o un load balancer riceve la richiesta.
L'ingresso inoltra la richiesta al Service Kubernetes.
Il Service ClusterIP riceve il traffico sulla porta 80.
Il Service seleziona i Pod con etichetta app=lipari-be.
Kubernetes inoltra la richiesta alla porta 3000 del Pod.
Il container Node.js riceve ed elabora la richiesta.
L'applicazione può comunicare con Redis tramite REDIS_URL.
La risposta torna al client attraverso il percorso di rete.
Health check

La livenessProbe controlla se l'applicazione è viva. La readinessProbe controlla se il Pod può ricevere traffico. L'endpoint /healthz deve avere un comportamento coerente con le probe.

Risorse

Le requests indicano le risorse necessarie per pianificare il Pod. I limits definiscono i limiti massimi di CPU e memoria.

Problemi prima della produzione
Il registry GHCR privato richiede imagePullSecrets.
Manca un Ingress per esporre il servizio all'esterno.
I segreti devono essere conservati in Kubernetes Secrets o in un gestore dedicato.
Manca un PodDisruptionBudget per le interruzioni volontarie.
Il servizio Redis chiamato cache deve esistere nel cluster.
Le probe devono essere verificate rispetto al comportamento reale di /healthz.
Immagine

Il Deployment usa un tag SHA per identificare una build precisa. Il tag deve corrispondere a un'immagine realmente pubblicata su GHCR.

Applicazione

Questo esercizio richiede solo la lettura e la validazione dei manifest. Non eseguire kubectl apply.
