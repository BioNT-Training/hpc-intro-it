---
title: Accesso al software tramite moduli
teaching: 30
exercises: 15
---




::::::::::::::::::::::::::::::::::::::: objectives

- Carica e utilizza un pacchetto software.
- Spiegate come cambia l'ambiente della shell quando il meccanismo dei moduli carica o scarica i pacchetti.

::::::::::::::::::::::::::::::::::::::::::::::::::

:::::::::::::::::::::::::::::::::::::::: questions

- Come si caricano e scaricano i pacchetti software?

::::::::::::::::::::::::::::::::::::::::::::::::::

In un sistema di calcolo ad alte prestazioni, raramente il software che vogliamo usare è disponibile al momento dell'accesso. È installato, ma è necessario "caricarlo" prima che possa essere eseguito.

Prima di iniziare a utilizzare i singoli pacchetti software, tuttavia, è necessario comprendere il ragionamento alla base di questo approccio. I tre fattori principali sono:

- incompatibilità software
- versioning
- dipendenze

L'incompatibilità del software è un grosso grattacapo per i programmatori. A volte, la presenza (o l'assenza) di un pacchetto software ne rompe altri che dipendono da esso. Due esempi ben noti sono le versioni dei compilatori Python e C. Python 3 fornisce un comando `python` che è in conflitto con quello fornito da Python 2. Il software compilato con una versione più recente del compilatore C non è in grado di funzionare. Il software compilato con una versione più recente delle librerie C e poi eseguito su una macchina in cui sono installate librerie C più vecchie, darà luogo a uno spiacevole errore `'GLIBCXX_3.4.20' not found`.

La gestione delle versioni del software è un altro problema comune. Un gruppo di lavoro potrebbe dipendere da una certa versione di un pacchetto per il proprio progetto di ricerca; se la versione del software dovesse cambiare (per esempio, se un pacchetto venisse aggiornato), i risultati potrebbero risentirne. Avere accesso a più versioni del software permette a un gruppo di ricercatori di evitare che i problemi di versione del software influenzino i loro risultati.

Le dipendenze sono quando un particolare pacchetto software (o anche una particolare versione) dipende dall'accesso a un altro pacchetto software (o anche a una particolare versione di un altro pacchetto software). Ad esempio, il software per la scienza dei materiali VASP può dipendere dalla disponibilità di una particolare versione della libreria software FFTW (Fastest Fourier Transform in the West) per funzionare.

## Moduli d'ambiente

I moduli d'ambiente sono la soluzione a questi problemi. Un *modulo* è una descrizione autonoma di un pacchetto software: contiene le impostazioni necessarie per eseguire un pacchetto software e, di solito, codifica le dipendenze necessarie da altri pacchetti software.

Esistono diverse implementazioni di moduli di ambiente comunemente utilizzate sui sistemi HPC: le due più comuni sono *TCL modules* e *Lmod*. Entrambi utilizzano una sintassi simile e i concetti sono gli stessi, quindi imparare a usarne uno vi permetterà di utilizzare quello installato sul sistema che state usando. In entrambe le implementazioni il comando `module` è usato per interagire con i moduli di ambiente. Al comando viene solitamente aggiunto un ulteriore sottocomando per specificare ciò che si vuole fare. Per un elenco di sottocomandi si può usare `module -h` o `module help`. Come per tutti i comandi, è possibile accedere alla guida completa nelle pagine di *man* con `man module`.

All'accesso si può iniziare con un set predefinito di moduli caricati o con un ambiente vuoto; ciò dipende dalla configurazione del sistema in uso.

### Elenco dei moduli disponibili

Per vedere i moduli software disponibili, usare `module avail`:

```bash
[yourUsername@login1 ~] module avail
```

```output
~~~ /cvmfs/pilot.eessi-hpc.org/2020.12/software/x86_64/amd/zen2/modules/all ~~~
  Bazel/3.6.0-GCCcore-x.y.z              NSS/3.51-GCCcore-x.y.z
  Bison/3.5.3-GCCcore-x.y.z              Ninja/1.10.0-GCCcore-x.y.z
  Boost/1.72.0-gompi-2020a               OSU-Micro-Benchmarks/5.6.3-gompi-2020a
  CGAL/4.14.3-gompi-2020a-Python-3.x.y   OpenBLAS/0.3.9-GCC-x.y.z
  CMake/3.16.4-GCCcore-x.y.z             OpenFOAM/v2006-foss-2020a

[removed most of the output here for clarity]

  Where:
   L:        Module is loaded
   Aliases:  Aliases exist: foo/1.2.3 (1.2) means that "module load foo/1.2"
             will load foo/1.2.3
   D:        Default Module

Use "module spider" to find all possible modules and extensions.
Use "module keyword key1 key2 ..." to search for all possible modules matching
any of the "keys".
```

