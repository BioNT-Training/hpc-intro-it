---
title: Connessione a un sistema HPC remoto
teaching: 25
exercises: 10
---


::::::::::::::::::::::::::::::::::::::: objectives

- Configurare l'accesso sicuro a un sistema HPC remoto.
- Connessione a un sistema HPC remoto.

::::::::::::::::::::::::::::::::::::::::::::::::::

:::::::::::::::::::::::::::::::::::::::: questions

- Come si accede a un sistema HPC remoto?

::::::::::::::::::::::::::::::::::::::::::::::::::



## Connessioni sicure

Il primo passo per utilizzare un cluster è stabilire una connessione dal nostro portatile al cluster. Quando siamo seduti davanti a un computer (o in piedi, o tenendolo in mano o al polso), ci aspettiamo una visualizzazione con icone, widget e forse alcune finestre o applicazioni: una *interfaccia utente grafica*, o GUI. Poiché i cluster di computer sono risorse remote a cui ci si connette tramite interfacce lente o intermittenti (soprattutto WiFi e VPN), è più pratico utilizzare una *interfaccia a riga di comando*, o CLI, per inviare comandi in testo semplice. Se un comando restituisce un output, anche questo viene stampato come testo normale. I comandi che eseguiamo oggi non apriranno una finestra per mostrare i risultati grafici.

Se avete mai aperto il Prompt dei comandi di Windows o il Terminale di macOS, avete visto una CLI. Se avete già seguito i corsi di The Carpentries sulla shell UNIX o sul controllo di versione, avete usato ampiamente la CLI sulla vostra macchina *locale*. L'unico salto da fare in questo caso è aprire una CLI su una *macchina remota*, prendendo alcune precauzioni in modo che gli altri utenti della rete non possano vedere (o modificare) i comandi che state eseguendo o i risultati che la macchina remota invia. Utilizzeremo il protocollo Secure SHell (o SSH) per aprire una connessione di rete crittografata tra due macchine, consentendovi di inviare e ricevere testo e dati senza dovervi preoccupare di occhi indiscreti.

![](/fig/connect-to-remote.svg){max-width="50%" alt="Connetti al cluster"}


I client SSH sono solitamente strumenti a riga di comando, in cui si fornisce l'indirizzo della macchina remota come unico argomento richiesto. Se il nome utente sul sistema remoto è diverso da quello utilizzato localmente, è necessario fornire anche quello. Se il vostro client SSH ha un front-end grafico, come PuTTY o MobaXterm, dovrete impostare questi argomenti prima di fare clic su "connect" Dal terminale, si scriverà qualcosa come `ssh userName@hostname`, dove l'argomento è proprio come un indirizzo e-mail: il simbolo "@" è usato per separare l'ID personale dall'indirizzo della macchina remota.

Quando si accede a un computer portatile, a un tablet o a un altro dispositivo personale, di solito sono necessari un nome utente, una password o un modello per impedire l'accesso non autorizzato. In queste situazioni, la probabilità che qualcun altro intercetti la password è bassa, poiché la registrazione dei tasti premuti richiede un exploit dannoso o un accesso fisico. Per i sistemi come login1' che eseguono un server SSH, chiunque sulla rete può accedere, o tentare di farlo. Poiché i nomi utente sono spesso pubblici o facili da indovinare, la password è spesso l'anello più debole della catena di sicurezza. Per questo motivo, molti cluster vietano il login basato su password, richiedendo invece di generare e configurare una coppia di chiavi pubbliche e private con una password molto più forte. Anche se il vostro cluster non lo richiede, la prossima sezione vi guiderà nell'uso delle chiavi SSH e di un agente SSH per rafforzare la vostra sicurezza *e* rendere più conveniente l'accesso ai sistemi remoti.

### Migliore sicurezza con le chiavi SSH

Il [Lesson Setup](../learners/setup.md) fornisce le istruzioni per installare un'applicazione shell con SSH. Se non l'avete già fatto, aprite l'applicazione di shell con un'interfaccia a riga di comando di tipo Unix sul vostro sistema.

