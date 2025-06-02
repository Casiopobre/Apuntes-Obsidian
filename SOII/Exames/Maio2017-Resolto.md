##### Exercicio 1
Explica brevemente qué hacen las siguientes líneas de bash, indicando el significado de los argumentos y los separadores. Indica también cuál es su salida.
a) `ps $USER | wc –l`
b) `chmod 755 f04`
c) `if [ $# -ge 2 ]; then`
d) `cat $1 | tac > ord_$1`
e) `while [ ! -e $1/$2 ]`
f) `ls ?`
###### Solución:
a) O comando `wc -l` conta as liñas resultado de executar `ps -U $USER`, que mostra os procesos activos do usuario (PID, nome...)

b) Concede, sobre o arquivo f04, os seguintes permisos: para o usuario dono do arquivo, tódolos permisos (7 = 111 en binario = r w x), e para o grupo de usuarios dono do arquivo e outros, só lles outorga permisos de lectura e execución (5 = 101 en binario = r - x)

c) Comproba que o número de parámetros (`$#`) sexa maior ou igual (`-ge`) que 2 e, de ser así, executa o código que haxa entre then e fi.

d) Colle o contido dun arquivo indicado polo primer parámetro (`$1`) e garda cada liña en orde inverso, é decir, empezando polo final, nun arquivo que vai chamar `ord_{nome_arquivo}`.

e) Executa os comandos que se lle indiquen abaixo ata o done mentres o arquivo`$1` do subdirectorio `$2` non exista.

f) Lista os ficheiros dos directorios que teñan como nome un só caracter.

---
##### Exercicio 2
Programa las funciones down y up de semáforos usando la instrucción atómica TSL.
###### Solución:
```asm
mutex_lock:
	TSL R1, candado     ; Copia o candado ao R1 e o pon a 1
	CMP R1, #0          ; Comproba se o candado era 0 (estaba libre)
	JNE mutex_lock      ; Se non era 0, volve a intentalo
	RET                 ; Volve ao procedemento chamador

mutex_unlock:
	MOV candado, #0     ; Pon o candado a 0 (o libera)
	RET

sem_up:
	CALL mutex_lock     ; Bloquea o mutex -> Entra na rexion critica
	ADD sem, sem, #1    ; sem++
	CALL mutex_unlock   ; Desbloquea o mutex -> Sae da rexion critica
	RET

sem_down:
	CALL mutex_lock     ; Bloquea o mutex -> Entra na rexion critica
	MOV R1, sem         ; Garda o semaforo en R1
	CMP R1, #0          ; Comproba se o semaforo era 0
	JE block_process    ; Se era 0, bloquea o proceso --> espera
	SUB sem, sem, #1    ; sem--
	CALL mutex_unlock   ; Desbloquea o mutex -> Sae da rexion critica
	RET

block_process:
	CALL mutex_unlock   ; Desbloquea o mutex
	CALL sched_yield    ; Lle cede a CPU a outro proceso
	JMP sem_down        ; Volve a intentar baixar o semáforo
```

---
##### Exercicio 3
Dos procesos ejecutan los códigos indicados, en donde A es una variable compartida e inicializada a 0. Indica razonadamente qué valor se imprimirá en la consola para el caso de que P1 llegue antes que P2 al mutex_lock, y para el caso en que ocurra lo contrario.
![[ex3ex2017.png| center | 400]]
###### Solución:
Se P1 chega primeiro ao `mutex_lock(m1)`, cando chegue a `cond_wait(c1, m1)`, liberará o mutex, polo que P2 poderá bloquealo e acceder á rexión crítica. Despois, P2 lle mandará a sinal a P1 ca variable de condición `c1`, pero como o mutex xa está bloqueado por P2, P1 se queda esperando a que o mutex estea desbloqueado (e coma se volvese a executar un `mutex_lock(m1)`). P2 segue ca súa execución, actualizando a variable `A=2` e desbloqueando o mutex xusto antes de imprimir. Nese momento, aínda que o mais probable é que se imprima o `2` antes de que a CPU lle dea o quantum a P1, pode darse o caso de que o sistema decida cederlle a CPU a P1 xusto antes de que P2 imprima nada. Entón, P1 escribirá `A=1` e desbloqueará o mutex. Cando P2 se volva a executar por onde se quedou, imprimirá o valor de A que gardou P1, é decir, `1`.

