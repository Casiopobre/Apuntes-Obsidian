# Tema 5. Análise de algoritmos: complexidade computacional
## Introducción
Dende un punto de vista computacional, _a calidade dun programa depende da eficiencia do mesmo_. Para determinar isto, emprégase a **análise de algoritmos**, que se pode realizar de _dúas formas_ distintas:
+ _Experimental_: da resultados máis "reais" pero é máis costosa e depende da máquina.
+ _Teórica_: non depende da máquina e é máis flexible pero da resultados non "exactos".

## Consumo de recursos
A **eficiencia** é a capacidade de resolver un problema empregando os _mínimos recursos computacionais_, e ten dous _factores_ fundamentais:
+ **Custo espacial**: cantidade de memoria requerida
+ **Custo temporal**: tempo necesario
Moitas veces, un bo algoritmo é o que resolve un problema cun bo compromiso tempo-memoria.
## Caracterización dos custos
O _problema concreto_ denomínase **instancia** do problema xeral, e o número enteiro que mide a _envergadura_ desa instancia denomínase **talla**.

> [!Exemplo]
> Dados estes 3 programas A1, A2, A3, que calculan $n^2$ de tres formas distintas (producto, suma iterativa e suma de sucesores), os custos temporais para un problema de talla $n$ dependen do tempo que tarden as operacions de multiplicación, suma e sucesor:
> $T_{A_{1}}=t_{producto}$
> $T_{A_{2}}=t_{suma} \cdot n$
> $T_{A_{3}}=t_{sucesor} \cdot n \cdot n$
```c
void main() { // A1
	int n, m;
	scanf("%d", &n);
	m = n * n; // <---- producto
	printf("%d\n", m);
}

void main(){ // A2
	int n, m=0;
	scanf("%d", &n);
	for (int i=1; i<=n; i++)
		m = m + n; // <---- suma
	printf("%d\n", m);
}

void main(){ // A3
	int n, m=0;
	scanf("%d", &n);
		for (int i=1; i<=n; i++)
			for (int j=1; j<=n; j++)
				m++; // <---- sucesor
	printf("%d\n", m);
}
```

No exemplo anterior, inda que pareza que o programa A1 é o mellor e A3 o peor, esto non ten por qué manterse. Por exemplo, se facemos que a multiplicación sexa moito máis costosa que a suma, xa non vemos un ganador tan claro:
![[exemploTallas.png| center | 400]]
Polo tanto, **para cada talla hai un mellor programa**.

Así, para realizar unha _boa caracterización computacional_ dun programa é importante **analizar o custo do mesmo a medida que aumenta a talla** ($n$), sobre todo para valores de $n$ **moi grandes** (cuto asintótico), xa que é o que determina realmente a súa eficincia. Ademáis, analizar só tallas grandes permite facer _aproximacións_ que _simplifican_ considerablemente a análise do custo.

### Paso
Un **paso** é unha _operación individual_ que ten un _custo asociado_, que non depende da talla do problema ou que está acotado por algunha constante (como por exemplo, operacións aritméticas, asignación de valores, comparación...). Así, podemos medir o _custo computacional_ dun programa según o _número de pasos_ en función da talla. 

> [!Exemplo] 
> Para os códigos anteriores A1, A2 e A3, agora podemos calcular o número de pasos:
> $T_{A_{1}}(n)=1$
> $T_{A_{2}}(n)=n$
> $T_{A_{3}}(n)=n \cdot n$

**[[Exemplos#Exemplos Pasos|CLICK PARA VER MÁIS EXEMPLOS]]**

## Cotas de custo
Moitas veces, **o custo non só depende da talla**, por exemplo, nun algoritmo de búsqueda lineal o custo depende do contido do vector e do vaor concreto do elemento a buscar.
Por esto, ao analizar o custo computacional dun algoritmo, calculamos a **cota inferior** (_mellor caso_) e a **cota superior** (_peor caso_) e, con estas, o **custo promedio**.

> [!Exemplo]
> Por exemplo, nun algoritmo de **búsqueda lineal**:
> $T_{inferior}(n)=1\to$ O elemento buscado é o primeiro
> $T_{superior}(n)=n\to$ O elemento buscado é o último
> $T_{promedio}(n)={n}/{2}$

## Resumo de determinación de custos computacionais
+ O custo débese representar sempre en _función da talla_ do problema
+ A comparación de custos debe ser _insensible a "constantes de implementación"_ (como por exemplo, tempos concretos de execucion de operacións individuais). Esto consíguese considerando únicamente o comportamento _asintótico_ da función de custo (_tallas grandes_).
+ Cando o custo _non_ se pode expresar apropiadamente _en función da talla_ debemos determinar _cotas superiores e inferiores_ (_peor e mellor caso_).

## Notación asintótica
Sexa $f : \mathbb{N} \to \mathbb{R}^+$ unha función dos naturais nos reais positivos. Defínese:
$O(f(n)) = \{t : \mathbb{N} \to \mathbb{R}^+ \mid (\exists c \in \mathbb{R}^+, \exists n_0 \in \mathbb{N}), \forall n \geq n_0, t(n) \leq c f(n)\}$
$\Omega(f(n)) = \{t : \mathbb{N} \to \mathbb{R}^+ \mid (\exists c \in \mathbb{R}^+, \exists n_0 \in \mathbb{N}), \forall n \geq n_0, t(n) \geq c f(n)\}$
$\theta(f(n)) = O(f(n)) \cap \Omega(f(n))$

A partires da definición de $\theta(f(n))$ tense:
$\theta(f(n)) = \{t : \mathbb{N} \to \mathbb{R}^+ \mid (\exists c_1, c_2 \in \mathbb{R}^+, \exists n_0 \in \mathbb{N}), \forall n \geq n_0, c_1 f(n) \leq t(n) \leq c_2 f(n)\}$

### ==Xerarquía de custos computacionais==
$$
O(1) \subset O(\log n) \subset O(\sqrt{n}) \subset O(n) \subset O(n \log n) \subset O(n^2) \subset O(n^3) \subset O(2^n) \subset O(n^n)
$$

[[Exercicios#Exercicios Tema5| VER EXERCICIOS RESOLTOS]]




