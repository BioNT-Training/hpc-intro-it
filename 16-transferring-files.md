---
title: Trasferimento di file con computer remoti
teaching: 15
exercises: 15
---




::::::::::::::::::::::::::::::::::::::: objectives

- Trasferimento di file da e verso un cluster di calcolo.

::::::::::::::::::::::::::::::::::::::::::::::::::

:::::::::::::::::::::::::::::::::::::::: questions

- Come si trasferiscono i file al (e dal) cluster?

::::::::::::::::::::::::::::::::::::::::::::::::::

Eseguire il lavoro su un computer remoto non è molto utile se non si possono ottenere file da o verso il cluster. Esistono diverse opzioni per trasferire i dati tra le risorse di calcolo utilizzando le utility CLI e GUI, alcune delle quali verranno trattate.

## Scaricare i file delle lezioni da Internet

Uno dei modi più semplici per scaricare i file è usare `curl` o `wget`. Uno di questi è solitamente installato nella maggior parte delle shell di Linux, nel terminale di Mac OS e in GitBash. Qualsiasi file che può essere scaricato nel browser web attraverso un link diretto può essere scaricato usando `curl` o `wget`. Si tratta di un modo rapido per scaricare insiemi di dati o codice sorgente. La sintassi di questi comandi è

- `wget [-O new_name] https://some/link/to/a/file`
- `curl [-o new_name] https://some/link/to/a/file`

provate a scaricare del materiale che useremo in seguito, da un terminale sulla vostra macchina locale, usando l'URL della base di codice corrente:

<https://github.com/hpc-carpentry/amdahl/tarball/main>

::::::::::::::::::::::::::::::::::::::: challenge

## Scarica il "Tarball"

La parola "tarball" nell'URL di cui sopra si riferisce a un formato di archivio compresso comunemente usato su Linux, che è il sistema operativo su cui gira la maggior parte dei cluster HPC. Un tarball è molto simile a un file `.zip`. L'estensione effettiva del file è `.tar.gz`, che riflette il processo in due fasi utilizzato per creare il file: i file o le cartelle vengono uniti in un unico file utilizzando `tar`, che viene poi compresso utilizzando `gzip`, quindi l'estensione del file è "tar-dot-g-z" È una parola lunga, quindi spesso si dice "il tarball *xyz*".

si può anche vedere l'estensione `.tgz`, che è solo un'abbreviazione di `.tar.gz`.

Per impostazione predefinita, `curl` e `wget` scaricano i file con lo stesso nome dell'URL: in questo caso, `main`. Usare uno dei comandi precedenti per salvare il tarball come `amdahl.tar.gz`.

::::::::::::::: solution

## Comandi `wget` e `curl`

```bash
[you@laptop:~]$ wget -O amdahl.tar.gz https://github.com/hpc-carpentry/amdahl/tarball/main
# or
[you@laptop:~]$ curl -o amdahl.tar.gz -L https://github.com/hpc-carpentry/amdahl/tarball/main
```

L'opzione `-L` di `curl` indica di seguire i reindirizzamenti URL (cosa che `wget` fa di default).



:::::::::::::::::::::::::

::::::::::::::::::::::::::::::::::::::::::::::::::

dopo aver scaricato il file, usare `ls` per vederlo nella propria directory di lavoro:

```bash
[you@laptop:~]$ ls
```

## Archiviazione dei file

Una delle maggiori sfide che spesso ci troviamo ad affrontare quando trasferiamo dati tra sistemi HPC remoti è quella di un gran numero di file. Il trasferimento di ogni singolo file comporta un sovraccarico e quando si trasferisce un gran numero di file questi sovraccarichi si combinano per rallentare notevolmente i trasferimenti.

La soluzione a questo problema è quella di *archiviare* più file in un numero minore di file più grandi prima di trasferire i dati per migliorare l'efficienza del trasferimento. A volte si combina l'archiviazione con la *compressione* per ridurre la quantità di dati da trasferire e quindi velocizzare il trasferimento. Il comando di archiviazione più comune che si usa su un cluster HPC (Linux) è `tar`.

`tar` può essere usato per combinare file e cartelle in un unico file di archivio e, facoltativamente, comprimere il risultato. Osserviamo il file scaricato dal sito della lezione, `amdahl.tar.gz`.

