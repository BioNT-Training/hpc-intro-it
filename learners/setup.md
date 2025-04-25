---
title: Configurazione
---


Ci sono diversi software che si desidera installare prima del workshop. Anche se la guida all'installazione sarà fornita durante il workshop, si consiglia di installare (o almeno scaricare) questi strumenti in anticipo.

1. [Un'applicazione terminale o un'interfaccia a riga di comando](#dove-dovete-digitare-comandi-come-aprire-una-nuova-shell)
2. [Un'applicazione Secure Shell](#ssh-for-secure-connections)

:::::::::::::::::::::::::::::::::::::::::: prereq

## Bash e SSH

Questa lezione richiede un'applicazione terminale (`bash`, `zsh` o altre) con la capacità di connettersi in modo sicuro a una macchina remota (`ssh`).


::::::::::::::::::::::::::::::::::::::::::::::::::

## Dove digitare i comandi: Come aprire una nuova shell

La shell è un programma che ci permette di inviare comandi al computer e di ricevere l'output. Viene anche chiamata terminale o riga di comando.

Alcuni computer includono un programma Unix Shell predefinito. I passi che seguono descrivono alcuni metodi per identificare e aprire un programma Unix Shell se ne avete già uno installato. Esistono anche opzioni per identificare e scaricare un programma Unix Shell, un emulatore Linux/UNIX o un programma per accedere a Unix Shell su un server.

### Gusci Unix su Windows

I computer con sistema operativo Windows non hanno automaticamente installato un programma di shell Unix. In questa lezione, vi invitiamo a usare un emulatore incluso in Git per Windows, che vi dà accesso sia ai comandi della shell Bash sia a Git. Se avete partecipato a una sessione di workshop di Software Carpentry, è probabile che abbiate già ricevuto istruzioni su come installare Git per Windows.

Una volta installato, è possibile aprire un terminale eseguendo il programma Git Bash dal menu di avvio di Windows.

#### Programmi di shell per Windows

- [Git per Windows][git4win] -- *raccomandato*
- [Sottosistema Windows per Linux][wsl] -- opzione avanzata per Windows 10

:::::::::::::::::::::::::::::::::::::: discussion

## Alternative a Git per Windows

Sono disponibili altre soluzioni per eseguire i comandi Bash su Windows. È ora disponibile uno strumento a riga di comando della shell Bash per Windows 10. Inoltre, è possibile eseguire i comandi Bash su un computer o server remoto che dispone già di una shell Unix, dal proprio computer Windows. Di solito questo può essere fatto attraverso un client Secure Shell (SSH). Uno di questi client, disponibile gratuitamente per i computer Windows, è [PuTTY][putty]. Per informazioni sull'installazione e l'uso di PuTTY, sull'uso dello strumento a riga di comando di Windows 10 o sull'installazione e l'uso di un emulatore Unix/Linux, consultare il riferimento riportato di seguito.

Per gli utenti avanzati, è possibile scegliere una delle seguenti alternative:

- Installare il [sottosistema Windows per Linux][wsl]
- Utilizzare il programma [PowerShell][ms-shell] di Windows
- Leggere [Utilizzo di un emulatore Unix/Linux][emulatore unix] (Cygwin) o di un client Secure Shell (SSH) (PuTTY)

::::::::::::::::::::::::::::::::::::::: challenge

## Avviso

I comandi del sottosistema Windows per Linux (WSL), di PowerShell o di Cygwin possono differire leggermente da quelli mostrati nella lezione o presentati nel workshop. In caso di mancata corrispondenza, chiedete pure: probabilmente non siete i soli.



::::::::::::::::::::::::::::::::::::::::::::::::::

::::::::::::::::::::::::::::::::::::::::::::::::::

### Shell Unix su macOS

Su macOS, la shell Unix predefinita è accessibile eseguendo il programma Terminal dalla cartella `/Application/Utilities` nel Finder.

Per aprire il Terminale, provate a eseguire una o entrambe le operazioni seguenti:

- Nel Finder, selezionare il menu Vai, quindi selezionare Utilità. Individuare Terminale nella cartella Utilità e aprirlo.
- Usate la funzione di ricerca del computer Mac 'Spotlight'. Cercare: `Terminal` e premere <kbd>Return</kbd>.

Per un'introduzione, vedere [Come usare il terminale su Mac][mac-terminal].

### Shell Unix su Linux

Nella maggior parte delle versioni di Linux, la shell Unix predefinita è accessibile eseguendo il [(Gnome) Terminal](https://help.gnome.org/users/gnome-terminal/stable/) o [(KDE) Konsole](https://konsole.kde.org/) o [xterm](https://en.wikipedia.org/wiki/Xterm), che possono essere trovati tramite il menu delle applicazioni o la barra di ricerca.

### Casi speciali

Se nessuna delle opzioni precedenti risponde alle vostre esigenze, provate a fare una ricerca online di: `Unix shell [your operating system]`.

## SSH per connessioni sicure

Tutti gli studenti devono avere installato un client SSH. SSH è uno strumento che ci permette di connetterci a un computer remoto e di utilizzarlo come se fosse il nostro.

### SSH per Windows

Git per Windows viene fornito con SSH preinstallato: non è necessario fare nulla.

:::::::::::::::::::::::::::::::::::::: discussion

## Supporto dell'interfaccia grafica per Windows

Se si sa che il software che si eseguirà sul cluster richiede un'interfaccia utente grafica (una finestra GUI deve essere aperta perché l'applicazione funzioni correttamente), installare [MobaXterm](https://mobaxterm.mobatek.net) Home Edition.


::::::::::::::::::::::::::::::::::::::::::::::::::

### SSH per macOS

macOS viene fornito con SSH preinstallato: non è necessario fare nulla.

:::::::::::::::::::::::::::::::::::::: discussion

## Supporto GUI per macOS

Se si sa che il software da eseguire richiede un'interfaccia grafica, installare [XQuartz](https://www.xquartz.org).


::::::::::::::::::::::::::::::::::::::::::::::::::

### SSH per Linux

Linux viene fornito con il supporto SSH e X window preinstallato: non è necessario fare nulla.

<!-- links -->

[git4win]: https://gitforwindows.org/
[wsl]: https://docs.microsoft.com/en-us/windows/wsl/install-win10
[putty]: https://www.chiark.greenend.org.uk/~sgtatham/putty/
[ms-shell]: https://docs.microsoft.com/en-us/powershell/scripting/learn/remoting/ssh-remoting-in-powershell-core?view=powershell-7
[unix-emulator]: https://www.cygwin.com/
[mac-terminal]: https://www.macworld.co.uk/feature/mac-software/how-use-terminal-on-mac-3608274/




