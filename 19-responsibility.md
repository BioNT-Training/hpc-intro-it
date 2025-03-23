---
title: Usare le risorse condivise in modo responsabile
teaching: 15
exercises: 5
---




::::::::::::::::::::::::::::::::::::::: objectives

- Descrivere come le azioni di un singolo utente possono influenzare l'esperienza degli altri su un sistema condiviso.
- Discutere il comportamento di un premuroso cittadino del sistema condiviso.
- Spiegare l'importanza del backup dei dati critici.
- Descrivere le sfide legate al trasferimento di grandi quantità di dati dai sistemi HPC.
- Convertire molti file in un singolo file di archivio usando tar.

::::::::::::::::::::::::::::::::::::::::::::::::::

:::::::::::::::::::::::::::::::::::::::: questions

- Come posso essere un utente responsabile?
- Come posso proteggere i miei dati?
- Come posso ottenere grandi quantità di dati da un sistema HPC?

::::::::::::::::::::::::::::::::::::::::::::::::::

Una delle principali differenze tra l'uso di risorse HPC remote e il proprio sistema (ad esempio il portatile) è che le risorse remote sono condivise. Il numero di utenti tra i quali la risorsa è condivisa varia da sistema a sistema, ma è improbabile che siate l'unico utente connesso o che utilizzi un sistema di questo tipo.

L'uso diffuso di sistemi di schedulazione in cui gli utenti inviano lavori su risorse HPC è un risultato naturale della natura condivisa di queste risorse. Ci sono altre cose che, in quanto membro integerrimo della comunità, dovete considerare.

## Sii gentile con i nodi di accesso

Il nodo di login è spesso impegnato a gestire tutti gli utenti connessi, a creare e modificare file e a compilare software. Se la macchina esaurisce la memoria o la capacità di elaborazione, diventa molto lenta e inutilizzabile per tutti. Sebbene la macchina sia destinata a essere utilizzata, assicuratevi di farlo in modo responsabile, ovvero senza influire negativamente sull'esperienza degli altri utenti.

I nodi di accesso sono sempre il posto giusto per lanciare i lavori. Le politiche dei cluster variano, ma possono anche essere usati per provare i flussi di lavoro e, in alcuni casi, possono ospitare strumenti avanzati di debug o di sviluppo specifici per il cluster. Il cluster può avere moduli che devono essere caricati, magari in un certo ordine, e percorsi o versioni di librerie che differiscono dal vostro portatile; fare un test interattivo sul nodo principale è un modo rapido e affidabile per scoprire e risolvere questi problemi.

::::::::::::::::::::::::::::::::::::::::: callout

## I nodi di login sono una risorsa condivisa

Ricordate che il nodo di login è condiviso con tutti gli altri utenti e le vostre azioni potrebbero causare problemi ad altre persone. Pensate bene alle potenziali implicazioni dell'emissione di comandi che possono utilizzare grandi quantità di risorse.

Non siete sicuri? Chiedete al vostro amichevole amministratore di sistema ("sysadmin") se la cosa che state pensando di fare è adatta al nodo di login, o se c'è un altro meccanismo per farlo in modo sicuro.


::::::::::::::::::::::::::::::::::::::::::::::::::

È sempre possibile usare i comandi `top` e `ps ux` per elencare i processi in esecuzione sul nodo di accesso, insieme alla quantità di CPU e memoria che stanno utilizzando. Se questo controllo rivela che il nodo di login è inattivo, si può tranquillamente usarlo per le attività di elaborazione non routinarie. Se qualcosa va storto (il processo impiega troppo tempo o non risponde), si può usare il comando `kill` insieme al *PID* per terminare il processo.

::::::::::::::::::::::::::::::::::::::: challenge

## Galateo del nodo di accesso

Quale di questi comandi sarebbe un'operazione di routine da eseguire sul nodo di login?

1. `python physics_sim.py`
2. `make`
3. `create_directories.sh`
4. `molecular_dynamics_2`
5. `tar -xzf R-3.3.0.tar.gz`

::::::::::::::: solution

## Soluzione

