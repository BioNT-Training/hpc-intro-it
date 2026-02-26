---
site: sandpaper::sandpaper_site
---

::::::::::::::::::::::::::::::::::::::::::  callout

## Dichiarazione di non responsabilità

Questo materiale formativo è destinato esclusivamente a scopi educativi e
informativi. Descrive come utilizzare un ambiente HPC basato su
[SLURM](https://slurm.schedmd.com/overview.html),
[moduli](https://lmod.readthedocs.io)
e tecnologie correlate, ma non fornisce accesso
all'infrastruttura computazionale necessaria per completare gli esercizi.

I partecipanti sono responsabili dell'organizzazione del proprio accesso a
risorse computazionali adeguate.

In alternativa, gli utenti possono considerare la configurazione di un
ambiente di test locale utilizzando il progetto open source
[slurm-docker-cluster](https://github.com/giovtorres/slurm-docker-cluster).
Un esempio dettagliato di utilizzo di questo progetto è descritto in un
[articolo di blog di terze parti di Thomas Sandmann](https://tomsing1.github.io/blog/posts/slurm_docker_cluster/).

L'uso di strumenti o documentazione di terze parti avviene a propria
discrezione e rischio.

::::::::::::::::::::::::::::::::::::::::::::::::::

Questo workshop è un'introduzione all'uso efficace dei sistemi di calcolo ad alte prestazioni. Non è possibile coprire tutti i casi o fornire un corso esaustivo sulla programmazione parallela in soli due giorni di lezione. Questo workshop intende invece fornire agli studenti una buona introduzione e una panoramica degli strumenti disponibili e di come utilizzarli in modo efficace.

:::::::::::::::::::::::::::::::::::::::::: prereq

## Prerequisiti

Per questa lezione è necessaria un'esperienza a riga di comando. Si consiglia ai partecipanti di seguire [shell-novice](https://swcarpentry.github.io/shell-novice/), se sono alle prime armi con la riga di comando (nota anche come terminale o shell).

::::::::::::::::::::::::::::::::::::::::::::::::::

Alla fine di questo workshop, gli studenti sapranno come:

- Identificare i problemi che un cluster può aiutare a risolvere
- Utilizzare la shell UNIX (nota anche come terminale o linea di comando) per connettersi a un cluster.
- Trasferimento di file su un cluster.
- Inviare e gestire lavori su un cluster utilizzando uno scheduler.
- Osservare i vantaggi e i limiti dell'esecuzione parallela.

::::::::::::::::::::::::::::::::::::::::: callout

## Iniziare

Per iniziare, seguire le [Istruzioni per la configurazione](learners/setup.md) per assicurarsi di avere un terminale e un'applicazione SSH.

::::::::::::::::::::::::::::::::::::::::::::::::::

Si noti che questa è la bozza della release di HPC Carpentry. Commenti e feedback sono benvenuti.

::::::::::::::::::::::::::::::::::::::::: callout

## Per gli istruttori

Se insegnate questa lezione in un workshop, consultate le [Note per gli istruttori] (instructors/instructor-notes.md).

::::::::::::::::::::::::::::::::::::::::::::::::::


