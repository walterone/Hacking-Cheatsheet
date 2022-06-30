## Static vs Shared Executables

A **shared executable** will only contain the code that you're actually compiling into the resulting file. All of the C library calls, like printf(), socket(), and so on, are implemented in "shared libraries". Shared libraries are stored in /usr/lib32 and other locations, and their filename always ends with .so. When the executable is run, any time a library function is called, the code is loaded from a shared library at RUN TIME. This means that the linker has to decide at compile time what specific library names the executable will rely on.

On the other hand, a static executable will include all of the code needed for the executable to run, libraries included. The libraries are chosen at link time. Static libraries are stored in the same locations as shared libraries, but their filenames end with .a.

UN file statico è molto più pesante (ovviamente).

Per controllare il tipo di file usare il tool "file":
![[Pasted image 20220513235842.png]]

Invece per vedere (SOLO NEGLI ESEGUIBILI SHARED) le librerie dipendenti usare "ldd":
![[Pasted image 20220513235806.png]]

Varie opzioni del compilatore nel caso ci sono problemi (le varie librerie sono state installate sulla mai kali)

`gcc -m32 -Wl,--hash-style=both -o <output filename> <input filename>`

	
	
## Cross Compiling

[[AV Bypass]]
