---
title: Esecuzione di un lavoro parallelo
teaching: 30
exercises: 60
---




::::::::::::::::::::::::::::::::::::::: objectives

- Installare un pacchetto Python utilizzando `pip`
- Preparare uno script di invio del lavoro per l'eseguibile parallelo.
- Avvio di lavori con esecuzione parallela.
- Registrare e riassumere i tempi e la precisione dei lavori.
- Descrivere la relazione tra parallelismo dei lavori e prestazioni.

::::::::::::::::::::::::::::::::::::::::::::::::::

:::::::::::::::::::::::::::::::::::::::: questions

- Come si esegue un'attività in parallelo?
- Quali vantaggi derivano dall'esecuzione parallela?
- Quali sono i limiti dei guadagni derivanti dall'esecuzione in parallelo?

::::::::::::::::::::::::::::::::::::::::::::::::::

Ora abbiamo gli strumenti necessari per eseguire un lavoro multiprocessore. Si tratta di un aspetto molto importante dei sistemi HPC, poiché il parallelismo è uno degli strumenti principali di cui disponiamo per migliorare le prestazioni delle attività di calcolo.

Se si è disconnessi, accedere nuovamente al cluster.

```bash
[you@laptop:~]$ ssh yourUsername@cluster.hpc-carpentry.org
```

## Installare il programma Amdahl

Con il codice sorgente di Amdahl sul cluster, possiamo installarlo, il che ci darà accesso all'eseguibile `amdahl`. Spostarsi nella directory estratta, quindi usare il Package Installer for Python, o `pip`, per installarlo nella propria home directory ("utente"):

```bash
[yourUsername@login1 ~] cd amdahl
[yourUsername@login1 ~] python3 -m pip install --user .
```

::::::::::::::::::::::::::::::::::::::::: callout

## Amdahl è un codice Python

Il programma Amdahl è scritto in Python e per installarlo o utilizzarlo è necessario individuare l'eseguibile `python3` sul nodo di accesso. Se non si riesce a trovarlo, provare a elencare i moduli disponibili usando `module avail`, caricare quello appropriato e riprovare il comando.


::::::::::::::::::::::::::::::::::::::::::::::::::

### MPI per Python

Il codice Amdahl ha una sola dipendenza: **mpi4py**. Se non è già stato installato sul cluster, `pip` cercherà di prelevare mpi4py da Internet e di installarlo per l'utente. Se ciò fallisce a causa di un firewall unidirezionale, è necessario recuperare mpi4py sulla propria macchina locale e caricarlo, proprio come abbiamo fatto per Amdahl.

:::::::::::::::::::::::::::::::::::::: discussion

## Recupero e caricamento di `mpi4py`

Se l'installazione di Amdahl non è riuscita perché non è stato possibile installare mpi4py, recuperare il tarball da <https://github.com/mpi4py/mpi4py/tarball/master> quindi `rsync` sul cluster, estrarlo e installarlo:

```bash
[you@laptop:~]$ wget -O mpi4py.tar.gz https://github.com/mpi4py/mpi4py/releases/download/3.1.4/mpi4py-3.1.4.tar.gz
[you@laptop:~]$ scp mpi4py.tar.gz yourUsername@cluster.hpc-carpentry.org:
# or
[you@laptop:~]$ rsync -avP mpi4py.tar.gz yourUsername@cluster.hpc-carpentry.org:
```

```bash
[you@laptop:~]$ ssh yourUsername@cluster.hpc-carpentry.org
[yourUsername@login1 ~] tar -xvzf mpi4py.tar.gz  # extract the archive
[yourUsername@login1 ~] mv mpi4py* mpi4py        # rename the directory
[yourUsername@login1 ~] cd mpi4py
[yourUsername@login1 ~] python3 -m pip install --user .
[yourUsername@login1 ~] cd ../amdahl
[yourUsername@login1 ~] python3 -m pip install --user .
```

::::::::::::::::::::::::::::::::::::::::::::::::::

:::::::::::::::::::::::::::::::::::::: discussion

## Se `pip` solleva un avviso...

`pip` potrebbe avvertire che i binari del pacchetto utente non sono presenti nel PATH.

```warning
WARNING: The script amdahl is installed in "${HOME}/.local/bin" which is
not on PATH. Consider adding this directory to PATH or, if you prefer to
suppress this warning, use --no-warn-script-location.
```

