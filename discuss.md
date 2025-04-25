---
title: I prossimi passi
teaching: 10
exercises: 60
questions:
- How can I get started on using HPC?
- Where can I get help to start using HPC?
objectives:
- Get help to get your work up and running on an HPC system
- Understand where you can get help from in the future
keypoints:
- Understand the next steps for you in using HPC.
- Understand how you can access help and support to use HPC.
---


Ora sapete abbastanza sull'HPC per esplorare come utilizzarlo per il vostro lavoro o per capire quali sono i suoi potenziali vantaggi. Potreste anche avere un'idea di dove possano trovarsi le barriere e le difficoltà e avere ulteriori domande su come iniziare a usare e/o provare l'HPC nella vostra zona.

Questa sessione è stata progettata per dare l'opportunità di esplorare queste domande e questi problemi. Gli istruttori e gli assistenti del corso saranno a disposizione per rispondere alle vostre domande e discutere con voi i passi successivi.

::::::::::::::::::::::::::::::::::::::::: callout

## Discussioni potenziali

Gli argomenti che potreste discutere con gli istruttori e gli assistenti potrebbero essere i seguenti:

- Il vostro flusso di lavoro computazionale e dove l'informatica avanzata potrebbe essere d'aiuto
- Come ottenere l'accesso alle strutture per il proprio lavoro
- Come ottenere aiuto e supporto per far funzionare il proprio lavoro con l'informatica avanzata. Ad esempio, sviluppo di software, formazione continua, accesso alle competenze locali

::::::::::::::::::::::::::::::::::::::::::::::::::

## Opzioni per questa sessione

Ci sono diverse opzioni per il lavoro pratico durante questa sessione. Le sfide che seguono includono: l'esplorazione del proprio lavoro; un esempio esteso che utilizza un'applicazione HPC parallela; un esempio esteso che utilizza l'high throughput computing su più analisi seriali. Se volete usare la sessione per qualcos'altro (ad esempio per discutere con gli istruttori/aiutanti come descritto sopra), sentitevi liberi di farlo. L'idea della sessione è quella di aiutarvi a fare il bootstrap del vostro uso del calcolo avanzato e questo varierà da individuo a individuo!

::::::::::::::::::::::::::::::::::::::: challenge

## Esplorazione del lavoro con l'HPC

Se avete un esempio pratico di qualcosa che rientra nel vostro ambito di lavoro e che vorreste fosse aiutato a mettere in funzione su un sistema HPC o a esplorare le prestazioni di un sistema HPC, è fantastico! Sentitevi liberi di discuterne con noi e di fare domande (sia tecniche che non).


::::::::::::::::::::::::::::::::::::::::::::::::::

::::::::::::::::::::::::::::::::::::::: challenge

## Esplorazione delle prestazioni di GROMACS

[GROMACS](https://www.gromacs.org) è un pacchetto di modellazione biomolecolare leader a livello mondiale, molto utilizzato sui sistemi HPC di tutto il mondo. La scelta delle risorse migliori per i calcoli di GROMACS non è banale perché dipende da molti fattori, tra cui:

- L'hardware sottostante del sistema HPC utilizzato
- Il sistema reale che viene modellato dal pacchetto GROMACS
- Il rapporto tra processi e thread utilizzati per il calcolo parallelo

In questo esercizio, dovrete cercare di decidere una buona scelta di risorse e impostazioni su {{ site.remote.name }} per un tipico sistema biomolecolare. Ciò comporterà:

- Download del file di input per GROMACS da [{{ site.url }}{{site.baseurl }}/files/ion-channel.tpr]( {{ site.url }}{{site.baseurl }}/files/ion-channel.tpr)
- Scrivere uno script per l'invio di un lavoro per eseguire GROMACS su {{ site.remote.name }} utilizzando la documentazione di sistema
- Variare il numero di nodi (da 1 a 32 nodi è un buon punto di partenza) utilizzati per il lavoro di GROMACS e analizzare le prestazioni (in n/giorno)
- Usare i risultati di questo studio per proporre una buona scelta di risorse per il calcolo di GROMACS

Se si vuole andare oltre questo compito iniziale, ci sono diversi modi interessanti per farlo. Ad esempio:

- Variare il numero di thread utilizzati per ogni processo
- Ridurre il numero di core utilizzati per ogni nodo
- Consentire al calcolo di utilizzare il multithreading simmetrico (SMT) se abilitato

Chiedete maggiori informazioni su queste opzioni a un assistente!


::::::::::::::::::::::::::::::::::::::::::::::::::

::::::::::::::::::::::::::::::::::::::: challenge

## Esecuzione di molte analisi BLAST+ seriali in parallelo

[BLAST+](https://blast.ncbi.nlm.nih.gov/Blast.cgi?CMD=Web&PAGE_TYPE=BlastDocs&DOC_TYPE=Download) trova regioni di somiglianza tra sequenze biologiche. Il programma confronta le sequenze nucleotidiche o proteiche con i database di sequenze e calcola la significatività statistica.

In questo esercizio dovrete utilizzare quanto appreso finora per impostare un modo per eseguire più analisi BLAST+ seriali in parallelo. Ci sono molti modi diversi per farlo, che possono essere usati da soli o in combinazione. Alcune idee includono:

- Uso degli array di lavori {{ site.sched.name }} per eseguire più copie su nodi diversi
- Utilizzo di un ciclo di bash all'interno di un nodo
- Utilizzo di GNU parallel in un nodo

Abbiamo preparato un set di dati di esempio con 100 sequenze da analizzare (in realtà si tratta di 10 sequenze ripetute 10 volte). Questo set è basato su [BLAST GNU Parallel example](https://github.com/LangilleLab/microbiome_helper/wiki/Quick-Introduction-to-GNU-Parallel)

Questo esercizio prevede:

- Scaricare ed espandere il dataset sul sistema HPC da: [{{ site.url }}{{site.baseurl }}/files/parallel\example.tar.gz]({{ site.url }}files/parallel_example.tar.gz)

- Scrivere uno script per l'invio di un lavoro per eseguire una singola analisi utilizzando il modulo `blast` e il seguente comando:

  ```bash
  blastp -db pdb_blast_db_example/pdb_seqres.txt -query test_seq_0.fas
  -evalue 0.0001 -word_size 7  -max_target_seqs 10 -num_threads 1 \
  -out output_seq_0.blast -outfmt "6 std stitle staxids sscinames"
  ```

  dove il carattere `\` indica a `bash` che il comando continua sulla riga successiva. Si noti che non ci sarà alcun output da questo allineamento, se funziona correttamente).

- Scegliere un metodo per eseguire più copie dell'analisi per completare tutti i 100 compiti di analisi in modo parallelo (non tutti e 100 devono essere eseguiti contemporaneamente).

È possibile approfondire l'analisi studiando diversi modi per parallelizzare questo problema e/o combinando più strategie parallele.

Si potrebbe anche studiare la variazione delle prestazioni con l'esecuzione di più copie su un nodo. A che punto l'hardware diventa sovraccarico?


::::::::::::::::::::::::::::::::::::::::::::::::::



