# Tema 5. Sistema de Recuperación
## Clasificación dos fallos
### Fallo na transacción
Pode ser debido a un:
+ **Erro lóxico**: algunha _condición interna_, como unha entrada incorrecta ou datos non atopados
+ **Erro do sistema**: o _sistema_ está nun _estado non desexado_, polo que a transacción non pode seguir executándose, pero pode volver a intentalo máis tarde.
### Caída do sistema
Debida a un mal funcionamento de hardware ou software, o que causa a perda do contido da memoria volátil e aborta unha transacción, deixando _intacto o contido da memoria non volátil_ (**suposto de fallo-parada** $\rightarrow$ _comprobacións internas_ que aboran o sistema cando hai un erro).
### Fallo de disco
Ocorre cando un **bloque do disco perde o seu contido**. Para recuperarse do fallo, se empregan as copias de seguridade que se atopan en medios de almacenamento secundarios 

## Almacenamento
Para poder determinar o medio polo cal o sistema debe recuperarse, debemos indentificar os modos de fallo dos dispositivos de almacenamento. Así pódense propoñer **algoritmos de recuperación** que garanticen a _consistencia_ e a _atomicidade_ da base de datos. Estes algoritmos teñen dúas partes: 
+ Accións que se levan a _cabo durante o procesamento de transaccións_ $\rightarrow$ asegurar que exista _infromación_ suficiente para recuperarse do fallo 
+ Accións _posteriores a un fallo_ $\rightarrow$ _restablecer_ o contido da BD
### Implemenatción do almacenamento estable
Replícase a información necesaria en **varios medios de almacenamento non volátil** con **modos de fallo independentes** e se *actualiza* de forma controlada. Os sistemas máis seguros realizan unha _copia de seguridade remota_ (copia de cada bloque de almacenamento estable nun lugar remoto a través da rede).

As **transferencias** de bloques **entre a memoria e o disco** poden acabar de tres formas:
+ _Éxito_: a información chega ao seu destino sen fallos
+ _Fallo parcial_: chega información incorrecta debido a un fallo no medio de transferencia
+ _Fallo total_: non chega información ao bloque destino, polo que permanece intacto

Cando se produce un **fallo de tranferencia de datos**, o sistema debe _detectalo_ e iniciar un procedemento de _restauración_ $\rightarrow$ ==o sistema necesita dous bloques físicos por cada bloqeue lóxico da BD==
Polo tanto, ao transferir datos de memoria intermedia a disco (operación de saída), faise da seguinte maneira:
1. Escríbese a información no primer bloque físico
2. Cando a primeira escritura se completa correctamente, escríbese a mesma información no segundo bloque físico
3. Cando a segunda escritura acaba correctamente, a saída se completou

Ao finalizar, **examínanse o par de bloques físicos**:
+ Ambos _coinciden_ e _non_ existen _erros_ detectables $\rightarrow$ non hai que facer nada máis
+ _Un bloque ten un erro_ detectable $\rightarrow$ substitúese o seu contido polo do outro bloque
+ Ningún ten erros detectables pero teñen _información distinta_ $\rightarrow$ substitúese o contido do primer bloque polo do segundo
Así, garántese que a escritura en almacenamento estable sexa _atómica_ (ou se completa correctamente ou non se escribe nada).

### Acceso aos datos
A base de datos divídese en **bloques** (unidades de almacenamento de lonxitude fixa) que se _transfiren_ entre as unidades de memoria. Os bloques do _disco_ denomínanse **bloques físicos**, e os que están temporalmente na _memoria principal_ (unha zona denominada __memoria intermedia de disco__), **bloques de memoria intermedia**. 
Para **transferir** bloques (B) entre o **disco e memoria principal** empréganse dúas operacións:
+ `entrada(B)`:  disco $\rightarrow$ memoria principal
+ `saida(B)`: memoria principal $\rightarrow$ disco

Ademáis, cada **transacción** ten unha **área de traballo privada** na que se gardan _copias_ ($x_{i}$) de tódolos elementos de datos da memoria principal ($X$) que emprega, que se crea cando comenza a transacción e se elimina cando remata. Así, cada transacción interatúa coa BD mediante _transferencias de datos entre a súa área privada e a memoria intermedia_, que se realizan mediante as operacións seguintes:
+ `ler(X)`: valor do elemento de datos _da memoria intermedia_ $X$ $\rightarrow$ _variable local_ $x_i$.
	+ Se o bloque $B_{X}$ no que está o elemento $X$ non se atopa en memoria principal, traeno con `entrada(B_X)`.
	+ Copia o valor de $X$ en $x_i$
