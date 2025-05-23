# Tema 3. Transaccións
## Concepto de transacción
Unha **transacción** é unha _unidade lóxica_ de traballo que agrupa _varias operacións_ sobre unha BD. As transaccións son esenciais para evitar problemas de integridade e concurrenciana BD.
### Propiedades das transaccións (ACID)
+ **Atomicidade**: a trasacción execútase _completamente_ ou non se executa _nada_ (se ocorre algún fallo, deben desfacerse tódolos cambios)
+ **Consistencia**: a transacción debe levar a BD dun estado consistente a outro (deben cumprirse tódalas _regras de integridade_)
+ **Aislamento**: deben poder executarse _varias transaccións simultáneamente_ sen que unhas interfiran con outras (para non perxudicar o rendemento, o aislamento non ten por qué ser total)
+ **Durabilidade**: unha vez terminada unha transacción, os _cambios_ realizados por esta deben _manterse_ na BD (incluso en caso de fallos no sistema)

## Estrutura de almacenamento
As estruturas de almacenamento axudan a garantir as propiedades de _atomicidade_ e _durabilidade_.
### Tipos de almacenamento
+ **Almacenamento volátil**: 
	+ _Pérdese_ en caso de que caia o sistema
	+ Inclúe a memoria _RAM_ e a _caché_
	+ É o mais _rápido_ (acceso directo)
+ **Almacenamento non volátil**:
	+ _Manteñense_ os datos en caso de caída do sistema
	+ _Discos_ magnéticos, almacenamento flash...
	+ É mais _lento_ e poden afectarlle os cambios físicos
+ **Almacenamento estable**:
	+ Os datos _nunca deberían perderse_
	+ Replícase a información en _varios dispositivos non volátiles_ (ex: sistema RAID)
	+ Debe seguir _protocolos estrictos_ para evitar a perda de datos en caso de fallos durante unha actualización

## Atomicidade e durabilidade nas transacciones
Cando unha transacción se executa nunha BD pode _terminar de dúas maneiras_:
- **Correctamente (Comprometida)**: _aplícanse_ os cambios permanentemente.
- **Incorrectamente (Abortada)**: _retrocédense_ os cambios para que non afecten á BD.
Esto está relacionado coa atomicidade e a durabilidade.
### Estados posibles das transaccións
As transaccións poden pasar por varios estados ó longo da súa execución:
+ **Activa**: en _execución_.
+ **Parcialmente comprometida**: acabou todas as operacións pero _inda non se gardaron_ na BD.
+ **Comprometida**: _confirmouse_ e tódolos seus cambios son _permanentes_.
+ **Fallida**: _detectouse un erro_ que impide a súa execución normal.
+ **Abortada**: _desfixeronse os seus cambios_ con axuda do log (a BD volve ao estado inicial). Podemos eliminala (se o fallo é da transacción) ou volver a intentala (se o fallo é do SXBD)
![[EstadosTransaccion.png| center | 330]]
### Escrituras externas observables
Como as **escrituras externas** (accións que mostran información por pantalla) _non se poden desfacer facilmente_, hai moitos sistemas que, para evitar problemas. só permiten estas escrituras cando a transacción está comprometida.
### Xestión de fallos e recuperación
O sistema mantén un **rexistro de transaccións (logs)** cos cambios realizados. Así, se unha transacción falla, o sistema pode empregalo para _desfacer (rollback)_ os cambios. En cambio, se unha transacción de compromete, a info dos logs permite _recuperar_ os datos en caso de fallos posteriores.
### Transaccións compensatorias
Para **revertir os cambios dunha transacción xa comprometida** debemos executar unha transacción compensatoria. Por exemplo, se unha transacción engade 20€ a unha conta, a súa transacción compensatoria debería restarlle 20€. De todas formas, _non sempre existe_ unha transacción compensatoria para tódolos procesos.
### Transaccións de longa duración
Nos casos nos que as transaccións poden durar moito tempo (como ao procesar grandes volúmenes de datos), permitir que os usuarios poidan _consultar información parcial_ mentres a transacción se está executando pode _comprometer a atomicidade_, polo que existen modelos especiais para manexar este tipo de transaccións sen afectar á consistencia da BD.

## Aislamento das transaccións
### Importancia da execución concurrente
+ **Mellorar o rendemento e a utilización dos recursos**, o que permite que a CPU e o sistema E/S traballen en paralelo.
+ **Reducir os tempos de espera** cando se combinan transaccións curtas e longas.
Aínda así, a execución concorrente pode xerar **problemas** se as transaccións interfiren entre sí (acceden aos mesmos datos sen control), o que pode producir _estados inconsistentes_ na BD:
### Mecanismos de control de concurrencia
Os **mecanismos de control de concurrencia** xestionan como se _intercalan as transaccións concurrentes_ sen comprometer o seu resultado ou a integridade dos datos, e serven para garantir o _aislameno_. A principal técnica para conseguir isto é garantir que a **planificación** (a _orde_ na que as _operacións_ de diferentes transaccións son executadas) sexa **secuenciable** (que produza o _mesmo resultado que unha execución secuencial_).
## Secuencialidade
### Secuencialidade en canto a conflictos
Se unha planificación non é secuenciable, podemos **simplificala**: ir vendo que operacións das transaccións poden ser _intercambiadas_. Se tras este proceso obtemos unha _planificación secuencial_, entón a planificación inicial é secuenciable, é dicir, _as planificacións son equivalentes en canto a conflictos_. 
> [!Exemplo]
> Por exemplo, dado que podemos simplificar a planificacion P1, convertíndoa nunha secuencial (P3), podemos concluír que P1 é secuencializable.
![[ExemploSimplifPlanif.png | center | 550]]
### Grafo de precedencia
É un grafo $G=(V,E)$ no que os $V$ son as **transaccións da planificación** e o conxunto de arestas son as **secuencias $\mathbf{T_i \to T_j}$**  tal que:
+ $T_i$ executa `escribir(Q)` antes de que $T_j$ execute `ler(Q)`
+ $T_i$ executa `ler(Q)` antes de que $T_j$ execute `escribir(Q)`
+ $T_i$ executa `escribir(Q)` antes de que $T_j$ execute `escribir(Q)`
Así, se $\exists$ unha aresta $T_i \to T_j$, entón, en calquera planificación S\` equivalente a S, $T_i$ debe aparecer antes que $T_j$. \*Se o grafo de precedencia de S ten un _ciclo_, entón a planificación de S _non é secuencializable_ en canto a conflictos.
%% Insertar grafo de precedencia das planificaicons anteriores %%
\*Nota: é posible atopar dúas planificacións que produzan o mesmo resultado e que non sexan equivalentes en canto a conflictos.

## Aislamento e atomicidade
Cando unha transacción falla, débese desfacer o se efecto para manter aatomicidade da transacción. Esto pódese complicar cando temos varias transaccións concurrentes que dependen unhas doutras.
### Planificacións recuperables
Unha planificación dise que é recuperable cando, $\forall$ par de transaccións $T_i, T_j$ $/$ $T_j$ lee datos que escribiu previamente $T_i$, a operación de commit de $T_j$ 