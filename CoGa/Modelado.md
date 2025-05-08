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
Consiste na creación ou transformación de figuras xeométricas a partires de **polígonos** (xeralmente triángulos, xa que son a figura xeométrica máis simple que delimita un plano). Para teselar claquera polígono de $n$ lados farán falta $n-2$ _triángulos_.

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
Con `GL_QUAD_STRIP` créase unha tira de **cuadriláterios**, empregando os catro primeiros vértices para o primeiro cadrado (de forma que _aos dous ultimos se lles invirte a orde_ $\to$ V1 V2 V4 V3). A partir de ahí, cada novo cadrado se crea cos dous últimos vértices do anterior cadrado (en orden) e cos dous seguintes vértices (en orde inversa).
![[quad_strip.png| center | 250]]
**[[ExemplosOpenGL#Cuadrilateros| _Ver exemplo de código_]]**

#### Polígonos
Temos as seguintes **restriccións**: 
+ Os polígonos deben ser _convexos sen cavidades_ 
+ Os seus _lados non_ deben _cortarse_.
##### Modos poligonais
Temos a función **`glPolygonMode()`**, que especifica o modo de **rasterización** dos polígonos (especifica como se representan os polígonos, non como se ensamblan): 
`glPolygonMode(GLenum cara, GLenum modo);`
+ `cara` $\to$ especifica a _que cara_ afectan os cambios (`GL_FRONT`, `GL_BACK` ou `GL_FRONT_AND_BACK`)
+ `modo` $\to$ especifica como se _pintan_ as caras (`GL_FILL`, `GL_LINE` ou `GL_POINT`)

### Listas de visualización
Son **indices** que nos permiten gardar un _conxunto de vertices_ mediante un _identificador_ (int único) para despois poder renderizalos invocando dito identificador (índice).

Para **construír** unha lista de visualización:
```c
IndiceLista = glGenLists(1); // Identificador = IndiceLista
glNewList(IndiceLista, GL_COMPILE);

glBegin(GL_QUADS);
	glVertex3f(-1.0f, -1.0f, -1.0f);
	glVertex3f(1.0f, -1.0f, -1.0f);
	glvertex3f(1.0f, -1.0f, 1.0f);
	glVertex3f(-1.0f, -1.0f, 1.0f);
glEndList();
```
\*`glGenLists` é unha lista correlativa de índices vacíos para almacear listas.
\*\*Valores para o segundo arg de `glNewList`: `GL_COMPILE` ou `GL_COMPILE_AND_EXECUTE`.

Para **chamar** a unha lista de visualización:
```c
glCallList(IndiceLista)
```
Cando se chama a unha lista, debuxarase a figura xa especficada (e xa compilada), o que aforra tempo
\*É equivalente ao uso de VAO e VBO con glad (OpenGL 3.3)

### Formas básicas en GLUT (GL Utility Toolkit)
![[formas_basicas_glut.png| center | 350]]

<div style="page-break-after: always;"></div>

## Modelado en OpenGL 3.3 (GLFW/glad)
![[Pasted image 20250305185105.png | center | 500]]
### Tipos de shaders
+ **Vertex shader**:
	+ É unha función que _recibe vértices_ como parámetros e _devolve a súa posición_ como saída.
	+ Manipula vértices en 3D; cor, coordenadas de textura, orientación e tamaño dos vértices.
+ **Fragment shader**:
	+  Recibe a _información_ de cada _fragmento_ (toda a información necesaria para debuxar un píxel) e devolve a _cor de cada píxel_.
	+ Aplícase na última etapa de rasterizado.
_Despois_ do fragment shader realízanse unha serie de _comprobacións_ relativas ao Alpha test (transparencias), Blending test e Depth test (profundidade).
### Envío de vértices
Empréganse **arrays de vértices** (**Vertex Array Objects, VAO**) nos que se almacena _información do obxecto_ (vertices e atributos) a debuxar. Para engadir os atributos ao VAO, empréganse os **VBO** (**Vertex Buffer Objects**)
```c
// Definir os vértices (un triángulo)
float vertices[] = {
    -0.5f, -0.5f, 0.0f, // Vértice 1
     0.5f, -0.5f, 0.0f, // Vértice 2
     0.0f,  0.5f, 0.0f  // Vértice 3
};

// Crear VAO e VBO
unsigned int VAO, VBO;
glGenVertexArrays(1, &VAO);
glGenBuffers(1, &VBO);

// Configurar VAO e VBO
glBindVertexArray(VAO);
glBindBuffer(GL_ARRAY_BUFFER, VBO);
glBufferData(GL_ARRAY_BUFFER, sizeof(vertices), vertices, GL_STATIC_DRAW);

// Especificar cómo interpretar os datos do VBO
glVertexAttribPointer(0, 3, GL_FLOAT, GL_FALSE, 3 * sizeof(float), (void*)0);
glEnableVertexAttribArray(0);

// Desvincular buffers
glBindBuffer(GL_ARRAY_BUFFER, 0);
glBindVertexArray(0);

// Renderizar no bucle de debuxo
glUseProgram(shaderProgram);
glBindVertexArray(VAO);
glDrawArrays(GL_TRIANGLES, 0, 3); // Debuxa o triangulo
glBindVertexArray(0);
```
\*Parámetros de `glVertexAttribPointer(GLuint index, GLint size, GLenum type, GLboolean normalized, GLsizei stride, const void *pointer);`
+ `index` $\to$ índice do atributo no shader (ver abaixo: `layout (location = 0)`)
+ `size` $\to$ número de componentes por vértice (x, y, z)
+ `type` $\to$ tipo de dato
+ `normalized` $\to$ se queremos ou non normalización automática
+ `stride` $\to$ espacio total que ocupa cada vértice no array (neste caso 3 floats, se lle metemos color, serían 6 floats: 3 para o vertice e 3 para a cor)
+ `pointer` $\to$ (offset) posición inicial dos datos (se tivesemos cor, sería de 3 floats, xa que a cor comeza despois dos3 primeiros floats)

<div style="page-break-after: always;"></div>

### Xestión de vértices
Despois de mandar os vértices ao pipe, xestiónanse mediante os **shaders**.
Os shaders están escritos nunha _linguaxe_ parecida a C chamada _GLSL_, dirixida a manipular vectores e matrices. Usualmente teñen esta **estrutura**:
```c
#version version_number
in type in_variable_name;
in type in_variable_name;

out type out_variable_name;
  
uniform type uniform_name;
  
void main()
{
  // process input(s) and do some weird graphics stuff
  ...
  // output processed stuff to output variable
  out_variable_name = weird_stuff_we_processed;
}
```
Específicamente, cando nos referimos a un vertex shader, cada variable de input chámase _vertex attribute_.

Primeiro o **vertex shader** nos devolve a _posición_ dos vértices, e que se almacena como un arquivo de texto no directorio de uso:
```c
#version 330 core
layout (location = 0) in vec3 aPos; // Input = vector (x,y,z) --> aPos
out vec4 vertexColor; // Output
void main() {
	gl_Position = vec4(aPos, 1.0);
    vertexColor = vec4(0.5, 0.0, 0.0, 1.0); // Unha cor calquera
}
```

Despois o **fragment shader** encárgase de calcular a _cor_ de cada pixel:
```c
#version 330 core
in vec4 vertexColor; // the input variable from the vertex shader
out vec4 FragColor;
void main() {
    FragColor = vertexColor;
} 
```

Así, para que o vertex shader e o fragment shader se _comuniquen_ entre si, as variables que outputee o vertex shader deben ser declaradas _exactamente igual_ no fragment shader (mesmos tipo e nome).
#### Atributos do vertex shader
Se queremos declarar **máis atributos para cada vértice**, debemos especificarllo ao vertex shader para que sepa como coller os datos.
Así, se por exemplo temos dous atributos, posición e cor, o noso array de vértices podería ser:
```c
float vertices[] = {
    // positions         // colors
     0.5f, -0.5f, 0.0f,  1.0f, 0.0f, 0.0f,   // bottom right
    -0.5f, -0.5f, 0.0f,  0.0f, 1.0f, 0.0f,   // bottom left
     0.0f,  0.5f, 0.0f,  0.0f, 0.0f, 1.0f    // top 
};    
```

Entón no **vertex shader** temos que engadir o _novo atributo_:
```c
#version 330 core
layout (location = 0) in vec3 aPos;
layout (location = 1) in vec3 aColor; // <-----
  
out vec3 ourColor; // output a color to the fragment shader

void main() {
    gl_Position = vec4(aPos, 1.0);
    ourColor = aColor; 
}    
```

E no **fragment shader**, como lle pasamos a cor, podemos empregala para colorear os vértices:
```c
#version 330 core
out vec4 FragColor;  
in vec3 ourColor;
  
void main() {
    FragColor = vec4(ourColor, 1.0);
}
```

Ademáis, como engadimos outro atributo aos vértices, temos que reconfigurar os `VertexAttribPointer`, xa que agora nos VBO hai a seguinte información:
![[vertexShaderAttrib.png | center | 350]]
Polo tanto, nos quedaría:
```c
// position attribute
glVertexAttribPointer(0, 3, GL_FLOAT, GL_FALSE, 6 * sizeof(float), (void*)0);
glEnableVertexAttribArray(0);
// color attribute
glVertexAttribPointer(1, 3, GL_FLOAT, GL_FALSE, 6 * sizeof(float), (void*)(3* sizeof(float)));
glEnableVertexAttribArray(1);
```

Así obteríamos a seguinte imaxe:
![[exemploOutput.png| center | 250]]
### Como se meten os shaders no programa?
Primeiro **envíanse** e **compílanse** os shades:
1. `glCreateShader()` $\rightarrow$ _crea_ un obxeto _shader_ do tipo corresponednte baleiro
2. `glShaderSource(shader, fonteGLSL)` $\rightarrow$ _méteselle_ o texto do shader (codigo) no obxeto
3. `glCompileShader(shader)` $\rightarrow$ o driver da GPU lee o código e o _compila_

Despois de compilar ambos shaders:
1. `glCreateProgram()` $\rightarrow$ _crea_ un obxeto _program_ para conter os shaders
2. `glAttachShader(program, tipoShader)` $\rightarrow$ _adxunta_ os shaders compilados ao program
3. `glLinkProgram(program)` $\rightarrow$ linkea o programa
4. `glUseProgram(program)` $\rightarrow$ activa o programa para que OpenGL o empregue