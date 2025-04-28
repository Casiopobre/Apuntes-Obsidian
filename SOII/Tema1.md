# Tema 1. Comunicación e sincronización entre procesos e fíos
## Comunicación entre procesos (IPC)
Realmente, hai **comunicación implícita** entre procesos xa que todos _comparten o kernel_ (rexión de memoria).
A comunicación entre procesos **emprégase para**:
+ _Pasar información_ entre procesos.
+ _Evitar interferencias_ (carreiras críticas entre procesos).
+ Garantir unha _orde correcta_ (sincronización mediante dependencias).
Os dous últimos puntos pódense tratar coas mesmas ferramentas tanto para procesos como para fíos; pero o primeiro punto é moito máis sinxelo para _fíos_, xa que estes _comparten espazo de direccións_ (os procesos non comparten memoria excepto no caso dun espazo virtual ou mediante o envío de mensaxes).
>[! Exemplo] Exemplo: Pipe
Ao executar "comando1 | comando2", o pipe colle a saída do primeiro proceso e a garda nun ficheiro temporal; e comeza a execución do segundo proceso, que tomará o contido do ficheiro temporal (saida de comando1) como entrada. A este ficheiro só pode acceder un proceso á vez, polo que para os sistemas de **multiprogramación**, existe a opción de marcar **puntos de referencia** para que se intercalen e o lean pseudoparalelamente.

**Principal inconvinte**: os sistemas son _multiprogramados_, polo que hai varios fíos e procesos simultáneos, polo que poden interferir.
### Condicións de carreira
Os procesos que traballan en conxunto poden **compartir un espazo de almacenamento** no que poden ler e escribir datos, e que pode estar na _memoria principal_ ou pode ser un _arquivo compartido_. Como consecuencia desto, pódense producir **condicións de carrera**, que son situacións nas que dous ou máis procesos _acceden simultáneamente a datos compartidos_ sen nungún orde específico, o que pode xerar resultados inesperados. Esto fai que _depurar_ programas con condicións de carrera sexa _complexo_.
> [!Exemplo: Spooler de Impresión]
> Cando un proceso quere imprimir un arquivo, introduce o seu nome nun **directorio de spooler**. Entón, o **demonio de impresión** (outro proceso) comproba periódicamente se  hai arquivos que imprimir, e se os hai, os imprime e elimina os seus nomes do directorio.
> Imaxina que temos un directorio spooler con _moitas ranuras_ que poden conter o nome dun arquivo, e que hai dúas _variables compartidas_: `sal`, que apunta ao _seguinte arquivo_ a imprimir, y `ent`, que apunta á _seguinte ranura libre_.
> Situación: Ranuras 0-3 baleiras e ranuras 4-6 cheas. Pseudosimultáneamente, os procesos A e B queren poñer un arqivo na cola de impresión. 
> ![[exemploSpooler.png| center | 250]]
> No **peor dos casos** pasaría o seguinte:
> 1. _Proceso A_ lee `ent` = 7 $\rightarrow$ garda 7 na súa variable local `seg_ranura_libre`
> 2. Ocorre unha interrupción de reloxo e o planificador lle da a _CPU ao  proceso B_
> 3. _Proceso B_ lee `ent` = 7 $\rightarrow$ garda 7 na súa variable local `seg_ranura_libre`
>~ Ambos procesos pensan que a seguinte ranura libre é a 7. ~
> 4. O _proceso B_ continúa ca súa execución $\rightarrow$ garda o nome do seu arquivo na ranura 7 e actualiza `ent++`
> 5. O _proceso A_ execútase de novo dende onde quedou $\rightarrow$ ve que a súa variable local dille que a seguinte ranura libre é a 7, polo que escribe o nome do seu arquivo na ranura 7, _sobreescribindo_ o que escribira antes o proceso B; e actualiza `ent++`.
> ~ **Resultado**: o nome do arquivo que escribiu o proceso B perdeuse e, polo tanto, nunca se imprimirá. ~

Para tratar problemas de carreiras críticas hai que ter sempre presente a **Lei de Murphy**, que di que _se algo pode saír mal, entón sairá mal_.

