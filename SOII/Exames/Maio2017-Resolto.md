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

f) Lista os ficheiros do directorio `?` se existe.

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
	JE .wait            ; Se era 0, bloquea o proceso --> espera
	SUB sem, sem, #1    ; sem--
	CALL mutex_unlock   ; Desbloquea o mutex -> Sae da rexion critica
	RET

.wait:
	CALL mutex_unlock   ; Desbloquea o mutex
	CMP R1, #0          ; Comproba se o candado estaba a 0
	JNE .return         ; Se non era 0, volve
	CALL sched_yield    ; Lle cede a CPU a outro proceso
	JMP sem_down        ; Volve a intentar baixar o semáforo

.return:
	RET
```

---
##### Exercicio 3
Dos procesos ejecutan los códigos indicados, en donde A es una variable compartida e inicializada a 0. Indica razonadamente qué valor se imprimirá en la consola para el caso de que P1 llegue antes que P2 al mutex_lock, y para el caso en que ocurra lo contrario.
![[ex3ex2017.png| center | 400]]
###### Solución:

---
##### Exercicio 4
La condición para que un sistema sea calendarizable es: $\sum \frac{C_i}{T_i} \leq 1$
Reescríbela para el caso en el que dicho sistema tiene un quantum de $X$ milisegundos y los cambios de contexto tardan en ejecutarse $Y$ microsegundos.
###### Solución:

---
##### Exercicio 5
Una de las cuatro condiciones para que ocurra un interbloqueo es la condición de espera circular. Indica un método para atacarla y por tanto evitar interbloqueos.
###### Solución:

---
##### Exercicio 6
Sea un sistema con cinco procesos, P1 a P5, y tres tipos de recursos, A, B y C, de los que existen 9, 5 y 7 ejemplares o instancias, respectivamente. Supongamos que en el instante actual tenemos la situación del sistema dada por las tablas adjuntas. Contesta a las siguientes cuestiones: 
(a) ¿Es un estado seguro? 
(b) ¿Cómo actuaría el algoritmo del banquero si ahora, el proceso P2 hace una petición de una instancia del recurso A? 
(c) ¿Y si posteriormente P5 pide una instancia del recurso C?  
(d) ¿Y si más tarde P3 solicita otra del recurso A?
![[ex6ex2017.png| center | 500]]
###### Solución:

---