+ `escribir(X)`: valor da _variable local_ $x_i$ $\rightarrow$ elemento de datos X da _memoria intermedia_
	+ Se o bloque $B_{X}$ no que está o elemento $X$ non se atopa en memoria principal, traeno con `entrada(B_X)`.
	+ Copia o valor de $x_i$ en $X$ (que se atopa no bloque $B_X$)
Ambas operacións poden requerir unha entrada, pero _ningunha require explícitamente unha saída_, así, cando o sistema copia os datos de memoria principal ao disco executando `salida(B)` dise que o sistema **forzou a saída** de B.
![[transferenciaDatos.png | center | 400]]

### Rexistro histórico
O rexistro histórco é unha **secuencia de rexistros** que almacena todas as *actividades de actualización* da base de datos e que se almacena en _memoria estable_. Estes rexistros poden ser de _distintos tipos_. Para describir unha única escritura na BD emprégase un **rexistro de actualización do rexistro histórico**, que ten os seguintes campos:
+ $T_{i}$ $\rightarrow$ identificador da _transacción_ (a transacción realiza a operación `escribir`)
+ $X_{j}$ $\rightarrow$ identificador do _elemento de datos_ (o elemento que se escribe)
+ $V_{1}$ $\rightarrow$ _valor anterior_ do elemento de datos (antes da escritura)
+ $V_{2}$ $\rightarrow$ _valor posterior_ do elemento de datos (despois da escritura)
Polo tanto, unha actualización represéntase da seguinte maneira: $<T_{i}, X_{j}, V_{1}, V_{2}>$
_Outros tipos_ de rexistro histórico son:
+ $<T_{i}$ iniciada $>$ 
+ $<T_{i}$ comprometida $>$
+ $<T_{i}$ abortada $>$
> [!Nota]
 Considérase unha transacción comprometida aínda que os datos non estean aínda no disco, xa que **é máis importante que os cambios estean reflectidos no rexistro histórico**, que se garda en memoria estable, que no disco.

### Modificación da Base de Datos
Cando unha transacción modifica un elemento de datos, realiza os seguintes **pasos**:
1. Realiza algúns cálculos na súa área privada de memoria
2. Modifica o bloque de datos da memoria intermedia (`escribir`)
3. O sistema garda os datos no disco (`salida`)

As _actualizacións_ que faga a transacción na súa _área privada non contan como modificacións_ na base de datos ata que non se actualizan na _memoria intemedia_ ou no propio disco. Polo tanto, hai _dous tipos de modificacións_:
+ **Modificación inmediata**: vanse escribindo os datos no disco cando o SO poida (_non espera a que remate a transacción_).
+ **Modificación diferida**: espérase a que a _transacción estea comprometida_ para gardar os datos no disco. Ten a _sobrecarga_ de que a transacción necesita facer unha copia local de tódolos elementos de datos actualizados.
\* En ambos casos, mentres non se actualiza o disco, os datos gárdanse en _memoria intermedia_. 

Polo tanto, un **algoritmo de recuperación** ten que ter en conta:
+ Que unha transacción pode estar comprmetida aínda que algunhas modificacións _non estean aínda no disco_.
+ Que unha transacción pode ter modificado a base de datos e despois necesite _abortar_.
Así, cando hai un fallo, o sistema pode realizar **dúas operacións**, empregando o **rexistro histórico**:
+ ==Desfacer==: axústase o elemento de datos especificado no rexistro ao _valor anterior_ 
+ ==Refacer==: axústase o elemento de datos especificado no rexistro ao _valor novo_ (emprégase cando unha transacción xa foi comprometida)

### Control de concurrencia e recuperación
Os **algoritmos de recuperación** requiren que, se unha transacción _modifica un elemento de datos_, entón _ningunha outra transacción_ poida _modificalo_ ata que a outra transacción remate (exclusión mútua) $\rightarrow$ a transacción adquire un **bloqueo exclusivo** sobre os datos que actualiza ata que remate.
>Polo tanto, pódese decir que as actualizacións da BD por unha transacción sempre son diferidas ata que a transacción estea parcialmente comprometida.

### Transacción comprometida
Unha transacción está comprometida cando **o seu rexistro de rexistro histórico está comprometido**, é decir, se gardou en _almacenamento estable_. Polo tanto, se ocorre un fallo do sistema antes de que o rexistro $<T_{i}$ comprometida $>$ se garde en almacenamento estable, a transacción $T_{i}$ volve atrás.

### Emprego do rexistro histórico para desfacer e refacer transacciones
**Procedementos de recuperación** $\rightarrow$ empregan o rexistro histórico para atopar qué datos actualiza cada transacción $T_{i}$, e os seus valores anterior e novo:
+ `desfacer(T_i)`: restaura o valor de tódolos _elementos de datos actualizados_ por $T_i$ aos seus **valores anteriores**.
	+ Escribe **rexistros só-desfacer** especiais do rexistro histórico, que gardan as _actualizacións_ realizadas durante a operación _desfacer_ (_non conteñen o valor anterior_ dos elementos de datos actualizados).
	+ Cando se completa a operación `desfacer(T_i)`, _escríbese nun rexistro_ $<T_{i}$ aborta $>$ para indicar que se rematou de desfacer
	+ Desfacer _só se executa unha vez por transacción_.
