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
Realmente, no diseño dun computador se teñen en conta máis cousas ademáis da ISA (o espazo, o consumo, o rendemento...)

## Tipos de pararelismo
O paralelismo pódese explotar a diferentes niveis. Por exemplo: _OpenMP_, que permite explotar o sistema multicore (lanzar unha tarefa a cada un dos procesadores) --> **Paralelismo a nivel de thread**
Tamén temos explotación do **paralelismo a nivel de instrucción**: varias instrucción executándose á vez en distintos cores.
\*_Arquitectura vectorial_: as instruccións vectoriais son as que executan as GPUs (operan con vectores, é decir, unha única instrucción operando moitos datos á vez: simple instrucción, múltiple dato (SIMD))

## Clasificación das arquitecturas paralelas
### Taxonomía de Flynn
Baséase en streams de datos e instruccións:
+ Instrucción simple, fluxo de datos simple (SISD):
	+ Computadores secuenciais (como o pentium 4)
+ Instrucción simple, fluxo de datos múltiple (SIMD):
+ Múltiples instruccións, fluxo de datos simple (MISD):
+ Múltiples instruccións, fluxo de datos múltiples (MIMD):
	+ Clústers

### Arquitecturas MIMD
+ Procesadores de memoria compartida
+ Multiprocesadores de memoria distribuída: cada procesador ten a súa propia memoria RAM independente, e estas memorias RAM están interconectadas, polo que o tempo de acceso a un dato depende de onde estea (desde o procesador P0 podo acceder á memoria do procesador P2, pero tardarei máis). Por esto denomínase Non-Uniform Memory Access (NUMA)
	+ Multicores: todos os cores comparten a memoria (o espazo de direccións) (UMA)
		+ Teñen máis dun procesador por chip
		+ Requiren programación paralela explícita para aproveitalos
		+ É complicado conseguir un bo rendemento, dividir o traballo entre os núcleos e optimizar a comunicación entre eles

\* Chegamos a un teito tecnolóxico no que respecta ao rendemento das CPUs

Caso do Pentium4:
Tiña hiperthreading: cun único núcleo, comportábase como se divese 2 (ía cambiando dunha tarefa á outra todo o rato (concepto de SOI))
