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
Tamén temos explotación do **paralelismo a nivel de instrucción (ILP)**: varias instruccións executándose á vez en distintos cores (chegou á máxima explotabilidade).
\*_Arquitectura vectorial_: as instruccións vectoriais son as que se executan nas GPUs (operan con vectores, é decir, unha única instrucción operando moitos datos á vez: simple instrucción, múltiple dato (SIMD))

## Clasificación das arquitecturas paralelas
### Taxonomía de Flynn
Clasifica as arquitecturas paralelas baseándose en **streams de datos e instruccións**:
+ **Instrucción simple, fluxo de datos simple (SISD)**:
	+ Computadores secuenciais mononúcleos (como o pentium 4)
+ **Instrucción simple, fluxo de datos múltiple (SIMD)**:
	+ Arquitecturas vectoriais: GPUs
+ **Múltiples instruccións, fluxo de datos simple (MISD)**:
+ **Múltiples instruccións, fluxo de datos múltiples (MIMD)**:
	+ Cada procesador ten as súas propias instruccións e opera sobre os seus datos: Clústers
\* Moitos computadores son _híbridos de diferenctes tipos_

### Arquitecturas MIMD/multiprocesadores
+ **Procesadores de memoria compartida (UMA = Unifrom Acces Memory)**:
	+ Todos os procesadores comparten un _único espazo de direccións_ ("memoria compartida")
	+ O programador _non necsita coñecer a ubicación dos datos_
+ **Multiprocesadores de memoria distribuída (NUMA = Non-Uniform Memory Access)**: 
	+ _Cada procesador ten a súa propia memoria RAM independente_ (o seu propio espazo de direccións), e estas memorias RAM están _interconectadas_, polo que _o tempo de acceso a un dato depende da súa localización (onde se atopa)_ (dende o procesador P0 podo acceder á memoria do procesador P2, pero tardarei máis). \*Por esto denomínase Non-Uniform Memory Access (NUMA)
	+ O programador _necesita coñecer onde están os datos_ ![[MultiprocesadoresNUMA.png|center| 230]]
	+ **Multicores**: 
		+ _Todos os cores comparten a memoria_ (o espazo de direccións) (UMA)
		+ Teñen _máis dun procesador por chip_
		+ Requiren _programación paralela explícita_ para aproveitalos
		+ É _complicado_ conseguir un bo rendemento, dividir o traballo entre os núcleos e optimizar a comunicación entre eles

**GRÁFICA**: rendemento dos procesadores ó longo do tempo ![[VarRendProcesadores.png | center | 650]]Destacar que no 2004 aparecen as computadoras multinúcleo, e a partires de ahí baixa considerablemente a porcentaxe de evolución debido ao esgotamento das melloras.
Como va¡emos, chegamos a un teito tecnolóxico no que respecta ao rendemento das CPUs, xa que os procesadores teñen unha _baixa escalabiliadde_ (ao engadirlle novos recursos, o seu rendemento non mellora de maneira proporcional)
>[! Caso] Caso do Pentium4:
Foi un dos últimos procesadores creados antes da aparición dos multicore. Tiña _hiperthreading_: cun único núcleo, comportábase como se tivese 2 (ía cambiando dunha tarefa á outra todo o rato ("pseudoparalelismo"))

## Concepto de segmentación (pipeline)
As **unidades de retardo** mídense en **F04**, que é o que tarda unha sinal en _atravesar un inversor que ten 4 inversores de saída_ (fan out de 4). _Depende do nodo tecnolóxico_ (tecnoloxía de fabricación).