+ `refacer(T_i)`: restaura o valor de tódolos _elementos de datos actualizados_ por $T_i$ aos seus **valores novos**.
	+ A operación refacer debe executarse nunha _orde equivalente á orixinal_ para que o elemento de datos non teña un valor erróneo. Por esto, a maioría de algoritmos realizan un _percorrido polo rexistro histórico aplicando a operación refacer a cada rexistro_, de forma que asegura que se mantén a orde.

Así, eventualmente ==toda transacción terá un rexistro compometida ou abortada no rexistro histórico==.

**Cando se produce un fallo no sistema**, o esquema de recuperación _consulta o rexistro histórico_ e determina as _transaccións_ que deben _refacerse_ e as que deben _desfacerse_:
+ $T_{1}$ debe **desfacerse** $\rightarrow$ o rexistro histórico contén $<T_{i}$ iniciada $>$ pero _non_ $<T_{i}$ comprometida $>$ ou $<T_{i}$ abortada $>$.
+ $T_{1}$ debe **refacerse** $\rightarrow$ o rexistro histórico contén $<T_{i}$ iniciada $>$ _e_ $<T_{i}$ comprometida $>$ ou $<T_{i}$ abortada $>$.

> [!Exemplo]
> Sexan $T_{0}$ e $T_{1}$ dúas transaccións que traspasan 50€ dunha conta a outra e retiran 100€ dunha conta, respectivamente, e sexa a imaxe seguinte o mesmo rexistro histórico en diferentes momentos:
> ![[Pasted image 20250405214331.png | center | 400]]
> **a) Supoñamos que o fallo do sistema ocorre xunto despois de que `escribir(B)` de $T_0$ se escribise en almacenamento estable.** 
> Cando o sistema volve a funcionar, atopa que no rexistro histórico está $<T_{0}$ iniciada $>$, pero non o seu correspondente $<T_{0}$ comprometida $>$ ou $<T_{0}$ abortada $>$, polo que se debe _desfacer_ $\rightarrow$ execútase `desfacer(T_0)`
> **b) Supoñamos que o fallo do sistema ocorre xunto despois de que `escribir(C)` de $T_1$ se escribise en almacenamento estable.** 
> Cando o sistema volva a funcionar, deberá _desfacer_ $T_{1}$, xa que ten o seu iniciada pero non o seu comprometida ou abortada; e _refacer_ $T_{0}$, xa que ten o seu iniciada e o seu comprometida 
> **c) Supoñamos que o fallo do sistema ocorre xunto despois de escribir $<T_{1}$ comprometida $>$**
> Deberán _refacerse_ tanto $T_{0}$ como $T_{1}$, xa que ambas teñen o seu iniciada e comprometida.

### Puntos de revisión
"Son como checkpoints". Introdúcense para _reducir a sobrecarga_ do proceso de búsqueda, e se realizan da seguinte maneira:
1. Escríbense en **almacenamento estable** tódolos rexistros do rexistro histórico que estean na _memoria principal_.
2. Escríbense **no disco** tódolos bloques de _memoria intermedia_ que se modificaran.
3. Escríbese **en almacenamento estable** un rexistro do rexistro histórico chamado $<$revisión $L >$, onde $L$ é a _lista de transaccións activas_ no momento do punto de revisión
\* Mentres se estea levando a cabo un punto de revisión, ningunha transacción pode realizar accións de actualización.
O rexistro $<$revisión $L >$ permite facer _máis eficiente_ o proceso de recuperación:
> [!Exemplo]
> Se temos unha transacción $T_i$ que se comprometeu antes do punto de revisión, dado que o rexistro $<T_{1}$ comprometida $>$ aparece antes que o rexistro $<$ revision $>$, entón no momento da recuperación non é necesario executar refacer sobre $T_i$ (xa que todas as modificacións feitas por $T_{i}$ xa deberían estar escritas na BD).

Cando **se produce un fallo** $\rightarrow$ o esquema de recuperación busca no rexistro histórico o _último rexistro_ de tipo $<$revisión $L>$ $\rightarrow$ aplica desfacer ou refacer só ás _transaccións de L_ e as que se iniciasen _despois_ do rexistro $<$revisión $L >$
> **Punto de revisión difuso**: punto de revisión no que se permiten transaccións mentres se escriben en disco os bloques de memoria intermedia

## Algoritmo de revisión







