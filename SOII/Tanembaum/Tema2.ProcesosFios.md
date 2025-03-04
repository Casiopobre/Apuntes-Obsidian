# Tema 2. Procesos e Fíos
Pseudoparalelismo: a CPU conmuta dun proceso a outro con rapidez, dando a sensación de que se están executando paralelamente.
## O modelo do proceso
Todo o software executable se organiza en varios **procesos secuenciais**. 
Un **proceso** é unha _instancia_ dun _programa en execución_, incluíndo o PC, os rexitros e as variables.
A **multiprogramación** consiste na _conmutación_ rápida dun proceso a outro.
![[Multiprogramacion.png | center | 500]]

### Diferencia entre proceso e programa
Un proceso é unha actividade que ten un programa, unha entrada, unha saída e un estado; e varios procesos poden compartir un só procesador (empregando un algoritmo de planificación). En cambio, un programa é un algoritmo que indica os pasos a seguir. Polo tanto, se un programa está a executarse por duplicado conta como dous procesos (xa que aínda que teñan o mesmo programa son dous procesos distintos)
>[!Analoxía]
>Un científico computacional con mente culinaria fai un pastel de aniversario para a súa filla; ten a receita para un pastel de aniversario e unha cociña ben equipada con todos os ingredientes: fariña, ovos, azucre, extracto de vainilla, etcétera. Nesta analoxía, a _receita_ é o _programa_ (é dicir, un algoritmo expresado nunha notación adecuada), o _científico computacional_ é o _procesador_ (CPU) e os _ingredientes_ do bolo son os _datos de entrada_. O _proceso_ é a _actividade_ que consiste en que o noso cociñeiro vaia lendo a receita, obtendo os ingredientes e facendo o pastel.
  Agora imaxina que a filla do científico entra correndo e berrando que unha abella acaba de picala. O científico computacional rexistra o punto da receita no que estaba (o estado do proceso en curso gárdase), saca un libro de primeiros auxilios e comeza a seguir as instrucións que contén. Aquí o procesador conmuta dun proceso (cocer o bolo) a un de maior prioridade (administrar coidados médicos), cada un dos cales ten un programa distinto (a receita e o libro de primeiros auxilios). Cando xa se ocupou da picadura de abella, o científico computacional regresa ao seu pastel e continúa no punto no que quedara.

### Creación dun proceso
Hai 4 **eventos que provocan a creación dun proceso**:
1. O _arranque_ do sistema
2. A execución dunha _chamda ao sistema_ para a creación de procesos (`fork`)
3. Unha _petición de usuario_ para crear un proceso
4. O inicio dun _traballo_ por lotes

Cando se arranca o SO créanse varios procesos:
+ **Procesos en primer plano**: interactúan cos _usuarios_
+ **Procesos en segundo plano**: están asociados cunha _función específica_
+ **Demonios**: permanecen en segundo plano para manexar _certas actividades_

> Para listar os procesos en execución podemos empregar o comando `ps`

Un proceso en execución pode emitir chamadas ao sistema para crear un ou máis procesos novos que lle axuden co seu traballo. En UNIX esta chamada é `fork`, que crea un proceso igual ao que executou a chamada. Despois de fork, os dous procesos (pai e fillo) teñen a mesma imaxe de memoria, as mesmas variables de entorno e os mesmos arquivos abertos (normalmente o proceso fillo executa `execve` para cambiar a súa imaxe de memoria e executar un novo programa).