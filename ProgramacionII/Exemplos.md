## Exemplos Pasos

> [!Exemplo 1]
> Para este programa que imprime os elementos dun vector en orde inverso, podemso definir o paso como cada acceso a v. Polo tanto, para unha talla $n$, o custo será: $T(n)=n+n=2n$ pasos, polo que $T(n)\in\mathcal{O}(n)$
```c
#define N 1000000
int main() {
	int count = 0, x, v[N];
	printf("Teclear datos (fin: ^D)\n");
	while ((count < N) && (scanf("%d", &x) == 1)) { // Lee datos (N veces)
		v[count] = x;
		count++;
	}

	for (int i = count-1; i >= 0; i--) // Imprime os datos ao reves (N)
		printf("%d ", v[i]);
	printf("\n");
	
	return 0;
}
```

> [!Exemplo 2]
> Para este programa de búsqueda do elemento máis cercano á media, se definimos un paso como un acceso a A, entón para unha talla $n$, $T(n)=n+n+2n+1=4n+1\in\mathcal{O}(n)$
```c
#define N 100000
int main() {
	int cercano = 0, n = 0, aux, A[N];
	double media, suma = 0;
	while (n < N && scanf("%d", &aux) == 1) { // N
		A[n] = aux;
		n++;
	}
	for (int i = 0; i < n; i++) // N
		suma += A[i];
	media = suma/n;

	for (i = 1; i < n; i++)
		if (fabs(A[i]-media) < fabs(A[cercano]-media)) // 2N
			cercano = i;
	
	printf("El más cercano es A[%d]=%d\n", cercano, A[cercano]);

	return 0;
}
```

>[!Exemplo 3: Cálculo (ineficiente) da moda dunha serie de idades]
>Paso = comparacións en if
>$T(n)=n^2+2n\in\mathcal{O}(n^2)\to$ Orden cuadrático
```c
#define N 100000
#define MAXEDAD 150
int main() {
	int n = 0, edad, frec, maxFrec = 0, moda, edades[N];
	printf("Teclear edades, finalizando con ^D\n");
	while ((n < N) && (scanf("%d", &edad) != EOF)) // N ifs
		if ((edad >= 0) && (edad < MAXEDAD)) {
			edades[n] = edad;
			n++;
		}
	
	for (int i = 0; i < n; i++) {
		frec = 0;
		for (int j = 0; j < n; j++)
			if (edades[i] == edades[j]) // N²
				frec++;
		if (frec > maxFrec) { // N
			maxFrec = frec;
			moda = edades[i];
		}
	}
	printf("Leidos %d datos; Moda=%d (frecuencia=%d)\n", n, moda, maxFrec);
	return 0;
}
```

>[!Exemplo 4: Cálculo (eficiente) da moda dunha serie de idades]
>Paso = comparacións en if
>$T(n)=2n+150\in\mathcal{O}(n)\to$ Orden lineal
```c
#define MAXEDAD 150
int main() {
	int n = 0, maxFrec = 0, moda, frecs[MAXEDAD];
	
	for (int edad = 0; edad < MAXEDAD; edad++)
		frecs[edad] = 0;
		
	printf("Teclear edades, fin con ^D\n");
	
	while (scanf("%d", &edad) != EOF)
		if ((edad >= 0) && (edad < MAXEDAD)) { // 2N
			n++; 
			frecs[edad]++;
		}
		
	for (int edad = 0; edad < MAXEDAD; edad++)
		if (frecs[edad] > maxFrec) {
			maxFrec = frecs[edad]; 
			moda = edad;
		}
	printf("Leidos %d datos; Moda=%d (frecuencia=%d)\n", n,moda,maxFrec);
}
```

>[!Exemplo 5: Funcións extrañas]
>Paso: +=
>$T(n)=(n+1)n+n=n^2+2n\in\mathcal{O}(n^2)\to$ Orden cuadrático
```c
int f1(int x, int n) {
	for (int i = 1; i <= n; i++) // n pasos
		x += i;
	return x;
}

int f2(int x, int n) {
	for (int i = 1; i <= n; i++) 
		x += f1(i, n); // (n+1) de f2 por n de f1 cada vez que se chama
	return x;
}

int main() {
	int a, n;
	scanf("%d", &n);
	a = f2(0, n); // chamase a f2 = (n+1)*n
	printf("%d\n", f1(a, n)); // chamase a f1 = n
	return 0;
}
```


