# Visión 3D e 3D
## Proxeccións
Unha proxección é unha **transformación dun espazo** de n dimensións noutro de _menor dimensión_. Ten os seguintes **elementos**:
+ _Obxectos_ a proxectar (escena).
+ _Plano de proxección_: a superficie 2D na uqe e representan os obxectos 3D.
+ _Punto de vista_ ou _centro de proxección CP_ (ubicación da cámara).

A proxección realízase a través duns _raios imaxinarios_ denominados **proxectores**, que _se trazan dende o centro de proxección ata os puntos do obxecto_. Estes raios intersecan o plano de proxección determinando onde se debuxará cada punto da imxe final.
![[Pasted image 20250509150817.png | center | 500]]
### Tipos de proxeccións
+ **Proxección paralela** (PP, _Ortográfica_): o _CP está no infinito_, polo que os _proxectores son paralelos_ entre si. Defínese unha _dirección de proxección_ (DP) en vez de un centro de proxección.
+ **Proxección perspectiva**: existe un _CP no espazo_, no que se cortan os proxectores. Emprégase para crear _respresentacións máis realistas_ de obxectos tridimensionais. 
![[tiposProxecccions.png| center | 400]]

<div style="page-break-after: always;"></div>

### Clasificación de proxeccións
![[clasifProxec.png| center | 500]]

### Proxección paralela: multivista ortográfica
O plano de proxección é **perpendicular** a algún eixo. Necesitamos **varias vistas** para ter unha visión 3D do
obxecto:
![[Pasted image 20250509152211.png | center | 350]]
**Vantaxes**:
- É posible realizar _medidas precisas_.
- Todas as vistas teñen a _mesma escala_.
**Inconvintes**:
- Non obtemos unha visión 3D do obxecto.

<div style="page-break-after: always;"></div>

### Proxección perspectiva
![[proxeccionPerspectiva.png| center | 350]]
**Vantaxes**:
- Proporcionan _realismo visual_.
- Sensación _tridimensional_.
**Inconvintes**:
- _Non manteñen a forma_.
- _Non manteñen a escala_.

## OpenGL 1.2
Todo o traballo sobe a cámara (determinación do frustrum, viewMatrix e projection Matrix)
resúmese nunha soa matriz: a **Matriz de Proxección**:
```c
glMatrixMode(GL_PROJECTION);
glLoadIdentity();
```
\* Sobre esta matriz só se deben realizar `glOrtho()` ou `gluPerspective()`.
### Onde mira a cámara
Con `gluLookAt()`, que se aplica sobre a View Matrix (`GL_MODELVIEW`) determínase a **onde e cómo está disposta a cámara**, que non ten nada que ver co tipo de proxección.
```c
gluLookAt(eyeX, eyeY, eyeZ, atX, atY, atZ, upX, upY, upZ);
```
O **eye** especifica a posición do _punto de vista_. Por defecto (0, 0, 0).
O **at** ou center especifica a posición do punto a _onde queremos que mire_ a cámara. Por defecto (0, 0, -1).
O vector **up** espeficica a _orientación_ da cámara. Por defecto (0, 1, 0).
![[camaraLoojAt.png| center | 150]]


### Proxección ortográfica
Emprégase `glOrtho3D()`, que se aplica sobre a _matriz de proxección_, describe unha transformación que produce unha **proxección paralela**.
```c
glOrtho3D(left, right, top, down, near, far);
```
![[proxeccionOrtografica.png| center | 300]]

Por defecto, `glOrtho3D(-1, -1, 1, -1, -1, 1);`

### Proxección perspectiva
Emprégase `gluPerspective()`, que escribe unha transformación que produce unha proxección perspectiva.
```c
gluPerspective(fovy, aspect, near, far);
```
![[Pasted image 20250509154512.png | center | 400]]

<div style="page-break-after: always;"></div>

### Modelo de cámara sintética
A **razón de aspecto** é a _relación entre o ancho e o alto_ (1:1, 4:3, 16:9).
O **FOV** vai dende 40º a 179º, e é equivalente a introducir _lentes de campo_ na cámara.
![[modeloCamaraSintetica.png| center | 300]]

> [!Exemplo]
> ```c
> void myCamara(){
>	glMatrixMode(GL_MODELVIEW);
>	gluLookAt(0, 0, 1, 0, 0, -1, 0, 1, 0);
>	glMatrixMode(GL_PROJECTION);
>	gluPerspective(45, WIDTH/HEIGHT, 0.1f, 100.0f);
> }
> ```

## OpenGL 3.3
Debemos crear **unha matriz para cada un dos pasos do pipeline** de forma que quede algo como:
$$
V_{clipPosition}=M_{projection}\times M_{view}\times M_{model}\times vertices
$$
A saída de $V_{clipPosition}$ será a posición final dos vértices na pantalla, e debe ser aplicada a `glPosition()` no _vertex shader_.
### Funcións
Son as mesmas que en OpenGL 1.2, pero agora a información da cámara ten unha _matriz separada_ (View Matrix).
Calculamos a $M_{projection}$ para volume otrográfico ou en pespectiva (`ortho` ou `perspective`):
```c
glm::mat4 proj = glm::perspective(45.0f, witdh/height, 0.1f, 100.0f);
```

Calculamos a $M_{view}$ coa matriz do modelo:
```c
glm::mat4 view;
view = glm::lookAt(glm::vec3(x,y,z), glm::vec3(x,y,z), glm::vec3(x,y,z));
```

Unha vez temos as matrices, mandámosllas ao vertex shader:
```c
unsigned int modelLoc = glGetUniformLocation(shaderProgram, "model");
glGetUniformMatrix4fv(modelLoc, 1, GL_FALSE, glm::value_ptr(model));

unsigned int viewLoc = glGetUniformLocation(shaderProgram, "view");
glGetUniformMatrix4fv(viewLoc, 1, GL_FALSE, glm::value_ptr(view));

unsigned int projectionLoc = glGetUniformLocation(shaderProgram, "projection");
glGetUniformMatrix4fv(projectionLoc, 1, GL_FALSE, glm::value_ptr(projection));
```

No vertex shader:
```c
#version 330 core
layout (location = 0) in vec3 aPos;

uniform mat4 model;
uniform mat4 view;
uniform mat4 projection;

void main(){
	gl_Position = projection * view * model * vec4(aPos, 1.0f);
}
```

## Matriz de proxección ortográfica
`glOrtho(Left, Right, Top, Bottom, Near, Far);`
$$
\begin{bmatrix}
\frac{2}{R - L} & 0 & 0 & -\frac{R + L}{R - L} \\
0 & \frac{2}{T - B} & 0 & -\frac{T + B}{T - B} \\
0 & 0 & \frac{2}{F - N} & -\frac{F + N}{F - N} \\
0 & 0 & 0 & 1
\end{bmatrix}
$$

## Matriz de proxección en pespectiva
`gluPerspective(fov, Aspect, Near, Far);` $\rightarrow$ hai que pasar o fov a radiáns
$$
\begin{bmatrix}
\frac{1}{\text{A} \cdot \tan(\text{fov}/2)} & 0 & 0 & 0 \\
0 & \frac{1}{\tan(\text{fov}/2)} & 0 & 0 \\
0 & 0 & \frac{N + F}{N - F} & \frac{2FN}{N - F} \\
0 & 0 & -1 & 0
\end{bmatrix}
$$
> Regra de ouro: Se os _graos_ se empregan nas _funcións trigonométricas_, hai que pasalos a **radiáns**.


