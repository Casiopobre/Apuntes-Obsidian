# Tema 2. Interbloqueos 
Tódolos SO teñen a capacidade de outorgar a un proceso o acceso exclusivo a certos recursos para evitar que se produzan incoherencias.

> **Interbloqueo**: situación na que dous ou máis procesos quedan bloqueados indefinidamente debido a un conflicto entre as súas diferentes necesidades. Polo tanto dise que:
> _Un conxunto de procesos está nun interbloqueo se cada proceso no conxunto está bloqueado esperando un evento que só pode ser ocasionado por outro proceso do conxunto_.

Os interbloqueos ocorren tanto nos recursos de hardware como nos de software, e se deben a que os procesos compiten polo emprego deses recursos. Cando ocorre un interbloqueo, tódolos procesos involucrados seguirán _esperando indefinidamente_, polo que supondremos que cada proceso só ten un fío e que non hai interrupcións que desperten un proceso bloqueado. 
## Recursos
> **Recurso**: obxeto (dispositivo hardware, peza de información...) que o SO outorga aos procesos e que se debe adquirir, empregar e liberar posteriormente.

Hai dous **tipos** de recurso:
+ **Apropitivos**: _pódeselle quitar_ ao proceso que o posúe sen efectos daniños (ex: memoria)
+ **Non apropiativos**: _non se lle pode quitar_ ao propietario actual sen efectos daniños (ex: CD-ROM)
\*Os máis problemáticos en termos de interbloqueos son os non apropiativos, xa que os conflictos dos apropiativos se poden resolver mediante a asignación dos recursos dun proceso a outro.

**Secuencia de accións** para solicitar un recurso:
1. _Solicitar_ o recurso $\rightarrow$ 2. _Empregar_ o recurso $\rightarrow$ 3. _Liberar_ o recurso
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

## Interbloqueos de recursos
Un interbloqueo de recursos ocorre cando **cada proceso espera pola liberación de algún recurso** que está sendo _empregado por outro proceso do conxunto_, polo que ningún dos procesos se pode executar, ningún pode liberar recursos e ningún pode ser despertado. 
### Condicións para os interbloqueos de recursos
Para que ocorra un interbloqueo deben aplicarse todas as condicións seguintes:
1. **Condición de exclusión mutua** $\rightarrow$ cada proceso _se asigna a un só proceso ou está dispoñible_.
2. **Condición de contención e espera** $\rightarrow$ os procesos que conteñen recursos outorgados previamente, poden _solicitar novos recursos_.
3. **Condición non apropiativa** $\rightarrow$ _os recursos outorgados_ previamente _non se lle poden quitar a un proceso pola forza_, senon que _deben ser liberados_ de explicitamente polo proceso que os contén.
4. **Condición de espera circular** $\rightarrow$ debe haber unha _cadea circular_ de dous ou máis _procesos_, cada un _esperando por un recurso contido polo seguinte_ proceso da cadea.
 
### Modelado de interbloqueos
Para **representar interbloqueos** gráficamente empréganse **grafos dirixidos** da seguinte forma:
+ Os **nodos** poden ser de dous tipos: _procesos_ (circulos) ou _recursos_ (cadrados)
+ Os **arcos** en dirección _recurso$\rightarrow$proceso_, significa que o recurso está _asignado_ ao proceso; mentres que os arcos en dirección _proceso$\rightarrow$recurso_, significa que o proceso está _bloqueado_ á espera do recurso.

![[exemploGrafoRecursos.png| center | 400]]

O sistema operativo _non ten que executar os procesos nunha orde especial_, polo que se ao aoutorgar unha petición específica, se pode producir un interbloqueo, o SO _pode suspender o proceso_ sen outorgar a solicitude ata que sexa seguro. 
![[exemploInterbloqueo.png| center | ]]
### Estratexias para lidiar cos interbloqueos
Hai catro estratexias:
+ Ignorar o problema
+ Detección e recuperación
+ Evitalos de forma dinámica
+ Prevención
#### Ignorar o problema: O algoritmo da avestruz
Simplemente _ignórase o problema_: "Tal vez si usted lo ignora, él lo ignorará a usted".
É o máis habitual (Windows, Linux...)
#### Detección e recuperación
**En vez de intentar evitar os interbloqueos, o sistema intenta detectalos para recuperarse despois**.
##### Detección de interbloqueos cun recurso de cada tipo
É o caso máis simple: **se o grafo de recursos ten algún ciclo, entón existe un interbloqueo**, e se non exisgten ciclos, entón o sistema non está en interbloqueo.
> [!Exemplo]
> Sistema con sete procesos (A-G) e seis recursos (R-W):
> 1. O proceso A contén a R e quere a S.  
> 2. O proceso B non contén ningún recurso pero quere a T.  
> 3. O proceso C non contén ningún recurso pero quere a S.  
> 4. O proceso D contén a U e quere a S e a T.  
> 5. O proceso E contén a T e quere a V.  
> 6. O proceso F contén a W e quere a S.  
> 7. O proceso G contén a V e quere a U.  
> Para _saber se o sistema está en interbloqueo_ e, en caso afirmativo, _qué procesos están involucrados_, podemos construir o **grafo de recursos**:
> ![[exemploGrafoInterbloqueo.png| center | 250]]