A **segmentación** consiste en _dividir a execución dunha instrucción_ en [[AnexosFuComp#1. Execución de instruccións|varias etapas]] e ir gardando os seus datos en _rexistros_ para evitar interferencias entre as instruccións (que se mezclen os datos). Así, en vez de acabar de executar unha instrucción completa antes de comezar a procesar a seguinte, cando unha instrucción remata unha fase, a seguinte xa pode comezala; é decir _as instruccións síguense executando secuencialmente, pero mentres está executándose unha, xa se pode comezar a executar outra_.
\*("Un procesador executa varias instruccións á vez por cachiños")
**Profundidade de segmentación**: cantidade de _etapas_ en que se divide o procesamento dunha _instrucción_

![[ConceptoSegmentacion.jpg| center | 400]]

Coa segmentación, **redúcese o período** (o tempo necesrio para executar unha instrucción), polo que _aumenta a frecuencia_ ($f=1/T$). Non obstante, _aumenta o retardo total_ dunha instrucción individual, xa que cada etapa do pipeline introduce un pequeno retardo (rexistros).

Poden ocorrer **paradas de emisión** (pipeline stalls), que son situacións nas que _non se pode executar a seguinte instrucción_ nun tempo de reloxo (o procesador non pode emitir unha nova instrucción ao pipeline). Estas poden vir dadas como _consecuencia dos saltos_ (o procesador non sabe se saltar ou non ata que se resolva outra instrucción\*); para solucionalo pódese empregar a _predicción de saltos_ (algoritmos que intentan adiviñar o resultado dun salto).
\*Exemplo:
```Assembly
beq $t0, $t1, etiqueta  # Se $t0 == $t1, saltamos a "etiqueta"
add $t2, $t3, $t4  # Inda non sabe se ten que saltar a "etiqueta"
```

Tamén poden ocorrer **burbullas no pipeline** (pipeline bubbles) cando _unha instrucción xa está no pipeline pero debe esperar_ para avanzar á seguinte etapa debido a algún conflicto.

## Arquitectura dun núcleo típico
#### Niveis de caché
Ten **varios niveis de caché**:
1. **Caché L1**: é a máis rápida e pequena. Divídese en:
	+ _L1-I_ (Instruccións)
	+ _L1-D_ (Datos): permite 2 lecturas de 1 escritura por ciclo,  o que mellora o rendemento (permite accesos simultáneos)
2. **Caché L2**: mais grande, maior latencia (12 ciclos). Está _unificada_ (almacena datos e instruccións)
3. **Caché L3**: máis grande e lenta (25-35 ciclos). Tamén _unificada_.
#### Fluxo dunha instrucción a través do núcleo:
1. **Búscase** a instrucción na L1 (IF) e se **almacena** nunha cola para ser decodificada (_Instruction Queue_).
2. **Decodifícase** a instrucción (ID) en microoperacións (µOPs).
3. A instrucción pasa polos _buffers de reordenación_ (intervén o predictor de saltos (_Branch Predictor_)).
4. O _Scheduler_ asigna as instruccións a distintas unidades de execución (_portos_) según a súa dispoñibilidade (os portos 0, 1 e 5, e 2, 3 e 4 permiten a execución en paralelo dalgunhas operacións).
5. Se a instrucción necesita datos da **memoria**, búscaos na xerarquía de memoria.
6. Os _resultados gárdanse_ nos rexistros ou na memoria (WB).
Este fluxo permite que múltiples instruccións independentes se executen simultáneamente na orde que máis conveña (para evitar bloqueos), xa que despois o _buffer de reordenación_ encárgase de devolver os resultados na orde correcta.
![[ArquitecturaNucleoTipico.png | center | 500]]


## Arquitectura dun procesador multinúcleo
Cada núcleo funciona como un _procesador independente_, pero teñen a **L3 compartida** a través dun **anel de comunicación**, polo que os núcleos poden ter datos comúns. 
![[ArquitecturaProcesadorMultinucleo.png | center | 200]]
Os cores, a memoria caché e o procesador gráfico funcionan a potencias e frecuencias diferentes, debido a que, para _aforrar enerxía_, se unha compoñente pode funcionar a unha frecuencia menor, dáselle esa frecuencia (**dominios de frecuencia separados**).
Ademáis tamén existe unha unidade de control encargada de **distribuír o consumo de enerxía** entre os distintos compoñentes, o que mellora o rendemento e a eficiencia.
\*A frecuencia por si mesma, non decide se un ordenador é máis rápido que outro

---
# Diapo 18

Tecnoloxía de fabricación:
Densidade de capas non uniforme ao longo do circuito

Densidade de transistores:
Según a lei de Moore, cada dous anos pódese meter o dobre de transistores no mesmo espazo. 

Hammering: acceder todo o rato á mesma zona de memoria --> correntes de difusión --> os e- pasan á celda contigua por efecto tunel. Sol: separar os transistores

Capacitancia: cap. de E quepode chegar a transmitir un cable

Power gating: facer dominios de potencia independentes e incluso desconectar da alimentación partes que non se empreguen
Domain-specific: deseños optimizados en consumo de potencia para unha determinada operación que se repite moito

