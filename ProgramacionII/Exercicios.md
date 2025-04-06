# Exercicios Tema5

#### Exercicio 1
Dado este programa, determina a **orde promedia** de complexidade (talla = N):
```c
#define C 1000
void main() {
    int v[N];
    int i, j;
    for (i = 0; i < N; i++) {
        for (j = 0; j < C; j++) {
            v[i] = v[i] + i * j;
        }
    }
}
```
##### Solución:
$T(n)=1000n \subset O(n)$

#### Exercicio 2
Dado este programa, determina a **orde inferior** de complexidade (talla = N):
```c
int f(int lim) {
    int k, rdo = 0;

    for (k = 0; k < lim; k++) {
        rdo += (k * k * k);
    }

    return rdo;
}

void main() {
    int i;
    int v[N];

    for (i = 0; i < N; i++) {
        v[i] = f(N % 100) * i;
    }
}
```
##### Solución:
$Ω(N)$

#### Exercicio 3
¿Qué es el orden superior de complejidad?
- [ ] Ninguna de las otras respuestas es correcta
- [ ] Me informa del comportamiento esperado de los algoritmos para casos de tallas grandes
- [ ]  Me informa del comportamiento esperado de los algoritmos para casos “fáciles” (independientemente de la talla)
- [ ]  QUIERO DEJAR ESTA PREGUNTA SIN CONTESTAR
- [ ]  Me informa de casos promedios
##### Solución:
- [v] Ninguna de las otras respuestas es correcta
- [ ] Me informa del comportamiento esperado de los algoritmos para casos de tallas grandes
- [ ]  Me informa del comportamiento esperado de los algoritmos para casos “fáciles” (independientemente de la talla)
- [ ]  QUIERO DEJAR ESTA PREGUNTA SIN CONTESTAR
- [ ]  Me informa de casos promedios

#### Exercicio 4
En términos de orden superior de complejidad temporal, tenemos un algoritmo sublineal (A), un algoritmo $n^n$ (B), un algoritmo logarítmico (C) y un algoritmo cúbico (D). Todos ellos resuelven un problema de integración.
Uno de los cuatro algoritmos sólo va a ser usable para tallas muy pequeñas, ¿cuál es?  
##### Solución:
B

#### Exercicio 5
En un TAD pila:
- [ ] a. ninguna de las otras respuestas es correcta  
- [ ] b. QUIERO DEJAR ESTA PREGUNTA SIN CONTESTAR  
- [ ] c. no existen operaciones de eliminación  
- [ ] d. la única inserción posible es insertar en el fondo de la pila  
- [ ] e. la única inserción posible es insertar en el tope de la pila
##### Solución:
- [ ] a. ninguna de las otras respuestas es correcta  
- [ ] b. QUIERO DEJAR ESTA PREGUNTA SIN CONTESTAR  
- [ ] c. no existen operaciones de eliminación  
- [ ] d. la única inserción posible es insertar en el fondo de la pila  
- [v] e. la única inserción posible es insertar en el tope de la pila

#### Exercicio 6
Para resolver un determinado problema se dispone de: un algoritmo superlineal, un algoritmo lineal, y un algoritmo logarítmico.
Seleccione una:
- [ ]  QUIERO DEJAR ESTA PREGUNTA SIN CONTESTAR  
- [ ]  ninguna de las otras respuestas es correcta  
- [ ]  en general preferiremos el logarítmico  
- [ ]  en general preferiremos el lineal  
- [ ]  el lineal para todas las posibles tallas del problema va a ser siempre peor que los otros
##### Solución:
- [ ]  QUIERO DEJAR ESTA PREGUNTA SIN CONTESTAR  
- [ ]  ninguna de las otras respuestas es correcta  
- [v]  en general preferiremos el logarítmico  
- [ ]  en general preferiremos el lineal  
- [ ]  el lineal para todas las posibles tallas del problema va a ser siempre peor que los otros

#### Exercicio 7
Tenemos dos algoritmos A y B, en orden superior de complejidad espacial A es lineal y B es factorial, y en terminos de orden superior de complejidad temporal A es cúbico y B es logarítmico, ¿cuál preferimos? 
##### Solución:
A

#### Exercicio 8
Una lista es implementad doblemente enlazada y circular. ¿En cuántos pasos se accede desde el primer elemento hasta el último si la lista tiene 101 elementos?
##### Solución:
1
