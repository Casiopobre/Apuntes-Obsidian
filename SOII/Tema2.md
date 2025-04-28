# Tema 2. Interbloqueos 
Tódolos SO teñen a capacidade de outorgar a un proceso o acceso exclusivo a certos recursos para evitar que se produzan incoherencias.

> **Interbloqueo**: situación na que dous ou máis procesos quedan bloqueados indefinidamente debido a un conflicto entre as súas diferentes necesidades. Polo tanto dise que:
> _Un conxunto de procesos está nun interbloqueo se cada proceso no conxunto está bloqueado esperando un evento que só pode ser ocasionado por outro proceso do conxunto_.

Os interbloqueos ocorren tanto nos recursos de hardware como nos de software, e se deben a que os procesos compiten polo emprego deses recursos.
## Recursos
> **Recurso**: obxeto (dispositivo hardware, peza de información...) que o SO outorga aos procesos e que se debe adquirir, empregar e liberar posteriormente.

Hai dous **tipos** de recurso:
+ **Apropitivos**: _pódeselle quitar_ ao proceso que o posúe sen efectos daniños (ex: memoria)
+ **Non apropiativos**: _non se lle pode quitar_ ao propietario actual sen efectos daniños (ex: CD-ROM)
\*Os máis problemáticos en termos de interbloqueos son os non apropiativos, xa que os conflictos dos apropiativos se poden resolver mediante a asignación dos recursos dun proceso a outro.

**Secuencia de accións** para solicitar un recurso:
1. _Solicitar_ o recurso: se non está dispoñible, esperar (nun bucle de solicitar, inactivo, volver solicitar)
2. _Empregar_ o recurso
3. _Liberar_ o recurso

### Adquisición de recursos
Para algúns tipos de recursos, é responsabilidade dos procesos de usuario administrar o seu uso, por exemplo, **asociando un semáforo con cada recurso**. Estos semáforos inicialízanse a 1 e implementan a _secuencia de accións_ da seguinte forma:
1. _Down_ $\rightarrow$ adquirir recurso
2. _Usar_ recurso
3. _Up_ $\rightarrow$ liberar recurso
Si os procesos necesitan _dous ou máis recursos_, estes se adquiren de **forma secuencial**, xa que a orde na que se adquiren os recursos é importante.

> [!Exemplo]
> Imaxina unha situación na que temos dous procesos, A e B, e dous recursos. Na figura (a) ambos procesos piden os recursos na mesma orde, e na (b), nunha orde distinta. 
> + (a): Un dos procesos adquire o primeiro recurso, despois o segundo e realiza o seu traballo. Se o outro proceso quere calquera dos recursos antes de que o primeiro remate con eles, se bloquea esperando.
> + (b): Un dos procesos adquire o primer recurso, despois o segundo proceso adquire o segundo procso: temos un interbloqueo, xa que o primer proceso non pode avazar sen o regundo recurso e viceversa.
> ![[interbloqueosSemaforos.png| center | 400]]

## Introducción aos interbloqueos



