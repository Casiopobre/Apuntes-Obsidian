# Tema 3. Núcleos de procesamento: paralelismo a nivel de instrucción
## Repaso
A **segmentación** permítenos executar varias instruccións en paralelo $\rightarrow$ Paralelismo a nivel de instrucción (**ILP**) $\rightarrow$ Obxectivo: _optimizar o CPI_ (que se ve afectado polas paradas):
+ Pipeline máis profundo
+ Emisión múltiple
\* Ver esquuemas

As paradas poden ser ocasionadas polas **dependencias de nome** (WAR e WAW), que non son dependencias de datos reais, e que non producen riscos no MIPS estudado (en outras arquitecturas sí, polo que se resolven mediante o _renombrado de rexistros_)
## Emisión múltiple
Consiste en comezar múltiples instruccións en cada ciclo de reloxo, e pode ser de dous tipos.
### Emisión múltiple estática
O **compilador** agrupa instruccións para emitilas xuntas en _paquetes de emisión_ de forma que non ocasionen dependencias. Así, é o compilador o que **detecta e evita riscos** $\rightarrow$ **reduce ou elimina as paradas**.

**Paquete de emisión**: grupo de instruccións que poden ser emitidas nun so ciclo e que, polo tanto, se poden executar _simultáneamente_ xa que _non teñen dependencias_ entre si ("instrucción moi longa").
$\rightarrow$ Son executados por _arquitecturas VLIW_ (Very Long Instruction Word)

Papel do **compilador**: **eliminar riscos**
+ _Reordenar as instruccións_ $\rightarrow$ Conseguir paquetes de emisión
+ _Reencher con NOP_ se é necesario

#### MIPS con emisión dual estática
Ten paquetes de **dúas instruccións**: unha ALU/salto e despois unha load/store
![[MIPSEmisionDualEstat.png | center | 500]]
Camiño de datos:
![[MIPSEmisionDualEstCaminoDatos.png | center | 550]]

Execútanse máis insctruccións en paralelo pero 
+ Surxen **novos riscos de datos en EX** xa que agora xa non podemos empregar o resultado dunha instrucción ALU nunha load/store no mesmo paquete de instruccións (non podemos empregar o forwarding $\rightarrow$ _dividilo en dous paquetes_).
+ Pódese producir un **risco de carga-uso entre paquetes**: segue a haber un ciclo de parada, pero agora execútanse dúas instruccións en cada paquete
$\rightarrow$ Polo tanto, requírese unha **planificación máis agresiva** (hardware)
> [!Exemplo de planificación]
> Consideramos salto retardado con unha ranura de salto
> ![[ExemploPlanificacionMIPSDual.png| center | 500]]

#### Desenrollamento de lazos
\* Librería BLAS (matrices) $\rightarrow$ Fai desenrollamento
Consiste en **replicar o corpo do lazo para expoñer máis o paralelismo**, é decir, en facer menos iteracións pero operando máis datos en cada iteración:
+ Reduce a sobrecarga por control de lazo
+ É beneficioso para reempregar datos almacenados na caché





