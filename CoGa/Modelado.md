# Modelado obxetos 3D
## Obxetos sólidos
Modelar un obxeto é **darlle forma**. Pode ser interior ou exterior:
+ **Interior**: _spatial-partition_, onde se describe o interior do obxecto dividíndoo en pequenas rexións sólidas que non se solapan entre si; polo xeral, cubos (_marching-cubes_).
+ **Exterior**: falamos dunha _representación de superficies_, que define o contorno do obxecto sen facer referencia ao seu volume interior. Proporciona información sobre:
	- A _superficie_: debe ter en conta a súa xeometría.
	- _Propiedades visuais_: cómo se comporta frente á luz, cor, textura.
	- Ao mellor algunha _propiedade física_: elasticiade,... (se se vai efectuar unha simulación).

## Primitivas gráficas
Son as funcións dun paquete gráfico que se empregan para **describir os distintos compoñentes dunha imaxe**. Se describen a **xeometría** dos obxetos, se lles chama **primitivas xeométricas**. Despois de especificar a xeometría dunha imaxe dentro dun sistema de coordenadas determinado, as primitivas se _proxectan_ sobre un _plano bidimensional_ (área de visualización do dispositivo de saída).
### Sistemas de coordenadas de referencia
O **sistema de coordenadas**, que pode ser bidimensional ou tridimensional, serve para definir os obxetos dunha imaxe, mediante conxuntos de posicións dos seus vértices. Estas coordenadas almacenananse na descrición da escena, xunto con outra información sobre os obxetos como a súa cor e a súa _extensión de coordenadas_ (valores x, y e z mínimos e máximos para cada obxeto). Despois os obxetos pasan por rutinas que determinan as superficies visibles e asignan os obxetos á súa posición correspondente no dispositivo de saída. Neste dispositivo de saída, as ubicacións se describen mediante **coordenadas de pantalla** (números enteiros que se corresponden coas posicións de pixel dentro do búfer de imaxe).
### Primitivas en OpenGL
As primitivas máis bśicas en OpenGL son os puntos, as liñas e os triángulos:
![[primitivas1.png | center | 500]]

### Triangulación/Tessellation
Consiste en dividir obxetos en _triángulos_ para o seu renderizado. Para teselar claquera polígono de $n$ lados farán falta $n-2$ triángulos.

### Identificación de caras
A **normal** da cara ven determinada pola _orde na que se especifican os vértices_. Para estimar a normal dunha cara podemos empregar a _regra da man dereita_ ou ben mirar a _dirección_ na que se debuxan as arestas:
![[identificar_caras.png| center | 300]]
Temos que para un punto $P(x,y,z)$:
+ $Ax+By+Cz+D = 0 \to$ na superficie
+ $Ax+By+Cz+D < 0 \to$ dentro do obxeto (por detrás)
+ $Ax+By+Cz+D > 0 \to$ fora do obxeto (diante)

Identificar a normal dunha cara é importante debido á **ocultación de caras**, que consiste en que a normal que non mira á cámara _non se ve_ para evitar un gasto de recursos innecesario. En OpenGL actívase con `glEnable(GL_CULL_FACE)`. 

Para facilitar o traballo, podemos activar a **normalización** en OpenGL  con `glEnable(GL_NORMALIZE)`, que calcula as normales de forma correcta e despois normaliza.
\*Podemos cambiar cómo queremos que calcule a normal (se queremos que a cara frontal sexa a que ten os vertices en sentido horario ou antihorario) con `glFrontFace(GL_CW)` e `glFrontFace(GL_CCW)`.

Para a **iluminación**, asígnanselle aos polígonos _unha normal por cara_, que se calcula a partir dos vértices, e se lle asigna unha cor, o que proporciona unha **iluminación plana** (`glShadeModel(GL_FLAT)`). Para _suavizar a iluminación_ en superficies, pódeselle asignar _unha normal a cada vértice_ para promediar os valores de cor en toda a superficie do obxeto.

### Representación superficie
Para a representación dun obxecto en base a polígonos necesitamos coñecer, representar e almacear os seus vértices e atributos. Para esto empréganse **táboas**:
![[taboas.png| center]]
![[taboas_indexadas.png| center ]]

## Modelado en OpenGL 1.2 (glut)
### Primitivas
Unha **primitiva** é a interpretación dun _conxunto de vétices_ debuxados dunha maneira específica na pantalla. En OpenGL hai 10 distintas e se empregan mediante `glBegin([Macro])` e `glEnd()`.
![[taboa_primitivas_glut.png| center | 350]]
#### O punto
Os **vértices** ou puntos son o **denominador común** das primitivas de OpenGL. Defínense coa función `glVertex()` e poden tomar de 2 a 4 parámetros de calquera tipo numérico. O _tamaño_ dos puntos pode especificarse con `glPointSize(GLfloat ancho)`.
![[punto_glut.png| center | 250]]
\*Programa que debuxa un punto no $(0.5, 0.5, 0.5)$ (o PointSize tense que esfecificar fora do Begin)
#### A liña
Se a debuxamos con `GL_LINES`, os puntos cóntanse por **parellas** (se especificamos un número impar de puntos o derradeiro ignórase), e se a debuxamos con `GL_LINE_STRIP` ou `GL_LINE_LOOP`, os vértices **encadénanse** (co loop, o derradeiro vértice e o primeiro péchanse).
![[line_strip_codigo.png | 250]]     ![[line_strip_output.png | 135]]
![[line_loop_codigo.png| 250]]     ![[line_loop_output.png| 135]]
#### Triángulos
Para crear **obxectos sólidos** precisamos primitivas que sexan _superficies pechadas_ para que, en conxunto, formen o elemento desexado. Normalmente empréganse **triángulos**, xa que ==son o mínimo elemento que nos permite xerar un plano==.
Con OpenGL podemos xerar triángulos de varias **formas**: 
+ `GL_TRIANGLES`: cada 3 vértices conforman un triángulo (ollo coas normais!)
+ `GL_TRIANGLE_STRIP`: os 3 primeiros vértices forman un triángulo na orde dada.  A partír de ahí, _cada novo vértice xera un novo triángulo_ coa seguinte peculiaridade: se o seguinte vértice é _par_ na secuencia, invírtese a orde dos dous primeiros vértices que conformarán o novo triángulo (se é impar, quédanse como están).
+ `GL_TRIANGLE_FAN`: Cada triángulo está formado polo _primeiro vértice_ dado na lista (que actúa como centro do abanico), o _último vértice_ do triángulo anterior, e o seguinte vértice da lista que índa _non foi empregado_.
![[triangulos_3formas.png| center | 600]]
**[[ExemplosOpenGL#Triangulos| _Ver exemplos de código_]]**

#### Cuadriláteros