> [!Outro exemplo de condición de carreira]
> (Este é das diapos de ffrivera, non aparece no Tanembaum)
> Código con condicións de carrera:
> ```c
> int suma = 0;
> void suma(int ni, int nf){
> 	int j;
> 	for (j = n1; j <= nf; j++)
> 		suma += j;
> }
> ```
> Código correxido para evitar condicións de carreira:
> ```c
> int suma = 0;
> void suma(int ni, int nf){
> 	int j, suma_parcial = 0;
> 	for (j = ni; j <= nf; j++)
> 		suma_parcial += j;
> 	suma += suma_parcial;
> }
> ```
> No segundo código, _redúcese a sección crítica_ xa que cada fío ou proceso ten a súa variable local `suma_parcial`, polo que a zona con rexión crítica (cando se modifica `suma`) só se executa unha vez por fío/proceso (en vez de executarse dentro do for), reducindo o risco de carreiras críticas.

### Rexións críticas
A **rexión crítica** é a parte do programa na que se _accede á memoria compartida_. 
Para **evitar as condicións de carreira**, necesitamos **exclusión mútua**, que asegura que, se un proceso está empregando una variable ou arquivo compartido, _os demáis procesos non poden acceder_ a esa variable ou arquivo.
**Condicións para unha boa solución**:
1. Non pode haber dous procesos na mesma rexión crítica simultáneamente
2. Non podemos facer suposicións sobre as velocidades ou o número de CPUs
3. Ningún proceso que se execute fora da sua rexion critica pode bloqeuar outros procesos
4. Ningún proceso ten que esperar de maneira indefinida para entrar á súa rexión crítica 
**Comportamento abstracto da exclusión mútua**:
![[CompAbstractoExMutua.png| center | 450]]

### Exclusión mútua con espera ocupada
A **espera ocupada** ou espera active consiste na acción de _avaliar de forma continua unha variable_ esperando certo valor e, polo xeral, debe evitarse debido a que desperdicia tempo de CPU.
Para lograr a exclusión mútua con espera ocupada hai varios métodos.
#### Deshabilitando interrupcións
Consiste en que cada proceso **deshabilite as interrupcións xusto antes de entrar á súa rexión crítica** e as rehabilite despois, evitando así que ocorran interrupcións de reloxo. Esto causa que _non se poida expropiar a un proceso_, xa que non hai cambios de contexto. Aínda que o _kernel o fai con frecuencia_, é _perigoso en procesos de usuario_, e resulta _inútil en sistemas multicore_.
> É unha técnica útil dentro do mesmo SO, pero non é apropiada como mecanismo de exclusión mútua xeral para os procesos de usuario.
#### Variables de candado
Consiste en ter unha **variable compartida** que ao principio vale 0 e que ten que ser **avaliada por cada proceso que queira entrar á rexión crítica**: se é 0, pona a 1 e entra, e se é 1, espera a que sexa 0.
Polo tanto:
+ Candado = 0 $\rightarrow$ ningún proceso na súa rexión crítica
+ Candado = 1 $\rightarrow$ algún proceso na súa rexión crítica
> Non funciona. \*_Candado de xiro_: candado que emprega a espera ocupada.
#### Alternancia estricta
Consiste en ter unha **variable entera `turno` que leva a conta de a qué proceso lle toca acceder á rexión crítica**. Cada proceso _avalía se `turno` vale un número concreto_ (0, 1, ...) e se é así, significa que pode entrar á rexión crítica. Despois, cando vai saír da rexión crítica, _actualiza turno_ para que outro proceso poida acceder a ela.
> [!Exemplo]
> Exemplo con 2 procesos:
> ```c
> while (TRUE) {                      while (TRUE) {   
> 	while (turno !=0) ;                 while (turno !=1) ;
> 	region critica();                   region critica();
> 	turno=1;                            turno=0; 
> 	region_no_critica();                region_no_critica();
> }                                   }
> ```

> Non é unha boa solución para procesos (é mellor para fíos) xa que un proceso pode quedar bloqueado por outro máis lento (os procesos sempre traballan ao ritmo do mais lento), reducindo a productividade do procesador. Ademáis, **incumple a condición 3**.

#### Solución de Peterson
Consiste en que, cada proceso, antes de entrar á rexión crítica, chama a unha función (`entrar_region`) que fai que **o proceso espere ata que sexa seguro entrar** (while do codigo); e cando quere saír da rexión crítica chama a `salir_region`, que actualiza o array `interesado`, permitindo así que _outro proceso poida accder_.