La parte `.gz` sta per *gzip*, che è una libreria di compressione. È comune (ma non necessario!) che questo tipo di file possa essere interpretato leggendo il suo nome: sembra che qualcuno abbia preso file e cartelle relativi a qualcosa chiamato "amdahl", li abbia impacchettati tutti in un singolo file con `tar`, quindi abbia compresso l'archivio con `gzip` per risparmiare spazio.

Vediamo se questo è il caso, *senza* scompattare il file. `tar` stampa il "**testo del contenuto" con il flag `-t`, per il file specificato con il flag `-f` seguito dal nome del file. Si noti che è possibile concatenare i due flag: scrivere `-t -f` è intercambiabile con scrivere `-tf` insieme. Tuttavia, l'argomento che segue `-f` deve essere un nome di file, quindi scrivere `-ft` non funzionerà.

```bash
[you@laptop:~]$ tar -tf amdahl.tar.gz
hpc-carpentry-amdahl-46c9b4b/
hpc-carpentry-amdahl-46c9b4b/.github/
hpc-carpentry-amdahl-46c9b4b/.github/workflows/
hpc-carpentry-amdahl-46c9b4b/.github/workflows/python-publish.yml
hpc-carpentry-amdahl-46c9b4b/.gitignore
hpc-carpentry-amdahl-46c9b4b/LICENSE
hpc-carpentry-amdahl-46c9b4b/README.md
hpc-carpentry-amdahl-46c9b4b/amdahl/
hpc-carpentry-amdahl-46c9b4b/amdahl/__init__.py
hpc-carpentry-amdahl-46c9b4b/amdahl/__main__.py
hpc-carpentry-amdahl-46c9b4b/amdahl/amdahl.py
hpc-carpentry-amdahl-46c9b4b/requirements.txt
hpc-carpentry-amdahl-46c9b4b/setup.py
```

Questo esempio di output mostra una cartella che contiene alcuni file, dove `46c9b4b` è un hash di commit di 8 caratteri [git][git-swc] che cambierà quando il materiale sorgente verrà aggiornato.

Ora scompattiamo l'archivio. Eseguiremo `tar` con alcuni flag comuni:

- `-x` per estrarre l'archivio
- `-v` per un output **v**erboso
- `-z` per la compressione g**z**ip
- `-f «tarball»` per il file da scompattare

:::::::::::::::::::::::::::::::::::::: discussion

## Estrazione dell'archivio

Usando i flag di cui sopra, scompattare il tarball del codice sorgente in una nuova directory chiamata "amdahl" usando `tar`.

```bash
[you@laptop:~]$ tar -xvzf amdahl.tar.gz
```

```output
hpc-carpentry-amdahl-46c9b4b/
hpc-carpentry-amdahl-46c9b4b/.github/
hpc-carpentry-amdahl-46c9b4b/.github/workflows/
hpc-carpentry-amdahl-46c9b4b/.github/workflows/python-publish.yml
hpc-carpentry-amdahl-46c9b4b/.gitignore
hpc-carpentry-amdahl-46c9b4b/LICENSE
hpc-carpentry-amdahl-46c9b4b/README.md
hpc-carpentry-amdahl-46c9b4b/amdahl/
hpc-carpentry-amdahl-46c9b4b/amdahl/__init__.py
hpc-carpentry-amdahl-46c9b4b/amdahl/__main__.py
hpc-carpentry-amdahl-46c9b4b/amdahl/amdahl.py
hpc-carpentry-amdahl-46c9b4b/requirements.txt
hpc-carpentry-amdahl-46c9b4b/setup.py
```

Si noti che non è stato necessario digitare `-x -v -z -f`, grazie alla concatenazione dei flag, anche se il comando funziona in modo identico in entrambi i casi, purché l'elenco concatenato termini con `f`, perché la stringa successiva deve specificare il nome del file da estrarre.


::::::::::::::::::::::::::::::::::::::::::::::::::

La cartella ha un nome infelice, quindi cambiamolo con qualcosa di più comodo.

```bash
[you@laptop:~]$ mv hpc-carpentry-amdahl-46c9b4b amdahl
```

Controllare la dimensione della directory estratta e confrontarla con la dimensione del file compresso, usando `du` per "**d**isk **u**sage".