Per verificare se questo avviso rappresenta un problema, utilizzare `which` per cercare il programma `amdahl`:

```bash
[yourUsername@login1 ~] which amdahl
```

Se il comando non restituisce alcun risultato, visualizzando un nuovo prompt, significa che il file `amdahl` non è stato trovato. È necessario aggiornare la variabile d'ambiente denominata `PATH` per includere la cartella mancante. Modificare il file di configurazione della shell come segue, quindi disconnettersi dal cluster e riaccenderlo in modo che abbia effetto.

```bash
[yourUsername@login1 ~] nano ~/.bashrc
[yourUsername@login1 ~] tail ~/.bashrc
```

```output
export PATH=${PATH}:${HOME}/.local/bin
```

Dopo aver effettuato il login a cluster.hpc-carpentry.org, `which` dovrebbe essere in grado di trovare `amdahl` senza difficoltà. Se si è dovuto caricare un modulo Python, caricarlo di nuovo.


::::::::::::::::::::::::::::::::::::::::::::::::::

## Aiuto!

Molti programmi a riga di comando includono un messaggio di "aiuto". Provatelo con `amdahl`:

```bash
[yourUsername@login1 ~] amdahl --help
```

```output
usage: amdahl [-h] [-p [PARALLEL_PROPORTION]] [-w [WORK_SECONDS]] [-t] [-e] [-j [JITTER_PROPORTION]]

optional arguments:
  -h, --help            show this help message and exit
  -p [PARALLEL_PROPORTION], --parallel-proportion [PARALLEL_PROPORTION]
                        Parallel proportion: a float between 0 and 1
  -w [WORK_SECONDS], --work-seconds [WORK_SECONDS]
                        Total seconds of workload: an integer greater than 0
  -t, --terse           Format output as a machine-readable object for easier analysis
  -e, --exact           Exactly match requested timing by disabling random jitter
  -j [JITTER_PROPORTION], --jitter-proportion [JITTER_PROPORTION]
                        Random jitter: a float between -1 and +1
```

Questo messaggio non ci dice molto su ciò che il programma *fa*, ma ci dice i flag importanti che potremmo voler usare quando lo lanciamo.

## Esecuzione del lavoro su un nodo di calcolo

Creare un file di presentazione, richiedere un task su un singolo nodo e lanciarlo.

```bash
[yourUsername@login1 ~] nano serial-job.sh
[yourUsername@login1 ~] cat serial-job.sh
```

```bash
#!/bin/bash
#SBATCH -J solo-job
#SBATCH -p cpubase_bycore_b1
#SBATCH -N 1
#SBATCH -n 1

# Load the computing environment we need
module load Python

# Execute the task
amdahl
```

```bash
[yourUsername@login1 ~] sbatch serial-job.sh
```

Come in precedenza, utilizzare i comandi di stato Slurm per verificare se il lavoro è in esecuzione e quando termina:

```bash
[yourUsername@login1 ~] squeue -u yourUsername
```

Usare `ls` per localizzare il file di output. Il flag `-t` ordina in ordine cronologico inverso: prima il più recente. Qual era l'output?

::::::::::::::: spoiler

## Lettura dell'output del lavoro

L'output del cluster deve essere scritto in un file nella cartella da cui è stato lanciato il lavoro. Ad esempio,

```bash
[yourUsername@login1 ~] ls -t
```

```output
slurm-347087.out  serial-job.sh  amdahl  README.md  LICENSE.txt
```

```bash
[yourUsername@login1 ~] cat slurm-347087.out
```

```output
Doing 30.000 seconds of 'work' on 1 processor,
which should take 30.000 seconds with 0.850 parallel proportion of the workload.

  Hello, World! I am process 0 of 1 on smnode1. I will do all the serial 'work' for 4.500 seconds.
  Hello, World! I am process 0 of 1 on smnode1. I will do parallel 'work' for 25.500 seconds.

Total execution time (according to rank 0): 30.033 seconds
```

:::::::::::::::::::::::::

Come abbiamo visto in precedenza, due dei flag del programma `amdahl` impostano la quantità di lavoro e la proporzione di tale lavoro che è di natura parallela. In base all'output, possiamo vedere che il codice utilizza un valore predefinito di 30 secondi di lavoro parallelo all'85%. Il programma ha funzionato per poco più di 30 secondi in totale e, se facciamo i conti, è vero che il 15% è stato contrassegnato come 'seriale' e l'85% come 'parallelo'.