### Elenco dei moduli attualmente caricati

Si può usare il comando `module list` per vedere quali moduli sono attualmente caricati nel proprio ambiente. Se non è stato caricato alcun modulo, verrà visualizzato un messaggio che lo indica

```bash
[yourUsername@login1 ~] module list
```

```output
No Modulefiles Currently Loaded.
```

## Caricamento e scaricamento del software

Per caricare un modulo software, usare `module load`. In questo esempio useremo Python 3.

Inizialmente, Python 3 non viene caricato. Si può verificare usando il comando `which`. il comando `which` cerca i programmi nello stesso modo in cui lo fa Bash, quindi possiamo usarlo per sapere dove è memorizzato un particolare software.

```bash
[yourUsername@login1 ~] which python3
```

Se il comando `python3` non fosse disponibile, si vedrebbe un risultato come

```output
/usr/bin/which: no python3 in (/cvmfs/pilot.eessi-hpc.org/2020.12/compat/linux/x86_64/usr/bin:/opt/software/slurm/bin:/usr/local/bin:/usr/bin:/usr/local/sbin:/usr/sbin:/opt/puppetlabs/bin:/home/yourUsername/.local/bin:/home/yourUsername/bin)
```

Si noti che questo muro di testo è in realtà un elenco, con i valori separati dal carattere `:`. L'output ci dice che il comando `which` ha cercato `python3` nelle seguenti directory, senza successo:

```output
/cvmfs/pilot.eessi-hpc.org/2020.12/compat/linux/x86_64/usr/bin
/opt/software/slurm/bin
/usr/local/bin
/usr/bin
/usr/local/sbin
/usr/sbin
/opt/puppetlabs/bin
/home/yourUsername/.local/bin
/home/yourUsername/bin
```

Tuttavia, nel nostro caso abbiamo un `python3` esistente disponibile, quindi vediamo

```output
/cvmfs/pilot.eessi-hpc.org/2020.12/compat/linux/x86_64/usr/bin/python3
```

Abbiamo bisogno di un Python diverso da quello fornito dal sistema, quindi carichiamo un modulo per accedervi.

Possiamo caricare il comando `python3` con `module load`:

```bash
[yourUsername@login1 ~] module load Python
[yourUsername@login1 ~] which python3
```

```output
/cvmfs/pilot.eessi-hpc.org/2020.12/software/x86_64/amd/zen2/software/Python/3.x.y-GCCcore-x.y.z/bin/python3
```

Allora, cosa è successo?

Per comprendere l'output, occorre innanzitutto capire la natura della variabile d'ambiente `$PATH`. `$PATH` è una variabile d'ambiente speciale che controlla dove un sistema UNIX cerca il software. In particolare, `$PATH` è un elenco di directory (separate da `:`) in cui il sistema operativo cerca un comando prima di arrendersi e dire che non lo trova. Come per tutte le variabili d'ambiente, è possibile stamparle usando `echo`.

```bash
[yourUsername@login1 ~] echo $PATH
```

```output
/cvmfs/pilot.eessi-hpc.org/2020.12/software/x86_64/amd/zen2/software/Python/3.x.y-GCCcore-x.y.z/bin:/cvmfs/pilot.eessi-hpc.org/2020.12/software/x86_64/amd/zen2/software/SQLite/3.31.1-GCCcore-x.y.z/bin:/cvmfs/pilot.eessi-hpc.org/2020.12/software/x86_64/amd/zen2/software/Tcl/8.6.10-GCCcore-x.y.z/bin:/cvmfs/pilot.eessi-hpc.org/2020.12/software/x86_64/amd/zen2/software/GCCcore/x.y.z/bin:/cvmfs/pilot.eessi-hpc.org/2020.12/compat/linux/x86_64/usr/bin:/opt/software/slurm/bin:/usr/local/bin:/usr/bin:/usr/local/sbin:/usr/sbin:/opt/puppetlabs/bin:/home/user01/.local/bin:/home/user01/bin
```

Noterete una somiglianza con l'output del comando `which`. In questo caso, c'è solo una differenza: la diversa directory all'inizio. Quando abbiamo eseguito il comando `module load`, questo ha aggiunto una directory all'inizio del nostro `$PATH`. Esaminiamo cosa c'è:

```bash
[yourUsername@login1 ~] ls /cvmfs/pilot.eessi-hpc.org/2020.12/software/x86_64/amd/zen2/software/Python/3.x.y-GCCcore-x.y.z/bin
```

