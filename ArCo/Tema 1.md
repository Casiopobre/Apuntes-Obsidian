# Tema 1. Fundamentos do deseño de computadores
## Parámetros de deseño dun sistema
+ **Rendemento**
+ **Custo**
+ **Potencia**:
	+ _estática_: por estar encendido (parásita)
	+ _dinámica_: en funcionamento
	+ _Potencia pico_
	+ _Potencia media_
+ **Robustez**:
	+ _Tolerancia ao ruido_
	+ _Resistencia á radiación_ (pode alterar os bits)
+ **Testabilidade**: que sexan facilmente testables
+ **Reconfigurabilidade**: que se poda reconfigurar facilmente en caso de que se estropee
+ Tempo de saída ao mercado, etc.

## Clases de computadores
+ **PMD (Personal Mobile Device)**: o máis importante é que respondan en tempo real e eficiencia enerxética
	+ Ex: teléfonos móviles, tablets...
+ **Computación de escritorio**
+ **Servidores**: conecta varios usuarios simultáneamente, polo que o máis importante é a _dispoñibilidade_ (que se poidan conectar varios usuarios simultáneos), a _escalabilidade_ (que funcione igual de ben con 3 usuarios que con 30) e _productividade_
+ **Clústers**: subclase = _supercomputadores_ (teñen moi bo rendemento en operacións en punto flotante ou en dobre precisión)
+ **Computadores embebidos**: os que hai nos coches, na roupa, para controlar a presión arterial (énfasis en que sexan baratos)

## Arquitectura dun computador
\*ISA: arquitectura do conxunto de instruccións
Realmente, _no diseño dun computador se teñen en conta máis cousas ademáis da ISA_ (o espazo, o consumo, o rendemento...)

## Tipos de pararelismo
O paralelismo pódese explotar a diferentes niveis. Por exemplo: _OpenMP_, que permite explotar o sistema multicore (lanzar unha tarefa a cada un dos procesadores) --> **Paralelismo a nivel de thread (TLP)**
Tamén temos explotación do **paralelismo a nivel de instrucción (ILP)**: varias instrucción executándose á vez en distintos cores (chegou á máxima explotabilidade).
\*_Arquitectura vectorial_: as instruccións vectoriais son as que se executan nas GPUs (operan con vectores, é decir, unha única instrucción operando moitos datos á vez: simple instrucción, múltiple dato (SIMD))

## Clasificación das arquitecturas paralelas
### Taxonomía de Flynn
Clasifica as arquitecturas paralelas baseándose en **streams de datos e instruccións**:
+ **Instrucción simple, fluxo de datos simple (SISD)**:
	+ Computadores secuenciais (como o pentium 4)
+ **Instrucción simple, fluxo de datos múltiple (SIMD)**:
	+ Arquitecturas vectoriais: GPUs
+ **Múltiples instruccións, fluxo de datos simple (MISD)**:
+ **Múltiples instruccións, fluxo de datos múltiples (MIMD)**:
	+ Clústers
\* Moitos computadores son _híbridos de diferenctes tipos_

### Arquitecturas MIMD/multiprocesadores
+ **Procesadores de memoria compartida (UMA = Unifrom Acces Memory)**:
	+ Todos os procesadores comparten un _único espazo de direccións_ ("memoria compartida")
	+ O programador _non necsita coñecer a ubicación dos datos_
+ **Multiprocesadores de memoria distribuída (NUMA = Non-Uniform Memory Access)**: 
	+ _Cada procesador ten a súa propia memoria RAM independente_ (o seu propio espazo de direccións), e estas memorias RAM están _interconectadas_, polo que _o tempo de acceso a un dato depende da súa localización (onde se atopa)_ (dende o procesador P0 podo acceder á memoria do procesador P2, pero tardarei máis). \*Por esto denomínase Non-Uniform Memory Access (NUMA) ![[MultiprocesadoresNUMA.png|center| 230]]
	+ **Multicores**: 
		+ _Todos os cores comparten a memoria_ (o espazo de direccións) (UMA)
		+ Teñen _máis dun procesador por chip_
		+ Requiren _programación paralela explícita_ para aproveitalos
		+ É complicado conseguir un bo rendemento, dividir o traballo entre os núcleos e optimizar a comunicación entre eles
\* Chegamos a un teito tecnolóxico no que respecta ao rendemento das CPUs
>[! Caso] Caso do Pentium4:
Tiña _hiperthreading_: cun único núcleo, comportábase como se tivese 2 (ía cambiando dunha tarefa á outra todo o rato ("pseudoparalelismo"))

## Concepto de segmentación (pipeline)
As **unidades de retardo** mídense en **F04**: é o que tarda unha sinal en _atravesar un inversor que ten 4 inversores de saída_ (fan out de 4). _Depende do nodo tecnolóxico_ (tecnoloxía de fabricación).

A **segmentación** consiste en dividir as "fases" dunha instrucción e ir gardando os seus datos en rexitros, de forma que a medida que se executan instruccións, xa se poden ir executando as seguintes (e, grazas aos rexistros, non se mezclan os datos). "Un procesador executa varias instruccións á vez por cachiños".
As instruccións síguense executando secuencialmente, pero mentres está executándose unha, xa se pode comezar a executar outra.
![[ConceptoSegmentacion.png| center | 300]]
**Profundidade de segmentación**: 
Redúcese o período (o tempo necesrio para executar unha instrucción), polo que aumenta a frecuencia 
($f=1/T$)
Poden ocorrer paradas de emisión: momentos nos que non se pode executar a seguinte instrucción nun tempo de reloxo
Provócanse problemas como consecuencias dos saltos: predicción de saltos
+++

## Arquitectura dun microprocesador multinúcleo
As cachés soen ter varias irregularidades de fabricación
Os cores, a memoria caché e o procesador gráfico funcionan a potencias e frecuencias diferentes, debido a que para aforrar enerxía, se unha compoñente pode funcionar a unha frecuencia menor, dáselle esa frecuencia (dominios de frecuencia separados).
A frecuencia por si mesma, non decide se un ordenador é máis rápido que outro

Tecnoloxía de fabricación:
Densidade de capas non uniforme ao longo do circuito

Densidade de transistores:
Según a lei de Moore, cada dous anos pódese meter o dobre de transistores no mesmo espazo. 

Hammering: acceder todo o rato á mesma zona de memoria --> correntes de difusión --> os e- pasan á celda contigua por efecto tunel. Sol: separar os transistores

Capacitancia: cap. de E quepode chegar a transmitir un cable

Power gating: facer dominios de potencia independentes e incluso desconectar da alimentación partes que non se empreguen
Domain-specific: deseños optimizados en consumo de potencia para unha determinada operación que se repite moito

