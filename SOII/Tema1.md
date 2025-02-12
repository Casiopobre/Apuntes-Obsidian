# Tema 1. Comunicación e sincronización entre procesos e fíos
## Comunicación entre procesos (IPC)
Hai **comunicación implícita** entre procesos xa que todos _comparten o kernel_ (rexión de memoria).

A comunicación entre procesos **emprégase para**:
+ _Pasar información_ entre procesos
+ _Evitar interferencias_ (carreiras críticas entre procesos)
+ Garantir unha _orde correcta_ (sincronización mediante dependencias)
Os dous últimos puntos pódense tratar coas mesmas ferramentas tanto para procesos como para fíos; pero o primeiro punto é moito máis sinxelo para _fíos_, xa que estes _comparten espazo de direccións_ (os procesos non comparten memoria excepto no caso dun espazo virtual ou mediante o envío de mensaxes).
>[! Exemplo] Exemplo: Pipe
Ao executar "comando1 | comando2", o pipe colle a saída do primeiro proceso e a garda nun ficheiro temporal; e comeza a execución do segundo proceso, que tomará o contido do ficheiro temporal (saida de comando1) como entrada. A este ficheiro só pode acceder un proceso á vez, polo que para os sistemas de multiprogramación, existe a opción de marcar puntos de referencia para que se intercalen e o lean pseudoparalelamente.

**Principal inconvinte**: os sistemas son _multiprogramados_, polo que hai varios fíos e procesos simultáneos, polo que poden interferir.
### Condicións de carreira