Para _formalizar a detección de interbloqueos_ empregamos **algoritmos para detectar ciclos en digrafos**:
1. Para cada nodo N do grafo, realizar os seguintes pasos:
2. Inicializar L como unha lista baleira, e marcar tódolos arcos como desmarcados.
##### Detección de interbloqueos con varios recursos de cada tipo
Para detectar interbloqueos entre $n$ procesos, de $P_{1}$ a $P_{n}$, con $m$ tipos de recursos, tenendo $E_{i}$ recursos de tipo $i$ (sendo $i \leq m$), necesitamos un **algoritmo baseado en matrices**. 

Elementos:
+ $E$ $\rightarrow$ vector de _recursos existentes_ $\rightarrow$  número total de instancias de cada recurso
+ $A$ $\rightarrow$ vector de _recursos disponibles_ $\rightarrow$ numero de instancias de cada recurso sin asignar
+ C $\rightarrow$ matriz de _asignacións actuais_
+ R $\rightarrow$ matriz de _peticións_

Desta forma queda:
![[estruturasAlgoritmo DeteccionInterbloqueos.png | center | 450]]

**Pasos** do algoritmo simplificado:
1. Inicializánse tódolos _procesos como non marcados_ e se toma $A$ como o _vector de recursos dispoñibles_.
2. _Búscase un proceso $P_{i}$ sen marcar tal que para cada recurso $j$, $R[i][j] \leq A[j]$_
3. Se existe, asumimos que $P_i$ pode rematar, polo que _o marcamos e actualizamos_ $A = A +$ fila $i$ de $C$. 
4. Volvemos ao paso 2 ata que non atopemos ningún proceso que cumpra a condición. **Todos os procesos non marcados ao final están en interbloqueo**.
##### Recuperacón por medio de apropiación
Consiste en **quitarlle temporalmente un recurso ao seu propietario actual** e _outorgarllo a outro proceso_, para despois _devolverllo_ ao seu propietario orixinal, polo que po requerir unha intervención manual.
Soe ser dificil ou imposible recuperarse desta forma.
##### Recuperación a través do retroceso
Consiste en facer que os procesos realicen **puntos de comprobación** de forma periódica, é dicir, que vaian _escribindo o seu estado nun arquivo_ para _poder reinicialo máis tarde_. O punto de comprobación contén a _imaxe da memoria_ e o _estado do recurso_ (qué recursos están asignados ao proceso nun momento dado). Para que sexan máis efectivos, _os novos puntos de comprobación escríbense en novos arquivos_, de forma que se acumule unha **secuencia completa** a medida que o proceso se execute.
Cando se detecta un interbloqueo, é doado ver que recursos se necesitan, polo que **para recuperarse**, un proceso que posúe un recurso necesario só ten que _iniciarse nun dos seus puntos de comprobación anteriores_ e _o recurso se lle asigna a un dos procesos do interbloqueo_.
##### Recuperación mediante a eliminación de procesos
Consiste en **eliminar un ou máis procesos**: ou ben un dos _procesos do ciclo_, ou ben un que _non estea no ciclo_, para poder liberar os seus recursos (este elíxese con coidado). Sempre que sexa posible, é mellor eliminar un proceso que poida volver a ser executado dende o inicio sen efectos daniños.
#### Métodos para evitar interbloqueos
Na maioría dos sistemas, os recursos se solicitan dun en un, polo que o sistema debe ser capaz de decidir se é seguro outorgar un recurso, e realizar a asignación só en caso afirmativo. 
###### ==Traxectorias dos recursos== ([[Exames#Ex4 exame maio 2022| ver exame 2022]])
Nos diagramas de traxectorias dos recursos, _cada punto no diagrama representa un estado_ conxunto dos dous procesos, e, cun só procesador, as rutas eben ser verticais ou horizontais, nunca diagonais.
> [!Exemplo]
> Neste diagrama móstranse as traxectorias de dous procesos competindo por dous recursos.
![[traxectoriasRecursos.png | center | 500]]
> + Punto p $\rightarrow$ ningún dos procesos executou instruccións.
> + Optamos por executar primeiro o proceso A (eixo das x) $\rightarrow$ punto q
> + Optamos por executar o proceso B $\rightarrow$ punto r (camiño vertical)
> + A solicita a impresora (cruza $I_1$) e se lle concede $\rightarrow$ traxectoria r-s
> + B solicita o trazador (en $I_5$) $\rightarrow$ punto t
>
A rexión azul representa a zona na que ambos procesos teñen a impresora, e a sexión rosa, cando ambos teñen o trazador, polo que ambas zonas son imposibles debido á exclusión mutua. Se o sistema entra na zona delimitada polo cadrado verde, entrará en interbloqueo cando chegue á intersección entre $I_2$ e $I_6$, xa que tanto A como B solicitan recursos que xa están asignados, o que fai que o cadrado verde sexa inseguro e non se deba entrar en el. Polo tanto, cando o sistema chega ao punto t, o único seguro é executar A ata que chegue a $I_4$ e libere ambos recursos.