```bash
[you@laptop:~]$ du -sh amdahl.tar.gz
8.0K     amdahl.tar.gz
[you@laptop:~]$ du -sh amdahl
48K    amdahl
```

I file di testo (compreso il codice sorgente Python) si comprimono bene: il "tarball" è un sesto della dimensione totale dei dati grezzi!

Se si vuole invertire il processo - comprimere i dati grezzi invece di estrarli - impostare un flag `c` invece di `x`, impostare il nome del file di archivio e fornire una directory da comprimere:

```bash
[you@laptop:~]$ tar -cvzf compressed_code.tar.gz amdahl
```

```output
amdahl/
amdahl/.github/
amdahl/.github/workflows/
amdahl/.github/workflows/python-publish.yml
amdahl/.gitignore
amdahl/LICENSE
amdahl/README.md
amdahl/amdahl/
amdahl/amdahl/__init__.py
amdahl/amdahl/__main__.py
amdahl/amdahl/amdahl.py
amdahl/requirements.txt
amdahl/setup.py
```

Se si dà `amdahl.tar.gz` come nome del file nel comando precedente, `tar` aggiornerà il tarball esistente con qualsiasi modifica apportata ai file. Ciò significa aggiungere la nuova cartella `amdahl` alla cartella *esistente* (`hpc-carpentry-amdahl-46c9b4b`) all'interno del tarball, raddoppiando le dimensioni dell'archivio!

::::::::::::::::::::::::::::::::::::::::: callout

## Lavorare con Windows

Quando si trasferiscono file di testo da un sistema Windows a un sistema Unix (Mac, Linux, BSD, Solaris, ecc.) questo può causare problemi. Windows codifica i suoi file in modo leggermente diverso da Unix e aggiunge un carattere extra a ogni riga.

In un sistema Unix, ogni riga di un file termina con un `\n` (newline). Su Windows, ogni riga di un file termina con un `\r\n` (ritorno a capo + newline). Questo a volte causa problemi.

Sebbene la maggior parte dei linguaggi di programmazione e dei software moderni gestisca questo problema correttamente, in alcuni rari casi si può incorrere in un problema. La soluzione consiste nel convertire un file dalla codifica Windows a quella Unix con il comando `dos2unix`.

È possibile identificare se un file ha terminazioni di riga Windows con `cat -A filename`. Un file con terminazioni di riga Windows avrà `^M$` alla fine di ogni riga. Un file con terminazioni di riga Unix avrà `$` alla fine di una riga.

Per convertire il file, basta eseguire `dos2unix filename`. (Viceversa, per riconvertire il formato Windows, si può eseguire `unix2dos filename`)


::::::::::::::::::::::::::::::::::::::::::::::::::

## Trasferimento di singoli file e cartelle con `scp`

Per copiare un singolo file da o verso il cluster, si può usare `scp` ("copia sicura"). La sintassi può essere un po' complessa per i nuovi utenti, ma la spiegheremo. Il comando `scp` è un parente del comando `ssh` che abbiamo usato per accedere al sistema e può usare lo stesso meccanismo di autenticazione a chiave pubblica.

per *caricare su* un altro computer, il comando modello è

```bash
[you@laptop:~]$ scp local_file yourUsername@cluster.hpc-carpentry.org:remote_destination
```

in cui `@` e `:` sono separatori di campo e `remote_destination` è un percorso relativo alla vostra home directory remota, o un nuovo nome di file se desiderate cambiarlo, o sia un percorso relativo *e* un nuovo nome di file. Se non si ha in mente una cartella specifica, si può omettere la `remote_destination` e il file verrà copiato nella propria home directory sul computer remoto (con il nome originale). Se si include un `remote_destination`, si noti che `scp` lo interpreta nello stesso modo in cui lo interpreta `cp` quando si effettuano copie locali: se esiste ed è una cartella, il file viene copiato all'interno della cartella; se esiste ed è un file, il file viene sovrascritto con il contenuto di `local_file`; se non esiste, si presume che sia un nome di file di destinazione per `local_file`.

Carica il materiale della lezione nella tua home directory remota in questo modo:

```bash
[you@laptop:~]$ scp amdahl.tar.gz yourUsername@cluster.hpc-carpentry.org:
```

::::::::::::::::::::::::::::::::::::::: challenge

## Perché non scaricare direttamente su HPC Carpentry's Cloud Cluster?