Solución de Peterson para 2 procesos:
```c
#define FALSE 0
#define TRUE 1
#define N 2 // Num. de procesos

int turno;
int interesado[N]; // Array de procesos esperando. Ao principio = {0}

void entrar_region(int proceso) {
	int otro; // O numero do outro proceso
	otro = 1 – proceso;
	interesado[proceso] = TRUE; // Indicams que está ineresado
	turno = proceso; // Damoslle o turno
	while (turno == proceso && interesado[otro] == TRUE); // Espera para entrar
}

void salir_region(int proceso) {
	interesado[proceso] = FALSE;
}
```
> [!Exemplo de execución]
> 1. Proceso 0 chama a `entrar_region` $\rightarrow$ `interesado[otro] = FALSE` $\rightarrow$ returnea inmediatamente (non se cumple o `&&` do while)
> 2. Proceso 1 chama a `entrar_region` $\rightarrow$ `interesado[otro] = TRUE` $\rightarrow$ quédase esperando no while ata que o outro proceso execute `salir_region` e poña o seu `interesado = FALSE`

> **¿Espera activa?** Sí debido a que o while compróbase continuamente polo proceso que estea esperando

#### Instrucción TSL (Hardware)
É unha **instrucción atómica** que require **axuda do hardware** xa que **bloquea o bus de memoria** mentres se executa. A instrucción é da forma: `TSL REXISTRO, CANDADO`
**Que fai?** Le o contido de `CANDADO` e o garda no rexistro `REXISTRO`, para despois almacenar un valor (distinto de 0) en `CANDADO`. Esto faino de forma **átomica** e indivisible gracias a que a CPU bloqeuea o bus de memoria mentres tanto.
> _Bloquear o bus de memoria != Deshabilitar as interrupcións_
> Cando se deshabilitan as interrupcións non se evita que outro procesador acceda á palabra, xa que deshabilitar as interrupcións no procesador 1 non ten ningún efecto no procesador 2. Para que o procesador 2 non interfira a ñunica forma é bloquear o bus (require de hardware).

Para **empregar TSL** necesitamos unha variable compartida `CANDADO` que coordine o acceso ás rexións críticas: cando `CANDADO == 0` $\rightarrow$ calquera proceso pode poñelo a 1 (chamada a TSL) e devolvelo a 0 cando remate (`MOVE` ordinario). 

**Para evitar que dous procesos entren ao mesm tempo nunha rexión crítica**:
```asm
entrar_region:
	TSL REGISTRO,CANDADO ;Copia o candado ao rexistro e pon candado a 1
	CMP REGISTRO,#0 ;Comproba se candado era 0
	JNE entrar_region ;Se non era 0, volve a intentalo
	RET ;Se era 0, volve ao procedemento chamador

salir_region: 
	MOVE CANDADO,#0 ;Pon o candado a 0
	RET ;Volve ao chamador
```

##### Instrucción alternativa: XCHG
A instrucción XCHG **intercambia o contido de dúas ubicacións de forma atómica**, e está imlementada nos procesadores Intel x86, que a empregan para a sincronización a baixo nivel.
Polo tanto, o codigo 1 e o codigo 2 son equivalentes (se o 2 se executara de forma atómica):
```asm
;Codigo 1
XCHG REG1, REG2

;Codigo 2
MOVE TMP, REG1   
MOVE REG1, REG2   
MOVE REG2, TMP
```

### Sleep and Wakeup
As _solucións anteriores_ comproban se un proceso que pide entrar a unha rexión crítica pode facelo, se non pode, quédase esperando. Esto pode ocasionar un **problema de inversión de prioridades**, que ocorre cando un proceso de baixa prioridade mantén un recurso (como un candado), e un proceso de alta prioridade que o necesita non pode continuar porque o proceso de baixa prioridade non pode executarse para liberarlo.

Para _evitar as esperas activas_ que poden dar lugar a problemas de inversión de prioridades, pódense empregar as **chamadas ao sistema `sleep`**, que fai que o sistema se bloquee ou desactive ata que outro programa a active, e **`wakeup`**, que desperta ao proceso pasado como parámetro. _Para asociar ambas chamadas, emprégase unha dirección de memoria_ (zona de memoria compartida).
#### O problema do productor-consumidor
[[SOII_Informe1.pdf| Mais info no Informe 1 da practica 2 de SOII]]
O **problema produtor-consumidor (ou buffer limitado)** consiste en **dous procesos** (produtor e consumidor) que comparten un **buffer de tamaño fixo**. Basicamente, o _produtor_ dedícase a _colocar elementos_ no buffer mentres o _consumidor os elimina_. Cando un dos dous _non pode realizar a súa función_ (colocar ou eliminar), _vai durmir_ (cun `sleep`) e agarda a que _o outro proceso o esperte_ (cun ​​`wakeup`). Para _levar a conta dos elementos_ do buffer, ambos procesos comparten a variable `conta`, que é actualizada polos procesos según van introducindo ou eliminando elementos do buffer.