```output
2to3              nosetests-3.8  python                 rst2s5.py
2to3-3.8          pasteurize     python3                rst2xetex.py
chardetect        pbr            python3.8              rst2xml.py
cygdb             pip            python3.8-config       rstpep2html.py
cython            pip3           python3-config         runxlrd.py
cythonize         pip3.8         rst2html4.py           sphinx-apidoc
easy_install      pybabel        rst2html5.py           sphinx-autogen
easy_install-3.8  __pycache__    rst2html.py            sphinx-build
futurize          pydoc3         rst2latex.py           sphinx-quickstart
idle3             pydoc3.8       rst2man.py             tabulate
idle3.8           pygmentize     rst2odt_prepstyles.py  virtualenv
netaddr           pytest         rst2odt.py             wheel
nosetests         py.test        rst2pseudoxml.py
```

Portando questo alla sua conclusione, `module load` aggiungerà software al vostro `$PATH`. Carica il software. Una nota speciale: a seconda della versione del programma `module` installata nel sito, `module load` caricherà anche le dipendenze software necessarie.

Per dimostrarlo, usiamo `module list`.`module list` mostra tutti i moduli software caricati.

```bash
[yourUsername@login1 ~] module list
```

```output
Currently Loaded Modules:
  1) GCCcore/x.y.z                 4) GMP/6.2.0-GCCcore-x.y.z
  2) Tcl/8.6.10-GCCcore-x.y.z      5) libffi/3.3-GCCcore-x.y.z
  3) SQLite/3.31.1-GCCcore-x.y.z   6) Python/3.x.y-GCCcore-x.y.z
```

```bash
[yourUsername@login1 ~] module load GROMACS
[yourUsername@login1 ~] module list
```

```output
Currently Loaded Modules:
  1) GCCcore/x.y.z                    14) libfabric/1.11.0-GCCcore-x.y.z
  2) Tcl/8.6.10-GCCcore-x.y.z         15) PMIx/3.1.5-GCCcore-x.y.z
  3) SQLite/3.31.1-GCCcore-x.y.z      16) OpenMPI/4.0.3-GCC-x.y.z
  4) GMP/6.2.0-GCCcore-x.y.z          17) OpenBLAS/0.3.9-GCC-x.y.z
  5) libffi/3.3-GCCcore-x.y.z         18) gompi/2020a
  6) Python/3.x.y-GCCcore-x.y.z       19) FFTW/3.3.8-gompi-2020a
  7) GCC/x.y.z                        20) ScaLAPACK/2.1.0-gompi-2020a
  8) numactl/2.0.13-GCCcore-x.y.z     21) foss/2020a
  9) libxml2/2.9.10-GCCcore-x.y.z     22) pybind11/2.4.3-GCCcore-x.y.z-Pytho...
 10) libpciaccess/0.16-GCCcore-x.y.z  23) SciPy-bundle/2020.03-foss-2020a-Py...
 11) hwloc/2.2.0-GCCcore-x.y.z        24) networkx/2.4-foss-2020a-Python-3.8...
 12) libevent/2.1.11-GCCcore-x.y.z    25) GROMACS/2020.1-foss-2020a-Python-3...
 13) UCX/1.8.0-GCCcore-x.y.z
```

In questo caso, il caricamento del modulo `GROMACS` (un pacchetto software bioinformatico) ha caricato anche `GMP/6.2.0-GCCcore-x.y.z` e `SciPy-bundle/2020.03-foss-2020a-Python-3.x.y`. Proviamo a scaricare il pacchetto `GROMACS`.

```bash
[yourUsername@login1 ~] module unload GROMACS
[yourUsername@login1 ~] module list
```

```output
Currently Loaded Modules:
  1) GCCcore/x.y.z                    13) UCX/1.8.0-GCCcore-x.y.z
  2) Tcl/8.6.10-GCCcore-x.y.z         14) libfabric/1.11.0-GCCcore-x.y.z
  3) SQLite/3.31.1-GCCcore-x.y.z      15) PMIx/3.1.5-GCCcore-x.y.z
  4) GMP/6.2.0-GCCcore-x.y.z          16) OpenMPI/4.0.3-GCC-x.y.z
  5) libffi/3.3-GCCcore-x.y.z         17) OpenBLAS/0.3.9-GCC-x.y.z
  6) Python/3.x.y-GCCcore-x.y.z       18) gompi/2020a
  7) GCC/x.y.z                        19) FFTW/3.3.8-gompi-2020a
  8) numactl/2.0.13-GCCcore-x.y.z     20) ScaLAPACK/2.1.0-gompi-2020a
  9) libxml2/2.9.10-GCCcore-x.y.z     21) foss/2020a
 10) libpciaccess/0.16-GCCcore-x.y.z  22) pybind11/2.4.3-GCCcore-x.y.z-Pytho...
 11) hwloc/2.2.0-GCCcore-x.y.z        23) SciPy-bundle/2020.03-foss-2020a-Py...
 12) libevent/2.1.11-GCCcore-x.y.z    24) networkx/2.4-foss-2020a-Python-3.x.y
```