La maggior parte dei cluster di computer sono protetti da Internet da un *firewall*. Per una maggiore sicurezza, alcuni sono configurati per consentire il traffico *in entrata*, ma non *in uscita*. Ciò significa che un utente autenticato può inviare un file a un computer del cluster, ma un computer del cluster non può recuperare i file dal computer di un utente o da Internet.

provare a scaricare direttamente il file. Si noti che potrebbe fallire, ma va bene così!

::::::::::::::: solution

## Comandi

```bash
[you@laptop:~]$ ssh yourUsername@cluster.hpc-carpentry.org
[yourUsername@login1 ~] wget -O amdahl.tar.gz https://github.com/hpc-carpentry/amdahl/tarball/main
# or
[yourUsername@login1 ~] curl -o amdahl.tar.gz https://github.com/hpc-carpentry/amdahl/tarball/main
```

:::::::::::::::::::::::::

ha funzionato? Se no, che cosa ci dice l'output del terminale su ciò che è successo?


::::::::::::::::::::::::::::::::::::::::::::::::::

## Trasferimento di una directory

Per trasferire un'intera directory, aggiungiamo il flag `-r` per "**r**ecursive": copia l'elemento specificato, e ogni elemento sotto di esso, e ogni elemento sotto di esso... fino a raggiungere il fondo dell'albero delle directory con radice nel nome della cartella fornito.

```bash
[you@laptop:~]$ scp -r amdahl yourUsername@cluster.hpc-carpentry.org:
```

::::::::::::::::::::::::::::::::::::::::: callout

## Attenzione

Per una directory di grandi dimensioni, sia in termini di dimensioni che di numero di file, la copia con `-r` può richiedere molto tempo.


::::::::::::::::::::::::::::::::::::::::::::::::::

Quando si usa `scp`, si può notare che un `:` segue *sempre* il nome del computer remoto. Una stringa *dopo* la `:` specifica la directory remota in cui si desidera trasferire il file o la cartella, compreso un nuovo nome se si desidera rinominare il materiale remoto. Se si lascia questo campo vuoto, per impostazione predefinita, `scp` è la propria home directory e il nome del materiale locale da trasferire.

Nei computer Linux, `/` è il separatore nei percorsi di file o directory. Un percorso che inizia con un `/` è detto *assoluto*, poiché non può esserci nulla al di sopra della radice `/`. Un percorso che non inizia con `/` è detto *relativo*, poiché non è ancorato alla radice.

Se si vuole caricare un file in una posizione all'interno della propria home directory, cosa che accade spesso, non è necessario un `/`. Dopo il `:`, si può digitare il percorso di destinazione relativo alla propria home directory. Se la propria home directory *è* la destinazione, si può lasciare il campo della destinazione vuoto, oppure digitare `~` -- l'abbreviazione della propria home directory -- per completezza.

Con `scp`, una barra tracciante sulla directory di destinazione è opzionale e non ha alcun effetto. Il trattino sulla directory di origine è importante per altri comandi, come `rsync`.

::::::::::::::::::::::::::::::::::::::::: callout

## Nota su `rsync`

Man mano che si acquisisce esperienza nel trasferimento di file, il comando `scp` può risultare limitante. L'utilità [rsync] fornisce funzioni avanzate per il trasferimento di file ed è tipicamente più veloce sia di `scp` che di `sftp` (vedi sotto). È particolarmente utile per trasferire file di grandi dimensioni e/o numerosi e per sincronizzare il contenuto delle cartelle tra computer.

la sintassi è simile a quella di `scp`. Per trasferire *a* un altro computer con le opzioni comunemente usate:

```bash
[you@laptop:~]$ rsync -avP amdahl.tar.gz yourUsername@cluster.hpc-carpentry.org:
```

Le opzioni sono:

- `-a` (**un**archivio) per conservare i timestamp dei file, i permessi e le cartelle, tra le altre cose; implica la ricorsione
- `-v` (**v**erbose) per ottenere un output verboso che aiuti a monitorare il trasferimento
- `-P` (parziale/progresso) per preservare i file parzialmente trasferiti in caso di interruzione e per visualizzare il progresso del trasferimento.

Per copiare ricorsivamente una directory, si possono usare le stesse opzioni:

