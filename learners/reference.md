---
permalink: /reference/
title: Base di conoscenza
---


### Riferimento rapido o "Schede di sicurezza" per i comandi del sistema di accodamento

Cercate online quello più adatto a voi, ma eccone alcuni per iniziare:

- [Slurm summary](https://slurm.schedmd.com/pdfs/summary.pdf) da SchedMD
- [Riassunto di Torque/PBS](https://gif.biotech.iastate.edu/torque-pbs-job-management-cheat-sheet) da Iowa State
- [Traduzione tra Slurm e PBS](https://www.msi.umn.edu/slurm/pbs-conversion) da Università del Minnesota

### Unità e lingua

La memoria e il disco di un computer sono misurati in unità chiamate *Byte* (un Byte è pari a 8 bit). Poiché i file e la memoria di oggi sono diventati grandi rispetto agli standard storici, i volumi sono indicati utilizzando i prefissi [SI](https://en.wikipedia.org/wiki/International_System_of_Units). Quindi 1000 Byte sono un Kilobyte (kB), 1000 Kilobyte sono un Megabyte (MB), 1000 Megabyte sono un Gigabyte (GB), ecc.

La storia e il linguaggio comune hanno tuttavia mescolato questa notazione con un significato diverso. Quando si dice "Kilobyte", si intende invece 1024 Byte. In questo senso, un Megabyte è 1024 Kilobyte.

Per ovviare a questa ambiguità, il [Sistema Internazionale delle Quantità](https://en.wikipedia.org/wiki/International_System_of_Quantities) standardizza i prefissi *binari* (con base 2<sup>10</sup>=1024) con i prefissi Kibi (ki), Mebi (Mi), Gibi (Gi), ecc. Per maggiori dettagli, vedere [qui](https://en.wikipedia.org/wiki/Binary_prefix).

### "Nessun file o directory di questo tipo" o "simbolo 0096" Errori

`scp` e `rsync` possono dare un errore di perplessità su file che esistono eccome. Una fonte di questi errori è il copia-incolla degli argomenti della riga di comando da parte dei browser Web, dove la stringa a doppio trattino `--` viene resa come un carattere em-dash "-" (o en-dash "-", o barra orizzontale `―`). Ad esempio, invece di mostrare la velocità di trasferimento in tempo reale, il seguente comando fallisce misteriosamente.

```bash
{{ site.local.prompt }} rsync —progress my_precious_data.txt {{ site.remote.user }}@{{ site.remote.login }}
rsync: link_stat "/home/{{ site.local.user }}/—progress" failed:
No such file or directory (2)
rsync error: some files/attrs were not transferred (see previous errors)
(code 23) at main.c(1207) [sender=3.1.3]
```

Il comando corretto, diverso solo da due caratteri, ha successo:

```bash
{{ site.local.prompt }} rsync --progress my_precious_data.txt {{ site.remote.user }}@{{ site.remote.login }}
```

Abbiamo fatto del nostro meglio per racchiudere tutti i comandi in blocchi di codice, il che impedisce questa sottile conversione. Se si riscontra questo errore, si prega di aprire un problema o una richiesta di pull sul repository delle lezioni per aiutare gli altri a evitarlo.

### Trasferimento interattivo di file con `sftp`

`scp` è utile, ma cosa succede se non si conosce la posizione esatta di ciò che si vuole trasferire? O forse non siamo ancora sicuri di quali file vogliamo trasferire. `sftp` è un modo interattivo di scaricare e caricare i file. Colleghiamoci a un cluster, usando `sftp` - noterete che funziona allo stesso modo di SSH:

```bash
{{ site.local.prompt }} sftp yourUsername@remote.computer.address
```

Questo avvia quella che sembra essere una shell bash (anche se il prompt dice `sftp>`). Tuttavia, abbiamo accesso solo a un numero limitato di comandi. Possiamo vedere quali comandi sono disponibili con `help`:

```bash
sftp> help
```

```output
Available commands:
bye                                Quit sftp
cd path                            Change remote directory to 'path'
chgrp grp path                     Change group of file 'path' to 'grp'
chmod mode path                    Change permissions of file 'path' to 'mode'
chown own path                     Change owner of file 'path' to 'own'
df [-hi] [path]                    Display statistics for current directory or
                                   filesystem containing 'path'
exit                               Quit sftp
get [-afPpRr] remote [local]       Download file
reget [-fPpRr] remote [local]      Resume download file
reput [-fPpRr] [local] remote      Resume upload file
help                               Display this help text
lcd path                           Change local directory to 'path'
lls [ls-options [path]]            Display local directory listing
lmkdir path                        Create local directory
ln [-s] oldpath newpath            Link remote file (-s for symlink)
lpwd                               Print local working directory
ls [-1afhlnrSt] [path]             Display remote directory listing

# omitted further output for clarity
```

Si noti la presenza di più comandi che fanno riferimento a locale e remoto. In realtà siamo collegati a due computer contemporaneamente (con due directory funzionanti!).

Per mostrare la nostra directory di lavoro remota:

```bash
sftp> pwd
```

```output
Remote working directory: /global/home/yourUsername
```

Per mostrare la nostra directory di lavoro locale, aggiungiamo un `l` davanti al comando:

```bash
sftp> lpwd
```

```output
Local working directory: /home/jeff/Documents/teaching/hpc-intro
```

Lo stesso schema segue per tutti gli altri comandi:

- `ls` mostra il contenuto della nostra directory remota, mentre `lls` mostra il contenuto della nostra directory locale.
- `cd` cambia la directory remota, `lcd` cambia quella locale.

Per caricare un file, digitare `put some-file.txt` (qui funziona il completamento a tabulazione).

```bash
sftp> put config.toml
```

```output
Uploading config.toml to /global/home/yourUsername/config.toml
config.toml                                  100%  713     2.4KB/s   00:00
```

Per scaricare un file digitiamo `get some-file.txt`:

```bash
sftp> get config.toml
```

```output
Fetching /global/home/yourUsername/config.toml to config.toml
/global/home/yourUsername/config.toml        100%  713     9.3KB/s   00:00
```

E possiamo mettere/togliere file in modo ricorsivo aggiungendo semplicemente `-r`. Si noti che la directory deve essere già presente.

```bash
sftp> mkdir content
sftp> put -r content/
```

```output
Uploading content/ to /global/home/yourUsername/content
Entering content/
content/scheduler.md              100%   11KB  21.4KB/s   00:00
content/index.md                  100% 1051     7.2KB/s   00:00
content/transferring-files.md     100% 6117    36.6KB/s   00:00
content/.transferring-files.md.sw 100%   24KB  28.4KB/s   00:00
content/cluster.md                100% 5542    35.0KB/s   00:00
content/modules.md                100%   17KB 158.0KB/s   00:00
content/resources.md              100% 1115    29.9KB/s   00:00
```

Per uscire, digitiamo `exit` o `bye`.