Le chiavi SSH sono un metodo alternativo di autenticazione per ottenere l'accesso a sistemi informatici remoti. Possono anche essere usate per l'autenticazione durante il trasferimento di file o per accedere a sistemi di controllo di versione remoti (come [GitHub][gh-ssh]). In questa sezione verrà creata una coppia di chiavi SSH:

- una chiave privata che si conserva sul proprio computer e
- una chiave pubblica che può essere inserita in qualsiasi sistema remoto a cui si accede.

::::::::::::::::::::::::::::::::::::::::: caution

## Le chiavi private sono il vostro passaporto digitale sicuro

Una chiave privata visibile a chiunque tranne che a voi deve essere considerata compromessa e deve essere distrutta. Questo include avere permessi impropri sulla directory in cui è memorizzata (o una sua copia), attraversare una rete non sicura (crittografata), allegare e-mail non crittografate e persino visualizzare la chiave nella finestra del terminale.

Proteggete questa chiave come se vi aprisse la porta di casa. Per molti versi, è così.

::::::::::::::::::::::::::::::::::::::::::::::::::

Indipendentemente dal software o dal sistema operativo utilizzato, *prego* di scegliere una password o una passphrase forte che funga da ulteriore livello di protezione per la chiave SSH privata.

::::::::::::::::::::::::::::::::::::::::: callout

## Considerazioni sulle password delle chiavi SSH

Quando viene richiesto, inserire una password forte che si ricordi. Esistono due approcci comuni:

1. Creare una passphrase memorabile con punteggiatura e sostituzioni di numeri con lettere, di almeno 32 caratteri. Gli indirizzi stradali funzionano bene; fate attenzione agli attacchi di social engineering o ai registri pubblici.
2. Utilizzare un gestore di password e il suo generatore di password integrato con tutte le classi di caratteri, a partire da 25 caratteri. [KeePass][keepass] e [BitWarden][bitwarden] sono due buone opzioni.
3. Nulla è *meno* sicuro di una chiave privata senza password. Se per sbaglio avete saltato l'inserimento della password, tornate indietro e generate una nuova coppia di chiavi *con* una password forte.

::::::::::::::::::::::::::::::::::::::::::::::::::

#### Chiavi SSH su Linux, Mac, MobaXterm e Windows Sottosistema per Linux

Una volta aperto il terminale, verificare la presenza di chiavi SSH e nomi di file esistenti, poiché le chiavi SSH esistenti vengono sovrascritte.

```bash
[you@laptop:~]$ ls ~/.ssh/
```

se `~/.ssh/id_ed25519` esiste già, è necessario specificare un nome diverso per la nuova coppia di chiavi.

Generare una nuova coppia di chiavi pubbliche e private usando il seguente comando, che produrrà una chiave più forte di quella predefinita `ssh-keygen` invocando questi flag:

- `-a` (il valore predefinito è 16): numero di cicli di derivazione della passphrase; aumentare per rallentare gli attacchi brute force.
- `-t` (l'impostazione predefinita è [rsa][wiki-rsa]): specifica il "tipo" o algoritmo crittografico.`ed25519` specifica [EdDSA][wiki-dsa] con una chiave a 256 bit; è più veloce di RSA con una forza comparabile.
- `-f` (predefinito è /home/user/.ssh/id\algoritmo): nome del file in cui memorizzare la chiave privata. Il nome del file della chiave pubblica sarà identico, con l'aggiunta dell'estensione `.pub`.

```bash
[you@laptop:~]$ ssh-keygen -a 100 -f ~/.ssh/id_ed25519 -t ed25519
```

Quando viene richiesto, inserire una password forte tenendo conto delle [considerazioni precedenti](#considerazioni-per-le-chiavi-password). Si noti che il terminale non sembra cambiare mentre si digita la password: questo è intenzionale, per la vostra sicurezza. Vi verrà richiesto di digitarla di nuovo, quindi non preoccupatevi troppo degli errori di battitura.

Guardare in `~/.ssh` (usare `ls ~/.ssh`). Si dovrebbero vedere due nuovi file:

- la vostra chiave privata (`~/.ssh/id_ed25519`): *non condividere con nessuno!
- la chiave pubblica condivisibile (`~/.ssh/id_ed25519.pub`): se un amministratore di sistema chiede una chiave, questa è quella da inviare. È anche sicura da caricare su siti web come GitHub: è destinata a essere vista.

::::::::::::::::::::::::::::::::::::::::: callout

## Usa RSA per i sistemi più vecchi

Se la generazione della chiave non è riuscita perché ed25519 non è disponibile, provare a usare il più vecchio (ma ancora forte e affidabile) crittosistema [RSA][wiki-rsa]. Anche in questo caso, verificare prima la presenza di una chiave esistente:

```bash
[you@laptop:~]$ ls ~/.ssh/
```

Se `~/.ssh/id_rsa` esiste già, è necessario specificare un nome diverso per la nuova coppia di chiavi. Generarla come sopra, con i seguenti flag aggiuntivi:

- `-b` imposta il numero di bit della chiave. L'impostazione predefinita è 2048. EdDSA utilizza una lunghezza fissa della chiave, quindi questo flag non ha alcun effetto.
- `-o` (nessun valore predefinito): utilizzare il formato della chiave OpenSSH, anziché PEM.

```bash
[you@laptop:~]$ ssh-keygen -a 100 -b 4096 -f ~/.ssh/id_rsa -o -t rsa
```

Quando viene richiesto, inserire una password forte tenendo conto delle [considerazioni di cui sopra](#considerazioni-per-le-chiavi-password).

Guardare in `~/.ssh` (usare `ls ~/.ssh`). Si dovrebbero vedere due nuovi file:

- la vostra chiave privata (`~/.ssh/id_rsa`): *non condividere con nessuno!
- la chiave pubblica condivisibile (`~/.ssh/id_rsa.pub`): se un amministratore di sistema chiede una chiave, questa è quella da inviare. È anche sicura da caricare su siti web come GitHub: è destinata a essere vista.

::::::::::::::::::::::::::::::::::::::::::::::::::

#### Chiavi SSH su PuTTY

Se si usa PuTTY su Windows, scaricare e usare `puttygen` per generare la coppia di chiavi. Vedere la [documentazione di PuTTY][putty-gen] per i dettagli.

- Selezionare `EdDSA` come tipo di chiave.
- Selezionare `255` come dimensione o forza della chiave.
- Fare clic sul pulsante "Genera".
- Non è necessario inserire un commento.
- Quando viene richiesto, inserire una password forte tenendo conto delle [considerazioni di cui sopra](#considerazioni-per-le-chiavi-password).
- Salvare le chiavi in una cartella che non può essere letta da altri utenti del sistema.

Guardare nella cartella specificata. Si dovrebbero vedere due nuovi file:

- la vostra chiave privata (`id_ed25519`): *non condividere con nessuno!
- la chiave pubblica condivisibile (`id_ed25519.pub`): se un amministratore di sistema chiede una chiave, questa è quella da inviare. È anche sicura da caricare su siti web come GitHub: è destinata a essere vista.

### Agente SSH per una gestione delle chiavi più semplice

Una chiave SSH è forte quanto la password usata per sbloccarla, ma d'altra parte digitare una password complessa ogni volta che ci si connette a una macchina è noioso e stufa molto velocemente. È qui che entra in gioco l'[agente SSH][ssh-agent].

Utilizzando un agente SSH, è possibile digitare una volta la password per la chiave privata e fare in modo che l'agente la ricordi per un certo numero di ore o finché non ci si disconnette. A meno che qualche malintenzionato non abbia accesso fisico al vostro computer, questo permette di mantenere la password al sicuro ed elimina la noia di doverla digitare più volte.

Ricordate la password, perché una volta scaduta nell'Agente, dovrete digitarla di nuovo.

#### Agenti SSH su Linux, macOS e Windows

Aprire l'applicazione terminale e verificare se è in esecuzione un agente:

```bash
[you@laptop:~]$ ssh-add -l
```

- Se si ottiene un errore come questo,

  ```error
  Error connecting to agent: No such file or directory
  ```

  ... quindi è necessario lanciare l'agente come segue:

  ```bash
  [you@laptop:~]$ eval $(ssh-agent)
  ```

  ::::::::::::::::::::::::::::::::::::::::: callout

  ## Cosa c'è in un `$(...)`?

  La sintassi di questo comando dell'agente SSH è insolita, rispetto a quanto visto nella lezione sulla shell UNIX. Questo perché il comando `ssh-agent` crea una connessione a cui solo voi avete accesso e stampa una serie di comandi di shell che possono essere usati per raggiungerla, ma *non li esegue!

  ```bash
  [you@laptop:~]$ ssh-agent
  ```

  ```output
  SSH_AUTH_SOCK=/tmp/ssh-Zvvga2Y8kQZN/agent.131521;
  export SSH_AUTH_SOCK;
  SSH_AGENT_PID=131522;
  export SSH_AGENT_PID;
  echo Agent pid 131522;
  ```

  Il comando `eval` interpreta l'output di testo come comandi e consente di accedere alla connessione dell'agente SSH appena creata.

  Si potrebbe eseguire da soli ogni riga dell'output di `ssh-agent` e ottenere lo stesso risultato. L'uso di `eval` rende tutto più semplice.


  ::::::::::::::::::::::::::::::::::::::::::::::::::

- In caso contrario, l'agente è già in esecuzione: non modificarlo.

Aggiungere la propria chiave all'agente, con scadenza della sessione dopo 8 ore:

```bash
[you@laptop:~]$ ssh-add -t 8h ~/.ssh/id_ed25519
```

```output
Enter passphrase for .ssh/id_ed25519: 
Identity added: .ssh/id_ed25519
Lifetime set to 86400 seconds
```

Per tutta la durata (8 ore), ogni volta che userete quella chiave, l'agente SSH fornirà la chiave per vostro conto senza che dobbiate digitare un solo tasto.

#### Agente SSH su PuTTY

Se si usa PuTTY su Windows, scaricare e usare `pageant` come agente SSH. Vedere la [documentazione di PuTTY][putty-agent].

### Trasferire la propria chiave pubblica

```bash
[you@laptop:~]$ scp ~/.ssh/id_ed25519.pub yourUsername@cluster.hpc-carpentry.org:~/
```



## Accedere al cluster

Aprire il terminale o il client grafico SSH, quindi accedere al cluster. Sostituire `yourUsername` con il proprio nome utente o con quello fornito dagli istruttori.

```bash
[you@laptop:~]$ ssh yourUsername@cluster.hpc-carpentry.org
```

potrebbe essere richiesta la password. Attenzione: i caratteri digitati dopo la richiesta della password non vengono visualizzati sullo schermo. L'output normale riprenderà quando si premerà `Enter`.

avrete notato che il prompt è cambiato quando vi siete collegati al sistema remoto usando il terminale (se vi siete collegati con PuTTY questo non vale perché non offre un terminale locale). Questo cambiamento è importante perché può aiutare a distinguere su quale sistema verranno eseguiti i comandi digitati quando li si passa al terminale. Questa modifica rappresenta anche una piccola complicazione che dovremo affrontare nel corso del workshop. L'esatta visualizzazione del prompt (che convenzionalmente termina con `$`) nel terminale quando è collegato al sistema locale e al sistema remoto sarà in genere diversa per ogni utente. È comunque necessario indicare su quale sistema si stanno inserendo i comandi, quindi adotteremo la seguente convenzione:

- `[you@laptop:~]$` quando il comando deve essere immesso su un terminale collegato al computer locale
- `[yourUsername@login1 ~]` quando il comando deve essere immesso su un terminale collegato al sistema remoto
- `$` quando non ha importanza a quale sistema sia collegato il terminale.

## Guardando intorno alla vostra casa remota

Molto spesso, molti utenti sono tentati di pensare a un'installazione di calcolo ad alte prestazioni come a una gigantesca macchina magica. A volte si pensa che il computer a cui si accede sia l'intero cluster di calcolo. Ma cosa sta succedendo davvero? A quale computer ci siamo collegati? Il nome del computer correntemente collegato può essere controllato con il comando `hostname`. (Si può anche notare che il nome dell'host corrente fa parte del nostro prompt)

```bash
[yourUsername@login1 ~] hostname
```

```output
login1
```

Quindi, siamo sicuramente sul computer remoto. Quindi, scopriamo dove ci troviamo eseguendo `pwd` per **stampare la **cartella di lavoro **d**.

```bash
[yourUsername@login1 ~] pwd
```

```output
/home/yourUsername
```

Ottimo, sappiamo dove siamo! Vediamo cosa c'è nella nostra directory corrente:

```bash
[yourUsername@login1 ~] ls
```

```output
id_ed25519.pub
```

Gli amministratori del sistema potrebbero aver configurato la vostra home directory con alcuni file, cartelle e collegamenti (scorciatoie) utili allo spazio riservato a voi su altri filesystem. Se non l'hanno fatto, la home directory potrebbe apparire vuota. Per ricontrollare, includere i file nascosti nell'elenco delle directory:

```bash
[yourUsername@login1 ~] ls -a
```

```output
  .            .bashrc           id_ed25519.pub
  ..           .ssh
```

Nella prima colonna, `.` è un riferimento alla directory corrente e `..` un riferimento al suo genitore (`/home`). Gli altri file, o file simili, possono essere visualizzati o meno: `.bashrc` è un file di configurazione della shell, che può essere modificato con le proprie preferenze; e `.ssh` è una directory che memorizza le chiavi SSH e un registro delle connessioni autorizzate.

### Installare la chiave SSH

::::::::::::::::::::::::::::::::::::::::: callout

## Ci può essere un modo migliore

Le politiche e le pratiche per la gestione delle chiavi SSH variano tra i cluster HPC: seguire le indicazioni fornite dagli amministratori del cluster o la documentazione. In particolare, se esiste un portale online per la gestione delle chiavi SSH, utilizzare quello invece delle indicazioni qui riportate.

::::::::::::::::::::::::::::::::::::::::::::::::::

Se si è trasferita la chiave pubblica SSH con `scp`, si dovrebbe vedere `id_ed25519.pub` nella propria home directory. Per "installare" questa chiave, deve essere elencata in un file chiamato `authorized_keys` sotto la cartella `.ssh`.

Se la cartella `.ssh` non è stata elencata sopra, allora non esiste ancora: crearla.

```bash
[yourUsername@login1 ~] mkdir ~/.ssh
```

Ora, usare `cat` per stampare la chiave pubblica, ma reindirizzare l'output, aggiungendolo al file `authorized_keys`:

```bash
[yourUsername@login1 ~] cat ~/id_ed25519.pub >> ~/.ssh/authorized_keys
```

Tutto qui! Se la chiave e l'agente sono stati configurati correttamente, non dovrebbe essere richiesta la password per la chiave SSH.

```bash
[yourUsername@login1 ~] logout
```

```bash
[you@laptop:~]$ ssh yourUsername@cluster.hpc-carpentry.org
```

[gh-ssh]: https://docs.github.com/en/authentication/connecting-to-github-with-ssh
[keepass]: https://keepass.info
[bitwarden]: https://bitwarden.com
[wiki-rsa]: https://en.wikipedia.org/wiki/RSA_\(cryptosystem\)
[wiki-dsa]: https://en.wikipedia.org/wiki/EdDSA
[putty-gen]: https://tartarus.org/~simon/putty-prerel-snapshots/htmldoc/Chapter8.html#pubkey-puttygen
[ssh-agent]: https://www.ssh.com/academy/ssh/agent
[putty-agent]: https://tartarus.org/~simon/putty-prerel-snapshots/htmldoc/Chapter9.html#pageant

:::::::::::::::::::::::::::::::::::::::: keypoints

- Un sistema HPC è un insieme di macchine collegate in rete.
- I sistemi HPC forniscono in genere nodi di accesso e una serie di nodi worker.
- Le risorse presenti sui nodi indipendenti (worker) possono variare per volume e tipo (quantità di RAM, architettura del processore, disponibilità di filesystem montati in rete, ecc.)
- I file salvati su un nodo sono disponibili su tutti i nodi.

::::::::::::::::::::::::::::::::::::::::::::::::::


