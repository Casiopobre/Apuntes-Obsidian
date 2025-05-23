**1.(1.75 puntos) Escribe a especificación formal dun TAD-bonobus que permita crear un bono, insertar diñeiro no bono (número natural de euros), coñecer se hai diñeiro no bono e realizar un gasto de viaxe no bus (cuxo prezo é sempre dun euro).)**

Especificación formal do TAD bonobus:
Sintaxis:
```
*crearBonoBaleiro --> bonobus
insertarDiñeiro(bonobus, natural) --> bonobus
eBaleiro(bonobus) --> booleano
realizarViaxe(bonobus) --> bonobus
```

Semantica: $\forall b \in bonobus, \forall n \in \mathbf{N}$
```
eBaleiro(crearBonoBaleiro) --> TRUE
eBaleiro(insertarDiñeiro(b, n)) --> FALSE
realizarViaxe(crearBonoBaleiro) --> ERRO
realizarViaxe(InsertarBono(b, e)) --> si eBaleiro(b) --> ERRRO | realizarViaxe(b)
```

**2.(1 punto) Unha cola impleméntase internamente coa estructura descrita no seguinte gráfico:**
**![[Pasted image 20250514160011.png | center | 300]]**
**As declaracións dentro de cola.c son as seguintes:**
```c
typedef int TELEMENTO;

typedef struct nodo {
	TELEMENTO datos
	struct nodo *sig;
} TNodo;

typedef struct {
	TNodo *principio, *final;
} STcola;

typedef STcola * TCOLA;
```
**Cada apartado conta 0.5 puntos, erros de sintaxe restan ata 0.25 puntos e erros lóxicos graves ata**
**0.5 puntos.**
**(a) (0.5 puntos) Escribe a implementación (con punteiros) da función que elimina o primeiro elemento da cola.**
```c
void elimina(TCOLA *q){
	if(esVacia(*q)) return;
	TNodo *aux = (*q)->principio;
	(*q)->principio = aux->sig;
	
	if ((*q)->principio->sig = NULL) (*q)->final = NULL;
	
	free(aux);
}
```

**(b) (0.5 puntos) Escribe a implementación da función que inserta un elemento ó final da cola.**
```c
void inserta(TCOLA *q, TELEMENTO e){
	TNodo *nodo = (TNodo *) malloc(sizeof(TNodo));
	nodo->datos = e;
	nodo->sig = NULL;
	
	if (esVacia(*q)) (q*)->principio = nodo;
	else (*q)->final->sig = nodo;

	(*q)->final = nodo;
}
```

**3.(3 puntos) Disponse dun TAD lista coas aoperacións tı́picas (ver .h a continuación) e onde o tipoelemento é un struct que permite gardar nomes de persoas xunto ás súas idades, DNI, nacionalidade (que é unha cadea de caracteres) e un campo vacinado que indica o número de vacinas que recibeu a persoa.**
```c
struct Datos {
	char nombre[100];
	char dni_nie[9];
	char nacionalidad[100];
	unsigned short edad;
	unsigned short vacunado;
}
typedef struct Datos TIPOELEM;
typedef void *POSICION;
typedef void *TLISTA;
void crea(TLISTA *l);
void destruye(TLISTA *l);
POSICION primero(TLISTA l);
POSICION fin(TLISTA l);
POSICION siguiente(TLISTA l, POSICION p);
POSICION anterior(TLISTA l, POSICION p);
int esVacia(TLISTA l);
void recupera(TLISTA l, POSICION p, TIPOELEM *e);
int longitud(TLISTA L);
void inserta(TLISTA *l, POSICION p, TIPOELEM e);
void suprime(TLISTA *l, POSICION p);
void modifica(TLISTA *l, POSICION p, TIPOELEM e);
```
**Por outro lado, disponse dun TAD cola con prioridade que trabalları́a sobre os elementos da lista creada anteriormente.**
```c
typedef void *TCOLAPRIO;
void crearColaPrio(TCOLAPRIO *q);
void destruirColaPrio(TCOLAPRIO *q);
int esColaVaciaPrio(TCOLAPRIO q);
void consultarPrimerElementoColaPrio(TCOLAPRIO q, TIPOELEM *e);
void suprimirElementoColaPrio(TCOLAPRIO *q);
void anadirElementoColaPrio(TCOLA *q, TIPOELEM e, unsigned int prioridad);
```
**donde o numero de prioridade é un valor maior ou igual a un, personalizable polo software que empregue a librerı́a, e que canto maior valor teña a prioridade do elemento insertado máis rapidamente sairá elemento da cola.**