Poiché abbiamo dato al lavoro una sola CPU, questo lavoro non è stato veramente parallelo: lo stesso processore ha eseguito il lavoro 'seriale' per 4,5 secondi, poi la parte 'parallela' per 25,5 secondi, e non è stato risparmiato tempo. Il cluster può fare di meglio, se lo chiediamo.

## Esecuzione del lavoro parallelo

Il programma `amdahl` utilizza la Message Passing Interface (MPI) per il parallelismo, uno strumento comune nei sistemi HPC.

::::::::::::::::::::::::::::::::::::::::: callout

## Cos'è MPI?

L'interfaccia per il passaggio di messaggi è un insieme di strumenti che consentono a più task in esecuzione simultanea di comunicare tra loro. In genere, un singolo eseguibile viene eseguito più volte, eventualmente su macchine diverse, e gli strumenti MPI vengono utilizzati per informare ogni istanza dell'eseguibile sui suoi processi fratelli e su quale istanza sia. MPI fornisce anche strumenti per consentire la comunicazione tra le istanze per coordinare il lavoro, scambiare informazioni su elementi dell'attività o trasferire dati. Un'istanza MPI ha tipicamente una propria copia di tutte le variabili locali.


::::::::::::::::::::::::::::::::::::::::::::::::::

Mentre gli eseguibili MPI-aware possono generalmente essere eseguiti come programmi autonomi, per poter essere eseguiti in parallelo devono utilizzare un *ambiente di esecuzione MPI*, che è un'implementazione specifica dello *standard* MPI. Per attivare l'ambiente MPI, il programma deve essere avviato con un comando come `mpiexec` (o `mpirun`, o `srun`, ecc. a seconda del run-time MPI da utilizzare), che garantirà l'inclusione del supporto run-time appropriato per il parallelismo.

::::::::::::::::::::::::::::::::::::::::: callout

## Argomenti di runtime MPI

Di per sé, comandi come `mpiexec` possono accettare molti argomenti che specificano quante macchine parteciperanno all'esecuzione, e potrebbero essere necessari se si desidera eseguire un programma MPI da soli (ad esempio, sul proprio portatile). Nel contesto di un sistema di accodamento, tuttavia, è frequente che il run-time MPI ottenga i parametri necessari dal sistema di accodamento, esaminando le variabili d'ambiente impostate al momento del lancio del lavoro.

::::::::::::::::::::::::::::::::::::::::::::::::::

Modifichiamo lo script del lavoro per richiedere più core e utilizzare il run-time MPI.

```bash
[yourUsername@login1 ~] cp serial-job.sh parallel-job.sh
[yourUsername@login1 ~] nano parallel-job.sh
[yourUsername@login1 ~] cat parallel-job.sh
```

```bash
#!/bin/bash
#SBATCH -J parallel-job
#SBATCH -p cpubase_bycore_b1
#SBATCH -N 1
#SBATCH -n 4

# Load the computing environment we need
# (mpi4py and numpy are in SciPy-bundle)
module load Python
module load SciPy-bundle

# Execute the task
mpiexec amdahl
```

Quindi inviare il lavoro. Si noti che il comando di invio non è cambiato rispetto a come abbiamo inviato il lavoro seriale: tutte le impostazioni parallele sono nel file batch anziché nella riga di comando.

```bash
[yourUsername@login1 ~] sbatch parallel-job.sh
```

Come in precedenza, utilizzare i comandi di stato per verificare l'esecuzione del lavoro.

```bash
[yourUsername@login1 ~] ls -t
```

```output
slurm-347178.out  parallel-job.sh  slurm-347087.out  serial-job.sh  amdahl  README.md  LICENSE.txt
```

```bash
[yourUsername@login1 ~] cat slurm-347178.out
```

```output
Doing 30.000 seconds of 'work' on 4 processors,
which should take 10.875 seconds with 0.850 parallel proportion of the workload.

  Hello, World! I am process 0 of 4 on smnode1. I will do all the serial 'work' for 4.500 seconds.
  Hello, World! I am process 2 of 4 on smnode1. I will do parallel 'work' for 6.375 seconds.
  Hello, World! I am process 1 of 4 on smnode1. I will do parallel 'work' for 6.375 seconds.
  Hello, World! I am process 3 of 4 on smnode1. I will do parallel 'work' for 6.375 seconds.
  Hello, World! I am process 0 of 4 on smnode1. I will do parallel 'work' for 6.375 seconds.

Total execution time (according to rank 0): 10.888 seconds
```

