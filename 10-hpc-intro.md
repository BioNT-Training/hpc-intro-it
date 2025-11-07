---
title: Perché usare un cluster?
teaching: 15
exercises: 5
---


::::::::::::::::::::::::::::::::::::::: objectives

- Descrivere che cos'è un sistema HPC
- Identificare i vantaggi di un sistema HPC.

::::::::::::::::::::::::::::::::::::::::::::::::::

:::::::::::::::::::::::::::::::::::::::: questions

- Perché dovrei essere interessato al calcolo ad alte prestazioni (HPC)?
- Cosa posso aspettarmi di imparare da questo corso?

::::::::::::::::::::::::::::::::::::::::::::::::::

Spesso i problemi di ricerca che utilizzano l'informatica possono superare le capacità del computer desktop o portatile da cui sono partiti:

- Uno studente di statistica vuole effettuare una convalida incrociata di un modello. Ciò comporta l'esecuzione del modello 1000 volte, ma ogni esecuzione richiede un'ora. L'esecuzione del modello su un computer portatile richiede più di un mese! In questo problema di ricerca, i risultati finali vengono calcolati dopo l'esecuzione di tutti i 1000 modelli, ma in genere viene eseguito solo un modello alla volta (in **serie**) sul portatile. Poiché ognuna delle 1000 esecuzioni è indipendente da tutte le altre, e con un numero sufficiente di computer, è teoricamente possibile eseguirle tutte insieme (in **parallelo**).
- Un ricercatore di genomica ha utilizzato piccoli insiemi di dati di sequenza, ma presto riceverà un nuovo tipo di dati di sequenziamento 10 volte più grandi. Aprire i set di dati su un computer è già impegnativo, analizzare questi set di dati più grandi probabilmente lo manderà in tilt. In questo problema di ricerca, i calcoli richiesti potrebbero essere impossibili da parallelizzare, ma sarebbe necessario un computer con **più memoria** per analizzare i futuri set di dati molto più grandi.
- Un ingegnere sta usando un pacchetto di fluidodinamica che ha un'opzione per l'esecuzione in parallelo. Finora questa opzione non è stata utilizzata su un desktop. Passando dalle simulazioni 2D a quelle 3D, il tempo di simulazione è più che triplicato. Potrebbe essere utile sfruttare questa opzione o funzione. In questo problema di ricerca, i calcoli in ogni regione della simulazione sono ampiamente indipendenti dai calcoli in altre regioni della simulazione. È possibile eseguire i calcoli di ogni regione simultaneamente (in **parallelo**), comunicare i risultati selezionati alle regioni adiacenti, se necessario, e ripetere i calcoli per convergere su un insieme finale di risultati. Passando da un modello 2D a uno 3D, **la quantità di dati e di calcoli aumenta notevolmente**, ed è teoricamente possibile distribuire i calcoli su più computer che comunicano su una rete condivisa.

In tutti questi casi, è necessario l'accesso a più computer. Questi computer dovrebbero essere utilizzabili contemporaneamente, **risolvendo in parallelo i problemi di molti ricercatori**.

## Presentazione del gergo

Aprire [HPC Jargon Buster](../files/jargon#p1) in una nuova scheda. Per presentare il contenuto, premere `C` per aprire una **c**lone in una finestra separata, quindi premere `P` per attivare la modalità di **p**resentazione.

:::::::::::::::::::::::::::::::::::::: challenge

## Avete mai usato un server?

Prendete un minuto e pensate a quali delle vostre interazioni quotidiane con un computer potrebbero richiedere un server remoto o addirittura un cluster per fornirvi i risultati.

::::::::::::::: solution

## Alcune idee

- Controllo della posta elettronica: il vostro computer (magari in tasca) contatta una macchina remota, si autentica e scarica un elenco di nuovi messaggi; carica anche le modifiche allo stato dei messaggi, come ad esempio se sono stati letti, contrassegnati come spazzatura o cancellati. Poiché il vostro non è l'unico account, il server di posta è probabilmente uno dei tanti in un centro dati.
- La ricerca di una frase online comporta il confronto del termine di ricerca con un enorme database di tutti i siti conosciuti, alla ricerca di corrispondenze. Questa operazione di "interrogazione" può essere semplice, ma la costruzione del database è un [compito monumentale][mapreduce]! I server sono coinvolti in ogni fase.
- La ricerca di indicazioni stradali su un sito web di mappatura comporta il collegamento dei punti (A) di partenza e (B) di arrivo [attraversando un grafo][dijkstra] alla ricerca del percorso "più breve" in base alla distanza, al tempo, alla spesa o a un'altra metrica. Convertire una mappa nella forma corretta è relativamente semplice, ma calcolare tutti i possibili percorsi tra A e B è costoso.

Il controllo della posta elettronica potrebbe essere seriale: la macchina si connette a un server e scambia dati. Anche la ricerca, interrogando il database per il termine di ricerca (o gli endpoint), potrebbe essere seriale, in quanto una macchina riceve l'interrogazione e restituisce il risultato. Tuttavia, l'assemblaggio e la memorizzazione dell'intero database vanno ben oltre le capacità di una sola macchina. Pertanto, queste funzioni vengono svolte in parallelo da una vasta collezione di server ["hyperscale"][hyperscale] che lavorano insieme.



:::::::::::::::::::::::::

::::::::::::::::::::::::::::::::::::::::::::::::::



[mapreduce]: https://en.wikipedia.org/wiki/MapReduce
[dijkstra]: https://en.wikipedia.org/wiki/Dijkstra%27s_algorithm
[hyperscale]: https://en.wikipedia.org/wiki/Hyperscale_computing


:::::::::::::::::::::::::::::::::::::::: keypoints

- Il calcolo ad alte prestazioni (HPC) comporta tipicamente la connessione a sistemi di calcolo molto grandi in altre parti del mondo.
- Questi altri sistemi possono essere utilizzati per eseguire lavori che sarebbero impossibili o molto più lenti su sistemi più piccoli.
- Le risorse HPC sono condivise da più utenti.
- Il metodo standard di interazione con questi sistemi è tramite un'interfaccia a riga di comando.

::::::::::::::::::::::::::::::::::::::::::::::::::