###### Estados seguros e inseguros
Os principais algoritmos pra evitar interbloqueos baséanse no concepto de **estados seguros**, é decir, estados nos que _hai certa orde_ de programación na que _se pode executar cada proceso ata completalo_, aínda que todos solicitaran de maneira repentina todos os seus recursos de inmediato.
> [!Exemplo]
> Na seguinte imaxe, o estado (a) é seguro xa que existe unha secuencia de asignacións que permite completar tódolos procesos, é decir, o sistema pode evitar un interbloqueo mediante unha programación cuidadosa: executamos B $\rightarrow$ executamos C
> ![[estadoSeguroExemplo1.png| center | 500]]
> Se decidisemos cederlle unha unidade de recurso a A, obteríamos o estado (b) da figura seguinte:
> ![[exemploEstadosSeguros2.png | center | 500]]
> Nese estado, B poderíase completar, pero despois , dado que só quedarían libres 4 recursos e dado que tanto A e C necesitan 5, non hai secuencia que garantice que os procesos se completarán, polo que o sistema non tería que haber outorgado a primeira petición de A.

**Observación**: un estado inseguro non é un estado en interbloqueo per se, senon qeu simplemente un estado inseguro non _garantiza_ que todos os procesos terminen.
##### ==Algoritmo do banqueiro para un só recurso== ([[Exames#Ex4 exame maio 2022| ver exame 2022]])
É un algoritmo proposto por Dijkstra en 1965 que pode evitar interbloqueos. Búscanse **procesos que poidan ser satisfeitos**. e se determina se os _recursos liberados_ cando remate poden _satisfacer a outro de maneira recursiva_. 
> [!Exemplo]
> Neste exemplo, tanto o (a) como o (b) son estados seguros, mentres que o (c) é inseguro:
> ![[exemploBanqueroUnRecurso.png| center | 400]]
> (b) é un estado seguro porque o banquero pode outorgarlle recursos aos procesos, por exemplo, na seguinte orde: C $\rightarrow$ B $\rightarrow$ A $\rightarrow$ D
> (c) é un estado inseguro porque só queda un recurso libre, e todos os procesos precisan máis dun recurso para executarse, polo que se todos os procesos solicitasen o seu número máxim de recursos o banquero non podería satisfacer a ningún deles e se produciría un interbloqueo.

Así, o algoritmo do banqueiro **considera cada petición a medida que vai ocorrendo, e analiza se outorgala produce un estado seguro**: en caso afirmativo outorga a petición, e senón a pospón. Para **saber se un estado é seguro**, o banqueiro comproba se ten os _recursos suficientes_ para satisfacer a algun dos seus clientes: en caso afirmativo, _vai facendo a simulación de outorgar as peticións_ a cada un dos seus clientes. Se todos os préstamos son satisfacrtorios, entón a petición inicial conduce a un estado seguro e se pode outorgar.
##### ==Algoritmo do banqueiro para varios recursos==
Para aplicar o algoritmo do banqueiro para varios recursos, necesitamos unha matriz de **recursos asignados**, outra de **recursos necesitados**, e tres vectores de **recuros existentes** (E), de **recursos posuídos** (P) e de **recursos dispoñibles** (A).
![[algoritmoBanqueiroVariosRecursos.png| center | 400]]
É evidente que $A_{i}=E_{i}-P_{i}$, é decir, que os recursos dispoñibles son os existentes menos os posuídos.