O **problema** ocorre cando a variable `conta` _non se actualiza correctamente_, xa que, ao non ter acceso restrinxido, é susceptible a carreiras críticas, o que acaba provocando tanto o produtor como o consumidor se queden _durmidos para sempre_.

Problema do productor-consumidor cunha condición de carreira crítica:
```c
#define N 100 // Tamaño do buffer
int cuenta = 0; // Número de elementos que contén o buffer

void productor(void) {
    int elemento;
    while (TRUE) {
        elemento = producir_elemento();
        if (cuenta == N) sleep(); // O buffer está cheo
        insertar_elemento(elemento);
        cuenta = cuenta + 1;
        if (cuenta == 1) wakeup(consumidor); 
    }
}

void consumidor(void) {
    int elemento;
    while (TRUE) {
        if (cuenta == 0) sleep(); // O buffer está baleiro
        elemento = quitar_elemento();
        cuenta = cuenta - 1;
        if (cuenta == N - 1) wakeup(productor);
        consumir_elemento(elemento);
    }
}
```
\*Para solucionar o problema de perda de sinais, pódese engadir un bit de espera de espertar, que se fixe cando un proceso que esté esperto, reciba un bit de espera a espertar, aínda uqe é unha solución pouco escalable.
### Semáforos
Tal e como os propuxo Dijkstra, un **semáforo** é unha _variable enteira_ que conta o _número de sinais de espertar_ e as almacena para un uso futuro. Ríxese por dúas **operacións atómicas**:
+ **`up`**: _incrementa o valor_ do semáforo. Se hai algún _proceso inactivo_ nese semáforo, que non completara o seu `down`, o sistema selecciona algún proceso inactivo e _lle permite completalo_.
+ **`down`**: se o valor do semáforo é _maior a 0_, o _decrementa_, e se é _0_, _bloquea_ o proceso.
####  Resolución do problema do productor-consumidor con semáforos
[[SOII_Informe2.pdf| Mais info no Informe 2 da practica 2 de SOII]]
Os semáforos serven para resolver a perda de sinais de despertar. Normalmente, impleméntanse `up` e `down` como **chamadas ao sistema** que _deshabilitan brevemente tódalas interrupcións_ mentres avalía o semáforo, o actualiza e pon o proceso a durmir se fai falta.

Esta solución emprega **tres semáforos**: un para contabilizar o número de _ranuras cheas_, outro para as _baleiras_, e un chamado _mutex_ para que o productor e o consumidor non accedan ao buffer simultaneamente.  Así, aseguramos a **exclusión mútua** facendo que cada proceso execute un _down antes de entrar_ á rexión crítica, e un _up despois de salir_ da mesma.
\*A semáforos que se inicializan a 1 e se comportan como o mutex se lles chama semáforo binarios

Solución:
```c
#define N 100
typedef int semaforo;
semaforo mutex = 1;
semaforo vacias = N;
semaforo llenas = 0;

void productor(void) {
    int elemento;
    while (TRUE) {
        elemento = producir_elemento();
        down(&vacias);
        down(&mutex);
        insertar_elemento(elemento);
        up(&mutex);
        up(&llenas);
    }
}

void consumidor(void) {
    int elemento;
    while (TRUE) {
        down(&llenas);
        down(&mutex);
        elemento = quitar_elemento();
        up(&mutex);
        up(&vacias);
        consumir_elemento(elemento);
    }
}
```

### Mutexes
Básicamente son como unha _versión simplificada dos semáforos_ (sen a habilidade de contar), é decir, unha variable que toma valores binarios (0 ou 1). A os mutexes dáselles ben únicamente **administrar a exclusión mútua**, e son eficientes e sinxelos de implementar. Están rexidos por **dúas operacións atómicas**:
+ **`lock`**
+ **`unlock`**



