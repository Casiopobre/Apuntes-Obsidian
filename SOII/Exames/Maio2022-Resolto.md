##### Exercicio 1.
Cuatro procesos se ejecutan sin que se garantice exclusión mutua de los accesos. Todos los procesos comparten una variable `sum` y todos ejecutan un bucle de 10 iteraciones en el que hacen `sum++`. ¿Cuál es el resultado final del sum?
###### Solución:
Dado que non hai exclusión mútua, como sum non é unha instrucción atómica, o resultado final de sum será un valor entre 2 (no peor dos casos) e 40, en caso de que por sorte ningún proceso pise a outro.


---
##### Exercicio 2.
¿Qué es y que hace el pthread_cond_wait()? ¿Qué son sus argumentos?
```
int pthread_cond_wait(pthread_cond_t *cond, pthread_mutex_t *mutex);
```

###### Solución:
É unha función da librería pthread que fai que o fío que a executa teña que esperar a que se cómpra unha condición específica. Para esto, se lle pasan a condición pola que ten que esperar (mediante unha variable pthread_cond_t) e o mutex que ten que bloquear ou desbloquear según sexa necesario. Máis específicamente, primeiro libera o mutex que se lle pasa e o fío se bloquea. Cando outro fío lle manda que a condición xa se cómpre (mediante pthread_cond_signal()), entón o fío que executou o pthread_cond_wait se desbloquea e segue ca sua execución, bloqueando o mutex para entrar á rexión crítica.

---
##### Exercicio 3.
Determina si el siguiente código tiene algún error y corrígelo. Después explica cómo evitarías carreras críticas en el segundo código.
```asm
Entrar: 
	TSL R1, $1000 
	CMP R1, #0 
	JNE entrar 
	RET 
Salir: 
	MOVE $1000, #0 
	RET
```

```asm
...
ADD R2, R3, R4
LW R5, $3000
ADD R5, R5, R2
SW R5, $3000
...
```
###### Solución:
Supoñendo que na dirección $100 está o candado, o código é correcto, xa que copia o candado a R1, comproba se o candado era 0 e de ser así, volve ao procedemento chamador (en caso contrario, volve a comprobalo). Despois en salir, pon o candado a 0 e volve ao procedemento chamador.

Para evitar carreiras críticas no segundo código, simplemente debemos chamar a Entrar antes de entrar á rexión crítica e a Salir, para salir dela:
```asm
...
CALL Entrar

ADD R2, R3, R4
LW R5, $3000
ADD R5, R5, R2
SW R5, $3000

CALL Sair
...
```

---
##### Exercicio 4.
A partir de este diagrama determina: 
	A. ¿Cuál es un estado inseguro, un estado seguro y un interbloqueo? 
	B. ¿En qué se diferencian las zonas sombreadas con dos tramas y las que están sombreadas con una? 
	C. ¿Que era la zona de esquina superior derecha delimitada por I3, I4, I7 e I8 
	D. Sobre esta figura, ¿cómo se aplicaría el algoritmo del banquero?
![[exer4exame2022.png| center | 400]]
###### Solución:
A. Un estádo inseguro sería o cadrado delimitado entre as intruccións I5 e I6 do proceso B e as instruccións I2 e I3 do proceso A, xa que de entrar en esa rexión, o sistema entrará en interbloqueo, debido a que tanto A como B solicitarán procesos que xa están asignados (A, o trazador, que xa lle está asignado a B; e B a impresora, que xa a ten A). Un estado seguro é calquera que non conduza a un interbloqueo, como por exemplo os instantes p, q, r, s e t. Un interbloqueo é un estado no que ningún dos dous procesos pode avanzar por estaren esperando un recurso posuído polo outro proceso. Neste diagrama, o interbloqueo dase no instante en que B chega a I6 e A chega a I2, xa que chegados a ese punto ningún dos procesos pode avanzar. 

B. As zonas sombreadas cunha trama indican as rexións nas que tanto o proceso A como o proceso B teñen un dos dous recursos (trazador ou impresora), mentres que a zona sombreada con dúas tramas indica a rexión na que ambos procesos teñen ambos recursos simultáneamente. Ambas zonas son rexións imposibles nas que o sistema nunca entrará debido á exclusión mútua (ningún recurso pode ser posuído por dous procesos á vez).