```bash
[you@laptop:~]$ rsync -avP amdahl yourUsername@cluster.hpc-carpentry.org:~/
```

così come è scritto, la directory locale e il suo contenuto si trovano sotto la propria home directory sul sistema remoto. Se all'origine viene aggiunto un trattino, non verrà creata una nuova directory corrispondente alla directory trasferita e il contenuto della directory di origine verrà copiato direttamente nella directory di destinazione.

Per scaricare un file, basta cambiare la sorgente e la destinazione:

```bash
[you@laptop:~]$ rsync -avP yourUsername@cluster.hpc-carpentry.org:amdahl ./
```

::::::::::::::::::::::::::::::::::::::::::::::::::

I trasferimenti di file che utilizzano sia `scp` che `rsync` utilizzano SSH per crittografare i dati inviati attraverso la rete. Quindi, se ci si può connettere tramite SSH, si potranno trasferire i file. Per impostazione predefinita, SSH utilizza la porta 22 della rete. Se è in uso una porta SSH personalizzata, è necessario specificarla usando il flag appropriato, spesso `-p`, `-P` o `--port`. Controllare `--help` o la pagina `man` se non si è sicuri.

::::::::::::::::::::::::::::::::::::::: challenge

## Cambia la porta di Rsync

Supponiamo di dover connettere `rsync` attraverso la porta 768 invece che 22. Come modificheremmo questo comando?

```bash
[you@laptop:~]$ rsync amdahl.tar.gz yourUsername@cluster.hpc-carpentry.org:
```

*Consiglio:* controllate la pagina `man` o "help" per `rsync`.

::::::::::::::: solution

## Soluzione

```bash
[you@laptop:~]$ man rsync
[you@laptop:~]$ rsync --help | grep port
     --port=PORT             specify double-colon alternate port number
See http://rsync.samba.org/ for updates, bug reports, and answers
[you@laptop:~]$ rsync --port=768 amdahl.tar.gz yourUsername@cluster.hpc-carpentry.org:
```

(Si noti che questo comando fallirà, poiché la porta corretta in questo caso è quella predefinita: 22)



:::::::::::::::::::::::::

::::::::::::::::::::::::::::::::::::::::::::::::::

## Trasferimento interattivo di file con FileZilla

FileZilla è un client multipiattaforma per scaricare e caricare file da e verso un computer remoto. È assolutamente infallibile e funziona sempre abbastanza bene. Utilizza il protocollo `sftp`. Per saperne di più sull'uso del protocollo `sftp` nella riga di comando, consultare la [discussione della lezione] (../learning/discuss.md).

Scaricare e installare il client FileZilla da <https://filezilla-project.org>. Dopo l'installazione e l'apertura del programma, dovrebbe apparire una finestra con un browser di file del sistema locale sul lato sinistro dello schermo. Quando ci si connette al cluster, i file del cluster appariranno sul lato destro.

Per connettersi al cluster, è sufficiente inserire le proprie credenziali nella parte superiore dello schermo:

- Host: `sftp://cluster.hpc-carpentry.org`
- Utente: il nome utente del cluster
- Password: La password del cluster
- Porta: (lasciare vuoto per usare la porta predefinita)

premere "Quickconnect" per connettersi. I file remoti dovrebbero apparire sul lato destro dello schermo. È possibile trascinare i file tra il lato sinistro (locale) e quello destro (remoto) dello schermo per trasferirli.

Infine, se si devono spostare file di grandi dimensioni (tipicamente più grandi di un gigabyte) da un computer remoto a un altro computer remoto, si deve accedere con SSH al computer che ospita i file e usare `scp` o `rsync` per trasferirli all'altro. Questo sarà più efficiente rispetto all'uso di FileZilla (o di applicazioni simili) che copierebbe dalla sorgente al computer locale e poi al computer di destinazione.

[git-swc]: https://swcarpentry.github.io/git-novice/
[rsync]: https://rsync.samba.org/

:::::::::::::::::::::::::::::::::::::::: keypoints

- `wget` e `curl -O` scaricano un file da Internet.
- `scp` e `rsync` trasferiscono i file da e verso il computer.
- È possibile utilizzare un client SFTP come FileZilla per trasferire i file attraverso un'interfaccia grafica.

::::::::::::::::::::::::::::::::::::::::::::::::::