La creazione di software, la creazione di directory e il disimballaggio di software sono compiti comuni e accettabili per il nodo di login: le opzioni #2 (`make`), #3 (`mkdir`) e #5 (`tar`) probabilmente vanno bene. Si noti che i nomi degli script non sempre riflettono il loro contenuto: prima di lanciare il #3, si consiglia di usare `less create_directories.sh` e assicurarsi che non sia un cavallo di Troia.

L'esecuzione di applicazioni ad alta intensità di risorse è sconsigliata. A meno che non siate sicuri che non influisca sugli altri utenti, non eseguite lavori come il #1 (`python`) o il #4 (codice MD personalizzato). Se non si è sicuri, chiedere consiglio al proprio sysadmin.



:::::::::::::::::::::::::

::::::::::::::::::::::::::::::::::::::::::::::::::

Se si riscontrano problemi di prestazioni con un nodo di login, è necessario segnalarlo al personale del sistema (di solito tramite l'helpdesk) affinché indaghi.

## Test prima di scalare

Ricordate che in genere l'utilizzo dei sistemi condivisi viene addebitato. Un semplice errore in uno script di lavoro può finire per costare una grande quantità di budget di risorse. Immaginate uno script di lavoro con un errore che lo fa stare fermo per 24 ore su 1000 core o uno in cui avete richiesto per errore 2000 core e ne usate solo 100! Questo problema può essere aggravato quando si scrivono script che automatizzano l'invio di lavori (ad esempio, quando si esegue lo stesso calcolo o analisi su molti parametri o file diversi). Quando ciò accade, si danneggia sia l'utente (che spreca molte risorse caricate) sia gli altri utenti (che sono bloccati dall'accesso ai nodi di calcolo inattivi). Su risorse molto trafficate, potreste aspettare molti giorni in coda e il vostro lavoro potrebbe fallire entro 10 secondi dall'avvio a causa di un banale errore di battitura nello script del lavoro. Questo è estremamente frustrante!

La maggior parte dei sistemi fornisce risorse dedicate ai test con tempi di attesa brevi per evitare questo problema.

::::::::::::::::::::::::::::::::::::::::: callout

## Test degli script di invio dei lavori che utilizzano grandi quantità di risorse

Prima di inviare un'ampia serie di lavori, inviarne uno come test per assicurarsi che tutto funzioni come previsto.

Prima di inviare un lavoro molto grande o molto lungo, eseguire un breve test troncato per assicurarsi che il lavoro venga avviato come previsto.


::::::::::::::::::::::::::::::::::::::::::::::::::

## Avere un piano di backup