**Algoritmo para compobar se un estado é seguro**:
1. Buscar unha _fila R_ (un proceso) cuxos _recursos que aínda se necesitan_ sexan _menores ou iguais que os recursos dispoñibles (A)_. Se non existe, entón o sistema entrará en interbloqueo en algún momento.
2. Supoñer que _o proceso da filla R obtén tódolos recursos_ que necesita e remata. Marcamos ese _proceso como rematado_ e _sumámoslle os seus recursos ao vector de recursos dispoñibles A_.
3. _Repetimos os pasos 1 e 2_ ata que todos os procesos estean marcados como _terminados_ ou ata que haxa un _interbloqueo_.

#### Prevención de interbloqueos
Para previr interbloqueos temos que asegurar que, polo menos, **nunca se cumpra unha das condicións** para estes ([[SOII/Tema2#Condicións para os interbloqueos de recursos| ver arriba]]).
##### Atacar a condición de exclusión mutua
Se **ningún recurso se asignara de maneira exclusiva a un só proceso**, nunca teríamos interbloqueos; pero esto non é posible ca maioría de recursos xa que sería un caos. Por exemplo, no caso dunha **impresora**, sería inviable permitir que tódoos procesos escribisen nela á vez, polo que neste modelo, o _único proceso que solicita realmente a impresora física_ é o **demonio de impresión**; e, como este nunca solicita ningún outro recurso, o _interbloqueo para a impresora queda eliminado_. Unha idea que se aplica con frecuencia é a de **evitar asignar un recurso cando non sexa estrictamente necesario**, e tratar de **asegurar que a menor cantidade posible de procesos reclamen ese recurso**.
##### Atacar a condición de contención e espera
Trátase de **evitar que os procesos que conteñen recursos esperen por máis recursos**, polo que se debería requerir que tódolos procesos _soliciten todos os seus recursos antes de empezar a execución_. Non obstante, o **problema** deste método é que moitos procesos _non saben cantos recursos necesitarán ata que empezen a executarse_ (se o souberan, poderíase empregar o algoritmo do banqueiro) . Ademáis, así os recursos _non se empregarían de maneira óptima_.
Unha forma distinta de atacar a condición de contención e espera é _requerir que un proceso que solicita un recurso libere termporalmete os recursos que contén_ nun momento dado, para despois tratar de obter todo o que necesite á vez.
##### Atacar a condición non apropiativa
Trátase de **virtualizar os recursos** de forma que _só un proceso teña acceso ao recurso real_, pero todos teñan acceso ao recurso "virtual". Non obstante, **non todos os recursos se poden virtualizar** así.
##### Atacar a condición de espera circular
Hai varias formas de atacar esta condición:
+ Ter unha regra que diga que **un proceso ten dereito a un só recurso en calquera momento**, polo que _se necesita outro recurso, debe liberar o anterior_.
+ Proporcionar unha **numeración global de tódos os recursos**, de forma que os procesos poidan _solicitar recursos_ sempre que queiran, pero realizando as peticións _en orde numérica_. Así, o grafo de asignación de recursos nunca terá ciclos (nunca se producirá un interbloqueo). ![[ordenacionRecursos.png| center | 300]]
## Outras cuestións
### Bloqueo de dúas fases
En **sistemas de Bases de Datos**, o bloqueo de dúas fases (visto en BDII) consiste en:
1. _Primeira fase_: o proceso trata de bloquear todos os rexistros que necesita.
2. _Segunda fase_: realiza as súas actualizacións e vai liberando bloqueos
### Interbloqueos de comunicacións
É un tipo de interbloqueo que pode ocorrer nos **sistemas de comunicacións**, onde os procesos se comunican entre sí mediante o _envío de mensaxes_. Para romper estes interbloqueos introdúcense os _tempos de espera_ (timeout) e os **protocolos** (mais info nos apuntamentos de Redes).
![[interbloqueoRedes.png|  center | 300]]
### Bloqueo activo
O **bloqueo activo** (**livelock**) ocorre cando _ningún dos procesos se bloquea, pero tampouco progresa_, polo que é funcionalmente equivalente a un interbloqueo.
### Inanición
Ocorre cando **certos procesos nunca reciben atención aínda que non estean nun interbloqueo**. Pódese _evitar_ mediante unha _política de asignación de recursos tipo FIFO_.


