::::::::::::::::::::::::::::::::::::::: challenge

## È 4 volte più veloce?

Il lavoro parallelo ha ricevuto 4 volte più processori del lavoro seriale: significa che è stato completato in ¼ del tempo?

::::::::::::::: solution

## Soluzione

Il lavoro parallelo ha richiesto *meno* tempo: 11 secondi sono meglio di 30! Ma è solo un miglioramento di 2,7 volte, non di 4 volte.

Guardate l'output del lavoro:

- Mentre il "processo 0" svolgeva il lavoro seriale, i processi da 1 a 3 svolgevano il lavoro parallelo.
- Mentre il processo 0 ha recuperato il suo lavoro parallelo, gli altri non hanno fatto nulla.

Il processo 0 deve sempre terminare il suo compito seriale prima di poter iniziare il lavoro parallelo. In questo modo si stabilisce un limite inferiore alla quantità di tempo che questo lavoro richiederà, indipendentemente dal numero di core.

Questo è il principio di base della [legge di Amdahl][amdahl], che è un modo per prevedere miglioramenti nel tempo di esecuzione per un carico di lavoro **fisso** che può essere suddiviso ed eseguito in parallelo in una certa misura.



:::::::::::::::::::::::::

::::::::::::::::::::::::::::::::::::::::::::::::::

## Quanto migliora le prestazioni l'esecuzione parallela?

In teoria, dividere un calcolo perfettamente parallelo tra *n* processi MPI dovrebbe produrre una diminuzione del tempo di esecuzione totale di un fattore *n*. Come abbiamo appena visto, i programmi reali hanno bisogno di un po' di tempo per far comunicare e coordinare i processi MPI, e alcuni tipi di calcoli non possono essere suddivisi: vengono eseguiti efficacemente solo su una singola CPU.

Inoltre, se i processi MPI operano su diverse CPU fisiche del computer o su più nodi di calcolo, la comunicazione richiede ancora più tempo di quello necessario quando tutti i processi operano su una singola CPU.

In pratica, è comune valutare il parallelismo di un programma MPI mediante

- esecuzione del programma su un intervallo di CPU,
- registrazione del tempo di esecuzione per ogni esecuzione,
- confrontando ogni tempo di esecuzione con il tempo di utilizzo di una singola CPU.

Poiché "di più è meglio" - il miglioramento è più facile da interpretare dall'aumento di una certa quantità piuttosto che dalla sua diminuzione - i confronti vengono fatti usando il fattore di accelerazione *S*, che è calcolato come il tempo di esecuzione a singola CPU diviso per il tempo di esecuzione a più CPU. Per un programma perfettamente parallelo, un grafico dello speedup *S* in funzione del numero di CPU *n* darebbe una linea retta, *S* = *n*.

Eseguiamo un altro lavoro, per vedere quanto si avvicina a una linea retta il nostro codice `amdahl`.

```bash
[yourUsername@login1 ~] nano parallel-job.sh
[yourUsername@login1 ~] cat parallel-job.sh
```

```bash
#!/bin/bash
#SBATCH -J parallel-job
#SBATCH -p cpubase_bycore_b1
#SBATCH -N 1
#SBATCH -n 8

# Load the computing environment we need
# (mpi4py and numpy are in SciPy-bundle)
module load Python
module load SciPy-bundle

# Execute the task
mpiexec amdahl
```

Quindi inviare il lavoro. Si noti che il comando di invio non è cambiato rispetto a come abbiamo inviato il lavoro seriale: tutte le impostazioni parallele sono nel file batch anziché nella riga di comando.

```bash
[yourUsername@login1 ~] sbatch parallel-job.sh
```

Come in precedenza, utilizzare i comandi di stato per verificare l'esecuzione del lavoro.

```bash
[yourUsername@login1 ~] ls -t
```

```output
slurm-347271.out  parallel-job.sh  slurm-347178.out  slurm-347087.out  serial-job.sh  amdahl  README.md  LICENSE.txt
```

```bash
[yourUsername@login1 ~] cat slurm-347178.out
```

