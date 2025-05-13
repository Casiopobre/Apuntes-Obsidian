# Tema 4. Sistemas operativos distribuídos
## Tipos de multiprocesadores
### Hardware de multiprocesadores UMA
Aínda que _todos os multiprocesadores teñen a propiedade de que cada CPU pode direccionar toda a memoria_, algúns teñen a característica de que **cada palabra de memoria se pode ler coa mesma velocidade que calquera outra palabra**. Estas máquinas coñécense como multiprocesadores **UMA** (_Uniform Memory Access_, Acceso uniforme á memoria).

#### UMAs con arquitectura baseada en bus
Os multiprocesadores máis simples baséanse **nun só bus**. Se está **inactivo**, a CPU _coloca a dirección da palabra que desexa no bus_, declara unhas cantas sinais de control _e espera_ ata que a memoria coloque a palabra desexada no bus. Se o bus está **ocupado** cando unha CPU desexa ler ou escribir na memoria, a CPU _agarda ata que o bus estea inactivo_.
![[umaBus.png| center | 400]]

**Problema:** con un _número elevado de CPUs_, a maioría delas estarán unha gran cantidade de tempo _inactivas_.

**Solución 1:** Engadir unha _caché a cada CPU_. Cando se fai referencia a unha palabra, obténse o seu bloque completo e gárdase na caché privada en modo lectura (permitindo que o mesmo bloque estea en varias cachés) ou lectura-escritura $\rightarrow$ _protocolo de coherencia_

**Solución 2:** Cada CPU ten _unha caché_ e unha _memoria privada local_ que utiliza mediante un _bus privado_. Deste xeito, _a memoria compartida úsase só para escribir variables compartidas_. O _compilador_ debe colocar todo o texto do programa, as cadeas, constantes e demais datos de só lectura, as pilas e as variables locais nas memorias privadas.

<div style="page-break-after: always;"></div>

#### UMAs basados en interruptores de barras cruzadas
O uso **dun único bus limita o número de CPUs** que se poden empregar, polo que se propoñen os **interruptores de barras cruzadas**, que son os circuítos máis simples para _conectar $n$ CPUs a $k$ memorias_. En _cada intersección_ dunha liña horizontal (entrante) e unha vertical (saínte) hai un **punto de cruce**, o cal é un interruptor que pode estar aberto ou pechado, dependendo de se as liñas se van conectar ou non.
![[umaInterruptores.png| center | 350]]
É unha **rede sen bloqueos** en moitos casos e **non precisa planificación**, aínda que se poden chegar a construír _redes moi grandes_.

#### UMAs que utilizan redes de conmutación multietapa
Partimos dun **conmutador simple con 2 entradas e 2 saídas**, recibindo como entrada mensaxes da forma que se ve no debuxo.
![[conmutadorUMA.png| center | 350]]
A partir deste tipo de conmutadores pódense crear _redes de conmutación multietapa_. Un exemplo é a **rede omega**.
![[redeOmega.png| center | 400]]
Se unha CPU quere acceder a unha _memoria coa que, en principio, non está conectada_, por exemplo a 011 coa memoria 111, compáranse bit a bit e, se son iguais, esa conexión será en paralelo e, se non, cruzada. Isto causa **contención**, xa que deberemos _sacrificar a conexión dalgúns módulos_.

### Hardware de multiprocesadores NUMA
O **espazo de memoria é compartido** entre todos os procesadores, polo que o **tempo de acceso á memoria non é uniforme**. Necesita **protocolos de coherencia**, que poden estan baseados en _directorio_.
![[NUMA.png| center | 400]]

## Tipos de sistemas operativos multiprocesador
Pasamos do hardware de multiprocesador ao **software**. Hai varias metodoloxías posibles.
### Cada CPU ten o seu propio sistema operativo
A primeira opción baséase en que cada CPU teña a súa propia **copia privada do sistema operativo**. Aínda que ao executar un programa o _código tamén sexa o mesmo para todas, cada unha ten datos distintos_, procesos e páxinas que non se comparten. Isto _inclúe tamén as táboas de procesos e páxinas_.

Unha **optimización** obvia é _permitir que todas as CPUs compartan o código do sistema operativo_ e obteñan copias privadas só das estruturas de datos.
![[SOpropio.png| center | 400]]

<div style="page-break-after: always;"></div>

### Multiprocesadores mestre-escravo
A segunda opción emprega a estratexia **mestre-escravo**. Todas as _chamadas ao sistema rediríxense á CPU 1_ para ser procesadas nela, onde hai unha _copia do sistema operativo e as súas táboas_. A CPU 1 tamén _pode executar procesos de usuario se ten tempo libre_. As demais encárganse de executar o código, pero _en ningún momento poden falar coa memoria xestionada pola CPU 1_.
![[MestreEsclavo.png| center | 400]]
**Problema:** o _rendemento é baixo_ porque todos os movementos deben pasar pola CPU mestre.

### Multiprocesadores simétricos (SMP)
A terceira opción son os **multiprocesadores simétricos** ou **SMP**. É un modelo moi versátil e amplamente utilizado.
![[SMP.png|center | 400]]
Para todas as CPUs hai _unha única copia do sistema operativo na memoria_. Como todas queren acceder a ela ao mesmo tempo, poden producirse condicións de carreira. Para evitalo, prográmase con **semáforos** ou **mutexes**, así poden _acceder ao núcleo_ de forma exclusiva (usando _locks_, por exemplo). Pola outra banda, hai _táboas únicas e compartidas_.