Sebbene molti sistemi HPC mantengano dei backup, questi non sempre coprono tutti i file system disponibili e possono servire solo per scopi di disaster recovery (cioè per ripristinare l'intero file system in caso di perdita piuttosto che un singolo file o una directory cancellati per errore). Proteggere i dati critici dalla corruzione o dall'eliminazione è una responsabilità primaria dell'utente: conservare le proprie copie di backup.

I sistemi di controllo delle versioni (come Git) hanno spesso offerte gratuite basate su cloud (ad esempio, GitHub e GitLab) che vengono generalmente utilizzate per archiviare il codice sorgente. Anche se non si scrivono programmi propri, questi sistemi possono essere molto utili per archiviare script di lavoro, script di analisi e piccoli file di input.

Se state costruendo un software, potreste avere una grande quantità di codice sorgente che compilate per creare il vostro eseguibile. Poiché questi dati possono essere generalmente recuperati scaricando nuovamente il codice o eseguendo nuovamente l'operazione di checkout dal repository del codice sorgente, questi dati sono anche meno critici da proteggere.

Per le grandi quantità di dati, in particolare per i risultati importanti delle esecuzioni, che possono essere insostituibili, è necessario assicurarsi di disporre di un sistema robusto per prelevare le copie dei dati dal sistema HPC, ove possibile, e trasferirle su uno storage di backup. Strumenti come `rsync` possono essere molto utili a questo scopo.

Il vostro accesso al sistema HPC condiviso sarà generalmente limitato nel tempo, quindi dovreste assicurarvi di avere un piano per trasferire i vostri dati dal sistema prima che il vostro accesso finisca. Il tempo necessario per trasferire grandi quantità di dati non deve essere sottovalutato e bisogna assicurarsi di averlo pianificato con sufficiente anticipo (idealmente, prima ancora di iniziare a usare il sistema per la propria ricerca).

In tutti questi casi, l'helpdesk del sistema in uso dovrebbe essere in grado di fornire indicazioni utili sulle opzioni di trasferimento dei dati per i volumi di dati da utilizzare.

::::::::::::::::::::::::::::::::::::::::: callout

## I vostri dati sono sotto la vostra responsabilità

Assicuratevi di aver capito qual è la politica di backup sui file system del sistema che state usando e quali sono le implicazioni per il vostro lavoro se perdete i vostri dati sul sistema. Pianificate i backup dei dati critici e le modalità di trasferimento dei dati dal sistema durante il progetto.


::::::::::::::::::::::::::::::::::::::::::::::::::

## Trasferimento dei dati

Come accennato in precedenza, molti utenti si trovano prima o poi a dover trasferire grandi quantità di dati dai sistemi HPC (è più frequente il caso di trasferimento di dati da un sistema all'altro che da un sistema all'altro, ma i consigli che seguono si applicano in entrambi i casi). La velocità di trasferimento dei dati può essere limitata da molti fattori diversi, quindi il miglior meccanismo di trasferimento dei dati da utilizzare dipende dal tipo di dati da trasferire e dalla loro destinazione.

I componenti tra la sorgente e la destinazione dei dati hanno livelli di prestazioni variabili e, in particolare, possono avere capacità diverse in termini di **larghezza di banda** e **latenza**.

La **larghezza di banda** è generalmente la quantità grezza di dati per unità di tempo che un dispositivo è in grado di trasmettere o ricevere. È una metrica comune e generalmente ben compresa.

La **latenza** è un po' più sottile. Per i trasferimenti di dati, si può pensare che sia la quantità di tempo necessaria per far uscire i dati dalla memoria e portarli in una forma trasmissibile. I problemi di latenza sono il motivo per cui è consigliabile eseguire i trasferimenti di dati spostando un piccolo numero di file di grandi dimensioni, piuttosto che il contrario.

Alcuni dei componenti chiave e dei problemi ad essi associati sono:

- **Velocità del disco**: I file system dei sistemi HPC sono spesso altamente paralleli, costituiti da un numero molto elevato di unità disco ad alte prestazioni. Ciò consente loro di supportare una larghezza di banda dei dati molto elevata. A meno che il sistema remoto non abbia un file system parallelo simile, la velocità di trasferimento potrebbe essere limitata dalle prestazioni del disco.
- **Prestazioni dei metadati**: *Le operazioni sui meta-dati*, come l'apertura e la chiusura dei file o l'elenco del proprietario o delle dimensioni di un file, sono molto meno parallele delle operazioni di lettura/scrittura. Se i dati sono costituiti da un numero molto elevato di piccoli file, la velocità di trasferimento potrebbe essere limitata dalle operazioni sui meta-dati. Anche le operazioni sui meta-dati eseguite da altri utenti del sistema possono interagire fortemente con quelle eseguite dall'utente, per cui la riduzione del numero di tali operazioni (combinando più file in un unico file) può ridurre la variabilità della velocità di trasferimento e aumentarla.
- **Velocità della rete**: Le prestazioni di trasferimento dei dati possono essere limitate dalla velocità della rete. Soprattutto è limitata dalla sezione più lenta della rete tra la sorgente e la destinazione. Se si sta trasferendo al proprio portatile/alla propria postazione di lavoro, è probabile che si tratti della sua connessione (via LAN o WiFi).
- **Velocità del firewall**: La maggior parte delle reti moderne è protetta da una qualche forma di firewall che filtra il traffico dannoso. Questo filtraggio ha un certo overhead e può comportare una riduzione delle prestazioni di trasferimento dei dati. Le esigenze di una rete generica che ospita e-mail/web-server e computer desktop sono molto diverse da quelle di una rete di ricerca che deve supportare un elevato volume di trasferimenti di dati. Se si cerca di trasferire dati da o verso un host su una rete generica, è possibile che il firewall di quella rete limiti la velocità di trasferimento.

Come già detto, se si dispone di dati correlati che consistono in un gran numero di piccoli file, si consiglia vivamente di impacchettare i file in un file *archivio* più grande per l'archiviazione e il trasferimento a lungo termine. Un singolo file di grandi dimensioni consente di utilizzare in modo più efficiente il file system ed è più facile da spostare, copiare e trasferire perché sono necessarie molte meno operazioni sui metadati. I file di archivio possono essere creati con strumenti come `tar` e `zip`. Abbiamo già incontrato `tar` quando abbiamo parlato del trasferimento dei dati.

![Diagramma schematico della larghezza di banda e della latenza per l'I/O su disco e di rete. Ogni componente della figura è collegato da una linea blu di larghezza proporzionale alla larghezza di banda dell'interfaccia. I piccoli labirinti in corrispondenza dei punti di collegamento illustrano la latenza del collegamento, con labirinti più tortuosi che indicano una latenza più elevata.](/fig/responsibility-bandwidth.svg){max-width="90%" alt="Schema della larghezza di banda di rete"}

::::::::::::::::::::::::::::::::::::::: challenge

## Considerare il modo migliore per trasferire i dati

Se si trasferiscono grandi quantità di dati, è necessario pensare a cosa può influire sulle prestazioni del trasferimento. È sempre utile eseguire alcuni test da utilizzare per estrapolare il tempo necessario per trasferire i dati.

Supponiamo di avere una cartella "dati" contenente circa 10.000 file, un sano mix di dati ASCII e binari di piccole e grandi dimensioni. Quale dei seguenti è il modo migliore per trasferirli in HPC Carpentry's Cloud Cluster?

1. `scp -r data yourUsername@cluster.hpc-carpentry.org:~/`
2. `rsync -ra data yourUsername@cluster.hpc-carpentry.org:~/`
3. `rsync -raz data yourUsername@cluster.hpc-carpentry.org:~/`
4. `tar -cvf data.tar data;`

   `rsync -raz data.tar yourUsername@cluster.hpc-carpentry.org:~/`
5. `tar -cvzf data.tar.gz data;`

   `rsync -ra data.tar.gz yourUsername@cluster.hpc-carpentry.org:~/`

::::::::::::::: solution

## Soluzione

1. `scp` copierà ricorsivamente la directory. Funziona, ma senza compressione.
2. `rsync -ra` funziona come `scp -r`, ma conserva le informazioni sui file, come i tempi di creazione. Questo è marginalmente migliore.
3. `rsync -raz` aggiunge la compressione, che farà risparmiare un po' di larghezza di banda. Se si dispone di una CPU forte a entrambi i capi della linea e si è su una rete lenta, questa è una buona scelta.
4. Questo comando usa prima `tar` per unire tutto in un unico file, poi `rsync -z` per trasferirlo con la compressione. Con un numero così elevato di file, l'overhead dei metadati può ostacolare il trasferimento, quindi questa è una buona idea.
5. Questo comando usa `tar -z` per comprimere l'archivio e poi `rsync` per trasferirlo. Le prestazioni sono simili a quelle del comando #4, ma nella maggior parte dei casi (per insiemi di dati di grandi dimensioni), è la migliore combinazione di alta velocità e bassa latenza (per sfruttare al meglio il tempo e la connessione di rete).

:::::::::::::::::::::::::

::::::::::::::::::::::::::::::::::::::::::::::::::

:::::::::::::::::::::::::::::::::::::::: keypoints

- Fare attenzione a come si usa il nodo login.
- I dati presenti sul sistema sono sotto la propria responsabilità.
- Pianificare e testare trasferimenti di dati di grandi dimensioni.
- Spesso è meglio convertire molti file in un singolo file di archivio prima di trasferirli.

::::::::::::::::::::::::::::::::::::::::::::::::::



