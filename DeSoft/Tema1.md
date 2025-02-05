# Tema 1. Introducción ao UML
A **UML (Unified Modeling Language)** é unha _linguaxe gráfica_ de modelado para visualizar, especificar, construir e documentar software; e proporciona o estándar para escribir os planos dun sistema.
**Orixe**: apareceu como _fusión de 3 linguaxes_, grazas á alianza de _Booch_, _Jacobson_ e _Rumbaugh_ na compañía _Rational Software_ nos anos 90.

### Concepto de modelo
Un **modelo** é unha _simplificación da realidade_ que proporciona planos dun sistema, permitindo un certo _nivel de abstracción_. Hai dous tipos:
+ **Estruturais**: organización
+ **De comportamento**: dinámica

### Para qué se modela?
Modélase para _producir software de calidade_ de maneira rápida, _consistente_ e predecible; que cumpla o seu propósito. 

### Cando se modela?
Principalmente cando o sistema é _grande e complexo_, xa que **o modelado reduce o problema** (séguese a mentalidade de Dijkstra de divide e vencerás).

### Principios de modelado
1. A _elección_ dos _modelos_ inflúe en como se chega á _solución_ do problema
2. Os modelos pódense expresar en diferentes _niveis de abstracción_
3. Os mellores modelos _non simplifican ningún detalle importante_ da realidade a modelar
4. Fai falta _máis de un modelo_ para un sistema non trivial

### Construcción de software
+ **Enfoque algorítmico**: constrúese o modelo mediante _procedemntos ou funcións_, o que o fai _dificil de manter_.
+ **Enfoque orientado a obxetos**: o modelo constrúese con _clases e obxetos_, o que o fai válido para toda clase de _dominios e complexidades_.

### Exemplo sinxelo
```java
import java.awt.Graphics; 
class HolaMundo extends java.applet.Applet { 
	public void paint (Graphics g) { 
		g.drawString("¡Hola, Mundo!", 10, 10); 
	}
}
```
![[DiagramaExemplo.png | center | 250]]
Ademáis deste diagrama de clases, pódense facer diagramas da xerarquía de clases, da interacción entre obxetos...

### Visión xeral de UML
Aporta un _vocabulario_ e unhas _regras_ para crear e ler modelos ben formados.

UML ten os sequientes **bloques de constrcción**:
+ _Elementos_ (abstraccións)
+ _Relacións_ entre elementos
+ _Diagramas_ dos elementos e as súas relacións

Tamén ten unha serie de **regras**:
+ Os bloques de construcción non se poden combinar de calquera forma
+ Para xerar modelos ben formados é necesario seguir as regras semánticas de UML