```output
which should take 7.688 seconds with 0.850 parallel proportion of the workload.

  Hello, World! I am process 4 of 8 on smnode1. I will do parallel 'work' for 3.188 seconds.
  Hello, World! I am process 0 of 8 on smnode1. I will do all the serial 'work' for 4.500 seconds.
  Hello, World! I am process 2 of 8 on smnode1. I will do parallel 'work' for 3.188 seconds.
  Hello, World! I am process 1 of 8 on smnode1. I will do parallel 'work' for 3.188 seconds.
  Hello, World! I am process 3 of 8 on smnode1. I will do parallel 'work' for 3.188 seconds.
  Hello, World! I am process 5 of 8 on smnode1. I will do parallel 'work' for 3.188 seconds.
  Hello, World! I am process 6 of 8 on smnode1. I will do parallel 'work' for 3.188 seconds.
  Hello, World! I am process 7 of 8 on smnode1. I will do parallel 'work' for 3.188 seconds.
  Hello, World! I am process 0 of 8 on smnode1. I will do parallel 'work' for 3.188 seconds.

Total execution time (according to rank 0): 7.697 seconds
```

:::::::::::::::::::::::::::::::::::::: discussion

## Uscita non lineare

Quando abbiamo eseguito il lavoro con 4 lavoratori paralleli, il lavoro seriale ha scritto il suo output per primo, poi i processi paralleli hanno scritto il loro output, con il processo 0 che è arrivato per primo e per ultimo.

Con 8 worker, questo non è il caso: poiché i worker paralleli richiedono meno tempo del lavoro seriale, è difficile dire quale processo scriverà per primo il suo output, tranne che *non* sarà il processo 0!

::::::::::::::::::::::::::::::::::::::::::::::::::

Riassumiamo ora il tempo di esecuzione di ciascun lavoro:

| Number of CPUs | Runtime (sec) |
| -------------- | ------------- |
| 1              | 30\.033       |
| 4              | 10\.888       |
| 8              | 7\.697        |

Quindi, utilizzare la prima riga per calcolare gli speedup $S$, utilizzando Python come calcolatore a riga di comando e la formula

$$ S(t_{n}) = \frac{t_{1}}{t_{n}} $$

```bash
[yourUsername@login1 ~] for n in 30.033 10.888 7.697; do python3 -c "print(30.033 / $n)"; done
```

| Number of CPUs | Speedup | Ideal |
| -------------- | ------- | ----- |
| 1              | 1\.0    | 1     |
| 4              | 2\.75   | 4     |
| 8              | 3\.90   | 8     |

I file di output del lavoro ci dicono che questo programma esegue l'85% del suo lavoro in parallelo, lasciando il 15% all'esecuzione seriale. Questo dato sembra ragionevolmente alto, ma il nostro rapido studio dello speedup mostra che per ottenere uno speedup 4×, dobbiamo usare 8 o 9 processori in parallelo. Nei programmi reali, il fattore di velocizzazione è influenzato da

- Progettazione della CPU
- rete di comunicazione tra i nodi di calcolo
- Implementazioni della libreria MPI
- dettagli del programma MPI stesso

Utilizzando la legge di Amdahl, è possibile dimostrare che con questo programma è *impossibile* raggiungere una velocità di 8 volte, indipendentemente dal numero di processori disponibili. I dettagli di questa analisi, con i risultati a supporto, sono lasciati per la prossima lezione del workshop HPC Carpentry, *Flussi di lavoro HPC*.

In un ambiente HPC si cerca di ridurre il tempo di esecuzione di tutti i tipi di lavoro e MPI è un modo estremamente comune per combinare decine, centinaia o migliaia di CPU nella risoluzione di un singolo problema. Per saperne di più sulla parallelizzazione, vedere la lezione [lezione parallela per principianti][lezione parallela per principianti].


[amdahl]: https://en.wikipedia.org/wiki/Amdahl\'s_law
[parallel-novice]: https://www.hpc-carpentry.org/hpc-parallel-novice/


:::::::::::::::::::::::::::::::::::::::: keypoints

- La programmazione parallela consente alle applicazioni di sfruttare l'hardware parallelo.
- Il sistema di accodamento facilita l'esecuzione di compiti paralleli.
- I miglioramenti delle prestazioni derivanti dall'esecuzione parallela non hanno una scala lineare.

::::::::::::::::::::::::::::::::::::::::::::::::::