C. Realmente é unha zona na que o sistema nunca entrará, xa que non pode entrar ás zonas sombreadas e non pode volver cara atrás, polo que é imposible que entre nese cadrado.

D. Para aplicar o agoritmo do banqueiro para varios recursos primeiro temos esta situación inicial no instante t:
![[ex4ex2022-1.png| center | 400]]
Agora, según o algoritmo do banqueiro, primeiro buscmos un proceso cuxos recursos que aínda necesita sexan menores ou iguales aos recursos dispoñibles. Neste caso o proceso A satisface esta condición, polo que sumamos os seus recursos asignados ao vector de recursos dispoñibles (A) e o marcamos. Así nos queda a seguinte situación:
![[ex4ex2022-2.png| center | 400]]
Neste instante estamos na intersección entre I4 e I5 da figura de arriba, onde A xa rematou de executarse. Polo tanto, ao volver a repetir o paso 1 do algoritmo, vemos que B satisface a condición, polo que lle sumamos os seus recursos asignados (neste caso 0) ao vector de recursos dispoñibles e o marcamos. Desta forma obtemos a seguinte situación na que ambos procesos remataron de executarse (instante u):
![[ex4ex2022-3.png| center |400]]

---
##### Exercicio 5.
Se quiere comprobar si un sistema es calendarizable a partir de los siguientes procesos: 
+ El proceso A se ejecuta cada segundo y tarda 200 ms en ejecutarse 
+ El proceso B se ejecuta cada 3 segundos y tarda 1 segundo 
+ El proceso C se ejecuta cada 2 segundos y tarda 500 ms en ejecutarse 
+ El proceso D se ejecuta cada 10 segundos y tarda 2 segundos en ejecutarse
###### Solución:
Temos que para que un sistema operativo en tempo real sexa calendarizable, tense que cumprir que o factor de utilización da CPU ($U$), non supere 1:
$$
\sum_{i=1}^{n} \frac{C_i}{T_i} = U \leq 1
$$
Polo tanto, se aplicamos a fórmula aos datos do enunciado, e expresando o tempo en ms, obtemos que:
$$
U=\frac{200}{1000}+\frac{1000}{3000}+\frac{500}{2000}+\frac{2000}{10000}=0.98\lt 1
$$
Polo tanto, o sistema é calendarizable.
>[!Nota]
> Este sistema probabemente fallaría se empregase RMS debido a que este algoritmo só funciona cando
> $$
> U \leq n(2^{1/n}-1)
> $$ 
> Polo que no caso de ter 4 procesos ($n=4$), obteríamos que:
> $$ 
> U = 0.98 \gt 0.75682846
> $$
> Polo tanto, se empregasemos RMS neste sistema, tería moitas probabilidades de fallar. Deberíamos empregar EDF, que sempre funciona para calquera conxunto programable de procesos.

---
##### Exercicio 6: Bash
a) ¿Qué es la canalización (pipe)? 
b) ¿Qué es Useless Use of Commands? Pon ejemplo y como se resuelvo 
c) Describe el proceso iterativo del proceso de comandos. Diferencia entre comandos internos y externos

###### Solución:
a) A canalización en bash refírese a empregar a saída dun comando como entrada de outro, e se indica da seguinte forma:
```bash
comando1 | comando2
```
Onde a saída do comando1, se empregará como entrada do comando2.

b) O Useless Use of Commands refírese a empregar un comando cando o seu uso non é necesario e pode levar a que a acción tarde máis tempo en realizarse. Un exemplo é o coñecido como Useless Use of Cat, que se refire a empregar o comando cat, que mostra o contido dun arquivo, cando non é realmente necesario. Por exemplo:
```bash
cat arquivo | comando
```
É innecesario, xa que se pode facer directamente:
```bash
arquivo < comando
```
O que evita crear outro proceso para que execute o cat.

c) Cando se executa un comando externo, o shell crea un proceso para que execute ese comando da seguinte forma: inicia un novo proceso (`fork`) e cambia a súa imaxe pola do arquivo executable (`exec`). Polo tanto, o fillo executa o comando (executando o binario) e o pai (a consola) espera polo fillo.
Así, a diferencia entre comandos internos e externos é que os internos están integrados na shell e son executados pola propia shell, mentres que os externos non, e deben ser executados por outro proceso distinto.