Quindi l'uso di `module unload` "scarica" un modulo e, a seconda di come è configurato un sito, può anche scaricare tutte le dipendenze (nel nostro caso non lo fa). Se si volesse scaricare tutto in una volta, si potrebbe eseguire `module purge` (scarica tutto).

```bash
[yourUsername@login1 ~] module purge
[yourUsername@login1 ~] module list
```

```output
No modules loaded
```

Si noti che `module purge` è informativo. Ci farà anche sapere se un insieme predefinito di pacchetti "appiccicosi" non può essere scaricato (e come scaricarli se lo si desidera veramente).

Si noti che questo processo di caricamento del modulo avviene principalmente attraverso la manipolazione di variabili d'ambiente come `$PATH`. Di solito il trasferimento di dati è minimo o nullo.

Il processo di caricamento dei moduli manipola anche altre variabili d'ambiente speciali, tra cui quelle che influenzano la ricerca delle librerie software da parte del sistema e, talvolta, quelle che indicano ai pacchetti software commerciali dove trovare i server delle licenze.

Il comando module ripristina anche queste variabili d'ambiente della shell al loro stato precedente quando un modulo viene scaricato.

## Versione del software

Finora abbiamo imparato a caricare e scaricare i pacchetti software. Questo è molto utile. Tuttavia, non abbiamo ancora affrontato il problema del versionamento del software. Prima o poi ci si imbatterà in problemi in cui solo una particolare versione di un software sarà adatta. Forse la correzione di un bug fondamentale è avvenuta solo in una certa versione, oppure la versione X ha interrotto la compatibilità con un formato di file utilizzato. In entrambi questi casi, è utile essere molto precisi sul software caricato.

Esaminiamo più da vicino l'output di `module avail`.

```bash
[yourUsername@login1 ~] module avail
```

```output
~~~ /cvmfs/pilot.eessi-hpc.org/2020.12/software/x86_64/amd/zen2/modules/all ~~~
  Bazel/3.6.0-GCCcore-x.y.z              NSS/3.51-GCCcore-x.y.z
  Bison/3.5.3-GCCcore-x.y.z              Ninja/1.10.0-GCCcore-x.y.z
  Boost/1.72.0-gompi-2020a               OSU-Micro-Benchmarks/5.6.3-gompi-2020a
  CGAL/4.14.3-gompi-2020a-Python-3.x.y   OpenBLAS/0.3.9-GCC-x.y.z
  CMake/3.16.4-GCCcore-x.y.z             OpenFOAM/v2006-foss-2020a

[removed most of the output here for clarity]

  Where:
   L:        Module is loaded
   Aliases:  Aliases exist: foo/1.2.3 (1.2) means that "module load foo/1.2"
             will load foo/1.2.3
   D:        Default Module

Use "module spider" to find all possible modules and extensions.
Use "module keyword key1 key2 ..." to search for all possible modules matching
any of the "keys".
```

::::::::::::::::::::::::::::::::::::::: challenge

## Utilizzo dei moduli software negli script

Creare un lavoro in grado di eseguire `python3 --version`. Ricordate che per impostazione predefinita non viene caricato alcun software! Eseguire un lavoro è come accedere al sistema (non si deve dare per scontato che un modulo caricato sul nodo di login sia caricato su un nodo di calcolo).

::::::::::::::: solution

## Soluzione

```bash
[yourUsername@login1 ~] nano python-module.sh
[yourUsername@login1 ~] cat python-module.sh
```

```output
#!/bin/bash
#SBATCH 
r config$sched$comment` -t 00:00:30

module load Python

python3 --version
```

```bash
[yourUsername@login1 ~] sbatch  python-module.sh
```

:::::::::::::::::::::::::

::::::::::::::::::::::::::::::::::::::::::::::::::


:::::::::::::::::::::::::::::::::::::::: keypoints

- Carica il software con `module load softwareName`.
- Scarica il software con `module unload`
- Il sistema dei moduli gestisce automaticamente le versioni del software e i conflitti tra i pacchetti.

::::::::::::::::::::::::::::::::::::::::::::::::::