Por outro lado, se P2 chega antes ao `mutex_lock(m1)`, entón a sinal coa variable de condición que manda se perde, polo que se executa P2 ata o final e se imprime `2`. Neste caso nunca se chegaría a imprimir `1` porque o proceso P1 se quedaría bloqueado en `cond_wait(c1, m1)`, esperando a sinal que se perdeu antes.

---
##### Exercicio 4
La condición para que un sistema sea calendarizable es: $\sum \frac{C_i}{T_i} \leq 1$
Reescríbela para el caso en el que dicho sistema tiene un quantum de $X$ milisegundos y los cambios de contexto tardan en ejecutarse $Y$ microsegundos.
###### Solución:
A condición para que un sistema sexa calendarizable é que a suma das porcentaxes de tempo de CPU que necesita cada proceso sexa menor que 1 (menor ao 100% de CPU). Se temos en conta os cambios de contexto e o quantum da CPU, entón temos que, para cada proceso, hai que sumarlle o tempo que tarda o cambio de contexto, multiplicado polo número de cambios de contexto que fai cada proceso, polo que nos queda unha expresión tal que:
$$
\sum_{i=0}^n C_i + \frac{C_i}{X\cdot 10^3} \cdot Y \leq 1
$$

> [!Nota]
> Outra forma de velo e da seguinte maneira: calculamos a porcentaxe de tempo de CPU que vai ser ocupada polos cambios de contexto como
> $$
> CC=\frac{Y}{X\cdot 10^3}
> $$
> E despois lle sumamos eso ao factor de utilización da CPU, $U$, de forma que obtemos que un sistema é planificable se cómpre que:
> $$
> CC+U= \frac{Y}{X\cdot 10^3} + \sum_{i=1}^{n} \frac{C_i}{T_i} \leq 1
> $$


---
##### Exercicio 5
Una de las cuatro condiciones para que ocurra un interbloqueo es la condición de espera circular. Indica un método para atacarla y por tanto evitar interbloqueos.
###### Solución:
Hai dúas opcións: podemos ter unha regra que nos indique que un proceso só ten dereito a un só recurso en calquera momento, polo que non pode ter varios recursos á vez; ou numerar tódolos recursos de forma que os procesos poidan solicitar recursos sempre que queiran pero realizando as peticións en orde numérica, logrando así que o grafo de recursos nunca teña ciclos, polo que nunca se producirá un interbloqueo.

---
##### Exercicio 6
Sea un sistema con cinco procesos, P1 a P5, y tres tipos de recursos, A, B y C, de los que existen 9, 5 y 7 ejemplares o instancias, respectivamente. Supongamos que en el instante actual tenemos la situación del sistema dada por las tablas adjuntas. Contesta a las siguientes cuestiones: 
(a) ¿Es un estado seguro? 
(b) ¿Cómo actuaría el algoritmo del banquero si ahora, el proceso P2 hace una petición de una instancia del recurso A? 
(c) ¿Y si posteriormente P5 pide una instancia del recurso C?  
(d) ¿Y si más tarde P3 solicita otra del recurso A?
![[ex6ex2017.png| center | 500]]
###### Solución:
a) Sí, xa que se aplicamos o algoritmo do banqueiro, atopamos que é posible executar os procesos na seguinte orde de forma que non ocorran interbloqueos: P4 $\rightarrow$ P2 $\rightarrow$ P3 $\rightarrow$ P1 $\rightarrow$ P5
![[ex6-ex2017-1.png| center |500]]

b) Non llo outorgará debido a que conduciría a un interbloqueo:
![[ex6ex2017-3.png|center|280]]
Se o sistema lle outorga a petición a P2, entón non terá suficientes instancias do recurso A para executar ningún proceso.

c)  Tamén conduce a un estado inseguro, polo que o sistema non llo concederá:
![[ex6ex2017-4.png|center|280]]

d) Lle outorgará o recurso xa que esto conduce a un estado seguro como se pode ver na seguint imaxe:
![[ex6ex2017-5.png| center | 500]]

---