**O SERGAS dispón de unha lista con todas as posibles persoas domiciliadas no territorio galego donde se almacena cántas vacinas recibiu cada persoa. Nestos momentos, coa vacinación da terceira xa practicamente completada, desexase convocar a todas as persoas maiores (de idade superior a 80 anos) para unha cuarta inxeccion. Ademáis, todas aquelas persoas que dispoñan de menos de tres vacinas (independentemente da súa idade) deben tamén ser chamadas, ainda que serán chamadas menos prioritarias que as de maiores de 80 anos. Os maiores de 80 anos con menos de tres vacinas son os máis prioritarios. As persoas en cada grupo de prioridade deben incorporarse á vacinación na orde na que están nas listas do sergas. Cabe contemplar que a lista do serga poderı́a estar vacı́a, polo que a función deberá contemplar todos os casos posibles. Escribe unha función que, utilizando as librerı́as anteriores, reciba esa lista de persoas (parámetro de entrada) e xenere unha lista de prioridade que serı́a a que fora ser empregada no proceso de vacinación do vacunódromo.**

```c
TCOLAPRIO obterColaPrio(TLISTA listaSergas){
	// Creamos a cola
	TCOLAPRIO q;
	crearColaPrio(&q);

	if (esVacia(listaSergas)) return q;

	// Recorremos a lista
	POSICION pos = primero(listaSergas);
	TIPOELEM persona;
	while (pos != fin(listaSergas)){
		recupera(listaSergas, pos, &persona);
		if(persona.vacunado < 3 && persona.edad > 80){
			anadirElementoColaPrio(&q, persona, 3);
		} else if (persona.edad > 80){
			anadirElementoColaPrio(&q, persona, 2);
		} else if (persona.vacunado < 3){
			anadirElementoColaPrio(&q, persona, 1);
		}
		pos = siguiente(listaSergas, pos);
	}
	return q;
}
```

**4.(1 punto) Dada la lista del SERGAS descrita en el apartado anterior, si tuviésemos la necesidad de conocer cuántos mayores tienen menos de 3 vacunas, ¿qué complejidad algorítmica temporal tendrá el correspondiente proceso de cómputo de ese número? Explica cuál sería la complejidad temporal en el mejor de los casos, en el peor de los casos y en el caso promedio.**
Tería complexidade lineal ($\mathbf{O}(n)$), xa que sería necesario recorrer a lista unha única vez. Tanto no mellor, como  no peor, como no caso promedio sería lineal, porque en calquera situación é necesario recorrer toda a lista.


**5.(1 punto) Una nación extranjera desea conocer si algún ciudadano de su país está en la lista del SERGAS y tiene menos de 3 vacunas. En la implementación más eficiente posible de este proceso, explica cuál sería la complejidad temporal en el mejor de los casos, en el peor de los casos y el caso promedio.**
No mellor dos casos sería $O(1)$, xa que o elemento buscado podería ser o primeiro. No peor dos casos, sería $O(n)$, xa que pode ser qu eningunha personan cumpra coa condición solicitada ou que esta sexa a última da lista. Polo tanto, no caso promedio obteríamos $O(n)$, xa que habería que recorrer a metade da lista (n/2), pero o 2 simplifícase xa que é unha constante.


**6.(1.25 puntos) Analiza brevemente la búsqueda secuencial y la búsqueda binaria. Explica** 
	**i) qué tipo de técnicas algorítmicas son,** 
	**ii) que complejidad inferior y superior tienen,** 
	**iii) qué prerrequisitos tienen para ser utilizadas,** 
	**iv) cuál utilizarías para poner en un sistema en producción y** 
	**v) indica también si la mejor solución que propones sería siempre la más rápida.** 
**(0.25 por una explicación correcta de cada uno de estos aspectos).**
i) A búsqueda secuencial é un algoritmo de forza bruta, xa uqe segue a forma máis obvia de solucionar o problema, mentres que a búsqueda binaria é un algoritmo que segue o esquema divide e vencerás, polo que o que fai e ir dividindo o espazo de solucións en subconxuntos máis pequenos de datos ata atopar o que busca.

ii) Búsqueda secuencial: orde de complexidade inferior (no mellor dos casos): $O(1)$
Búsqueda secuencial: orde de complexidade superior (no peor dos casos): $O(n)$
Bisqueda binaria: orde de complexidade inferior (no mellor dos casos): $O(1)$
Bisqueda binaria: orde de complexidade superior (no peor dos casos): $O(\log n)$

iii) A busqueda secuencial, ningún; e a búsqueda binaria, que o conxunto de elementos esté ordenado e que se poida acceder aos datos mediante un índice.

iv) Se os datos están ordenados, a binaria, e se mom están ordenados, a secuencial

v) A busqeda binaria non é sempre a máis rápida, xa que para tallas pequenas pode ser mellor a búsqueda secuencial, ou se haiq ue ordenar a lista de elementos, pode non compensar debido ao custo de ordenamento.

