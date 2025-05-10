# Iluminación
## Introducción
A **luz** é unha _onda electromagnética_ que é percibida polo ser humano e que ten unha cor determinada pola súa frecuencia ou suma de frecuencias do espectro.
No **ollo humano** temos dous tipos fundamentais de **detectores**:
+ Os _bastóns_ empréganse na visión nocturna e responden á luminosidade, polo que non participan na distinción entre cores.
+ Os _conos_ poden ser de tres tipos (vermello, verde e azul) e responden con diferente sensibilidade ás bandas de radiación.
![[espectroOlloHumano.png| center | 200]]

Espectro da intensidade luminosa: $I(\lambda)=R.r(\lambda)+V.v(\lambda)+A.a(\lambda)$

As **cores aditivas** son as que se forman mediante a combinación de luces. As **cores primarias** son o vermello, o verde e o azul (_RGB_), xa que a partires delas podemos obter calquera outra luz visible.
![[interploacionCor.png| center | 550]]
## Modelo de iluminación
Hai **dous tipos** principais de modelos de iluminación:
+ **Locais**: empréganse en aplicacións onde prima a _velocidade de renedrizado_. A máis clásica é o _modelo de Phong_.
+ **Globais**: Consideran a escena en conxunto. Adoitan ser _lentos_, pero os seus resultados son moito mellores (_prima a calidade_). Os máis coñecidos son os de radiosidade e _trazado de raios_.

<div style="page-break-after: always;"></div>

### Modelo local de iluminación
Emprega un modelo de iluminación local no que os cálculos se realizan en función de:

**Fontes de luz**:
+ _Posición_: Localizada ou infinitamente alonxada.
+ _Intensidade e cor da fonte_.
+ _Número de luces_.
+ _Distribución lumínica_: uniforme, focalizada, direccional, etc.
+ _Xeometría_: puntual, esférica, lineal, etc.

**Obxectos**:
+ _Distancias_ ao observador e á fonte de luz.
+ _Material/Propiedades ópticas_: transparencia, refracción, etc.
+ _Cromaticidade_: Color superficial propio.
+ _Interacción lumínica_ con outros obxectos da escena.

**Observador**:
+ _Dirección_ de observación: cálculo da _intensidade_.

#### Modelo de iluminación simple
**Simplificacións iniciais**: fontes puntuais, obxectos opacos, sen inter-reflexións.
$$
I=f(p, pv, \{MGMO\}, \{MGCL\})
$$
#### ==Modelo de Phong==
É un **modelo empírico** de ilumnación local.
**Simplificacións**: fontes puntuais, os obxectos (non fontes) non emiten a súa propia luz.

##### Compoñentes da luz
**Luz ambiental**
Luz _non direccional_ nin identificable (incide sobre todas as partes do obxecto por igual). Só _depende da súa cor e do obxecto_, e produce _cambios uniformes_ na iluminación dos obxectos (non xera sombras nin matices de cor, polo que postra os obxectos como siluetas). Ven moldeada pola seguinte expresión:
$$
I=I_{a} \cdot K_{a}
$$
$I_{a} \rightarrow$ intensidade ambiental en todo punto do espazo
$K_{a} \rightarrow$ coeficiente de reflexión ambiental [0, 1] de cada obxecto

![[luzAmbiental.png| center | 150]]


**Luz difusa (Lambert)**
Asociada a un _foco de luz_ e reflexada en tódalas direccións segundo a _lei de Lambert_. A variación na iluminación depende dos _cambios de intensidade ou posición_ do foco. Xera _sombras e matices_ de cor en función do _ángulo de incidencia_.
> _Lei de Lambert_: "A compoñente difusa da luz reflexada por unha superficie é proporcional ao coseno do ángulo de incidencia coa normal á superficie."

$$
I = I_L \cdot K_d \cos(\theta) = I_L \cdot K_d \left( \vec{N} \cdot \vec{L}\right)
$$
Onde:
$I_{L} \rightarrow$ _intensidade_ da fonte
$\theta \rightarrow$ ángulo de _incidencia_ [0º, 90º]
$K_{d} \rightarrow$ coeficiente de _reflexión_ para a luz difusa [0, 1]
$\vec{N} \rightarrow$ _normal_ á superficie (unitario)
$\vec{L} \rightarrow$ vector de _iluminación_ (unitario) 

Para que a ecuación de _Lambert se cumpla_, o **vector normal** á cara do obxecto debe estar **normalizado**.
![[lambertEx.png| center | 400]]

<div style="page-break-after: always;"></div>


**Luz especular ou de Phong** 
Asociada a un _foco intenso_ de luz. Asóciase a _obxectos con brillo_ (o caso extremo é un espello). Depende da _intensidade_, a _posición_ do foco e _punto de vista_ (a posición do observador). As **superficies** poden ser _perfectas_ (cando o raio incidente e o reflexado son coplanarios) ou _imperfectas_ ( a luz é reflexada dentro dun patrón tridimensional, é decir, hai luz fóra do plano e o ángulo ideal de reflexión).
$$
I = I_L \cdot K_S \cos^n(\alpha) = I_L \cdot K_S \left( \vec{R} \cdot \vec{V} \right)^n
$$
Onde:
$\alpha \rightarrow$ _ángulo_ entre $\vec{R}$ e $\vec{V}$
$K_{S} \rightarrow$ coeficiente de _reflexión especular_ dependendo do material do obxecto [0, 1]
$n \rightarrow$ coeficiente de _especularidade_ ou concentración do brillo
![[luzEspecular.png| center | 450]]

##### Modelo global
Así obtemos o **modelo global** ao ter en conta as tres iluminacións:
$$
I = I_a \cdot K_a + I_L \cdot \left( K_d \cdot \left( \vec{N} \cdot \vec{L} \right) + K_S \cdot \left( \vec{R} \cdot \vec{V} \right)^n \right)
$$
![[phongGlobal.png| center | 500]]

##### Outros efectos
**Atenuación**: Non desestimar a atenuación da luz coa distancia ao observador.
**Múltiples fontes**: Para ter varias fontes en conta só hai que sumar os seus efectos.
**Cor**: O estudo é monocromo. Ter en conta as tres cores implicará triplicar as expresións (R,G,B)
##### Resumo
![[taboaResumoIluminacion.png| center | 300]]

## OpenGL 1.2
Para **iluminar unha escena** en OpenGL fan falta varios **procesos**, que se realizan mediante a función `glEnable()`:
1. _Iluminar a escena_.
2. Indicar como é o _modelo de iluminación_.
3. Indicar as _propiedades dos materiais_.

### 1. Iluminar a escena
```c
glEnable(GL_DEPTH_TEST); // Habilita o buffer de profundidade.
glEnable(GL_CULL_FACE); // Cálculos para as caras visibles.

glEnable(GL_LIGHTING); // Habilita os cálculos de iluminación.
glEnable(GL_LIGHTx); // Habilita unha luz concreta (x = {0, 1}).

glEnable(GL_COLOR_MATERIAL); // Activa o seguimento da cor.
glColorMaterial(face, Mode); // Indica a cara que se vai seguir
							 //e para que luces se leva a cabo.Cada obx.
							 // reflexará estas luces segundo a súa cor.
glShadeModel(MODEL); // Habilita o modelo de sombreado: GL_FLAT ou GL_SMOOTH
```
Normalmente fanse antes do `glutMainLoop()`. O `myInit()` é un bo lugar.
### 2. Modelo de iluminación
En OpenGL existen tres **tipos fundamentais de luces**: luces básicas _ambiente_, luces _direccionais_, luces _locais_, _focos_.
#### Luz básica
Modelo de **iluminación xeral** (sen por luces) que definirá unha _luz ambiente básica_ e especificará cómo se realiza a iluminación da escena en tanto a se as luces afectan ás dúas partes dun obxecto. Por defecto ten estas **propiedades**:
```c
glLightModelfv(GL_LIGHT_MODEL_AMBIENT, ambiente);
glLightModelf(GL_LIGHT_MODEL_TWO_SIDE, TRUE);
glLightModelfv(GL_LIGHT_MODEL_LOCAL_VIEWER, TRUE);
```

<div style="page-break-after: always;"></div>

#### Luz ambiente, difusa e especular
Están formadas por **3 compoñentes** (en RGB):
```c
Glfloat light_ambient[] = {1.0, 0.0, 1.0, 1.0};
Glfloat light_diffuse[] = {1.0, 1.0, 0.0, 1.0};
Glfloat light_specular[] = {0.0, 1.0, 1.0, 1.0};

glLightfv(GL_LIGHT0, GL_AMBIENT, light_ambient);
glLightfv(GL_LIGHT0, GL_DIFFUSE, light_diffuse);
glLightfv(GL_LIGHT0, GL_SPECULAR, light_specular);
```
#### Luz direccional
Está **situada no infinito**, polo que _non hai ateuación_ e a _dirección por defecto é (0, 0, 1, 0)_:
```c
float direccion[4] = {0.0, 0.0, 1.0, 0.0};
glLightfv(GL_LIGHT0, GL_POSITION, direccion);
```
![[luzDirec.png| center | 150]]
#### Luz local
Defínense _igual ca as direccionais_ agás polo feito de que o **último elemento do vector é 1**:
```c
float posicion[4] = {1.0, 1.0, 1.0, 1.0};
float direccion[4] = {1.0, 1.0, 1.0, 1.0};
glLightfv(GL_LIGHT0, GL_POSITION, posicion);
glLightfv(GL_LIGHT0, GL_SPOT_DIRECTION, direccion);
```
![[luzLocal.png| center | 200]]
#### Foco
Ven  definido por unha **luz local** cunha determinada **apertura**:
```c
glLightfv(GL_LIGHT0, GL_POSITION, posicion);
glLightfv(GL_LIGHT0, GL_SPOT_DIRECTION, direccion);
glLightf(GL_LIGHT0, GL_SPOT_CUTOFF, 20);
glLightf(GL_LIGHT0, GL_SPOT_EXPONENT, 0);
```
![[foco.png| center | 300]]

#### Especificar as propiedades da luz
Para especificar os vaores de posición, direción e atenuación, empréganse as funcións:
```c
glLightf(SourceNumber, Parameter, Value); // Para escalares
glLightfv(SourceNumber, Parameter, PointerToArray); // Para vectores
```

![[taboaPropiedadesLuz.png| center | 500]]

#### Sombreados
Depende de se declaramos **unha normal por cara** (_flat shading_ ou iluminación plana) ou **unha normal por vértice** (_smooth shading_ ou sombreado suave).

A **iluminación FLAT** é a máis simple e eficiente. Nela, _todo o polígono presenta a mesma cor_ porque
OpenGL só avalía unha para todos os seus puntos, xa que a **normal** calcúlase e normalízase a partir dunha _lista de vértices_ (ou _especifícase_ para un conxunto deles). Actívase con `glShadeModel(GL_FLAT);`.

##### Sombreados de interpolación
Na **iluminación SMOOTH**, a cada vértice asígnaselle unha normal e OpenGL _promedia os valores de cor en toda a superficie do obxecto_. Actívase con `glEnable(GL_SMOOTH);` e `glShadeModel(GL_SMOOTH);`. A **normal** dun vértice é elexida como a _media das caras das que forma parte o vértice_. 

<div style="page-break-after: always;"></div>

**Gouraud shading**:
A _normal media_ calcúlase para computar a _cor de cada vértice_. A cor de cada punto do polígono é calculada mediante a interpolación bilinear entre as cores do mesmo.
![[gouraudShading.png| center | 250]]

**Phong shading**:
Declara _unha normal por punto_, polo que se achega máis á _realidade_ pero tamén consume _máis recursos_ porque emprega unha _interpolación bi-lineal_ para calcular a normal de cada punto do polígono.
![[phongShading.png| center | 250]]
### 3. Propiedades dos materiais
O modelo de iluminación de OpenGL realiza unha **aproximación da cor dun material** en función da _luz que incide_ sobre el e a que é capaz de _reflexar_.
![[esquemaPropiedadesMAteriais.png| center | 500]]

**Primitivas** que se empregan para especificar diferentes _parámetros do material en cada polígono_: 
`void glMaterial{if}{v}(Glenum face, Glenum pname, TYPE *param);`
![[Pasted image 20250510124112.png | center | 500]]
> Ambient e Diffuse determinan a cor do material e adoitan ter valores aproximados se non iguais.

<div style="page-break-after: always;"></div>

#### Specular
Indica cómo se vai comportar o material en función da **compoñente Specular do raio de luz incidente**. Adóitase definir _branca ou gris_ ou da cor coa que se ilumine, de forma que _os reflexos do material se degradan dende a cor base da luz incidente ata a cor definida_ nesta propiedade.
#### Shininess
Determina a **concentración dos puntos que van reflexar a luz**. Pode ser _un só ou toda a superficie_ do obxecto ([0, 128]). _A máis concentración, máis aspecto metálico_.
#### Emission
Define a capacidade de simular a **emisión de luz sen ser propiamente unha fonte**. Especifícanse as _cores da luz que emite_ e serve para _simular estrelas, lámpadas_ ou outros obxectos similares.

## OpenGL 3.3
### Iluminación básica
A **cor dun obxecto** é froito da súa cor _orixinal multiplicada pola cor da luz_ coa que se ilumina.
Imos definir _dúas variables para pasarlle ao fragment shader_, no que incluímos o seu produto:
```c
#version 330 core

uniform vec3 lightColor;
uniform vec3 objColor;

void main(){
	Gl_FragColor = vec4(lightColor * objColor, 1.0);
}
```
### Luz ambiente
$$
I = I_{a} \cdot K_{a} 
$$
```c
// Cor do obxecto
unsigned int colorLoc = glGetUniformLocation(shaderProgram, "objColor");
glUniform3f(colorLoc, 0.9f,0.3f,0.3f);
// Cor da luz
unsigned int lightLoc = glGetUniformLocation(shaderProgram, "lightColor");
glUniform3f(lightLoc, 0.9f, 0.6f, 0.6f);
```

<div style="page-break-after: always;"></div>

Fragment shader:
```c
#version 330 core

uniform vec3 lightColor;
uniform vec3 objColor;

void main(){
	float ambientI = 0.1;
	vec3 ambient = ambientI * lightColor;
	vec3 result = ambient * objColor;
	Gl_FragColor = vec4(result, 1.0);
}
```

### Luz difusa
Temos as mesmas luces ambiente, difusa e especular coas mesmas características:
$$
I = I_L \cdot K_d \cos(\theta) = I_L \cdot K_d \left( \vec{N} \cdot \vec{L}\right)
$$
Neste caso precisamos **pasarlle ao shader** a _atenuación_, as coordenadas das _normais_ das caras, a _posición_ da luz e o punto de cálculo para ver a _dirección_ da luz L.
```c
// Vertices
glVertexAttribPointer(0, 3, GL_FLOAT, GL_FALSE, 8 * sizeof(float), (void*)0);
glEnableVertexAttribArray(0);
// Normais
glVertexAttribPointer(1, 3, GL_FLOAT, GL_FALSE, 8 * sizeof(float), (void*)(3 * sizeof(float)));
glEnableVertexAttribArray(1);

// Cor da luz
unsigned int lightColorLoc = glGetUniformLocation(shaderProgram,
"lightColor");
glUniform3f(lightColorLoc, 0.9f, 0.6f, 0.6f);
// Posición da luz (facémolo abaixo porque vai co obxecto que se move)
float lightPosX = 0.0 + -5 * sin((float)glfwGetTime() / 2.0f);
float lightPosY = 0.0;
float lightPosZ = 0.0 + (-5) * cos((float)glfwGetTime() / 2.0f);
unsigned int lightPosLoc = glGetUniformLocation(shaderProgram, "lightPos");
glUniform3f(lightPosLoc, lightPosX, lightPosY, lightPosZ);

```

<div style="page-break-after: always;"></div>

Vetrex shader:
```c
#version 330 core

layout (location = 0) in vec3 aPos;
layout (location = 1) in vec3 aNormal;

out vec3 Normal;
out vec3 FragPos;

uniform mat4 model;
uniform mat4 view;
uniform mat4 projection;

void main() {
    gl_Position = projection * view * model * vec4(aPos, 1.0f);
    Normal = mat3(transpose(inverse(model))) * aNormal;
    FragPos = vec3(model * vec4(aPos, 1.0));
}
```

Fragment shader:
```c
#version 330 core

in vec3 Normal;
in vec3 FragPos;

uniform vec3 lightPos;
uniform vec3 lightColor;
uniform vec3 objColor;

void main() {
    // ambient
    float ambientI = 0.5;
    vec3 ambient = ambientI * lightColor;

    // diffuse
    vec3 norm = normalize(Normal);
    vec3 lightDir = normalize(lightPos - FragPos);
    float diff = max(dot(norm, lightDir), 0.0);
    vec3 diffuse = diff * lightColor;

    vec3 result = (ambient + diffuse) * objColor;
    FragColor = vec4(result, 1.0);
}
```

<div style="page-break-after: always;"></div>

### Luz especular
$$
I = I_L \cdot K_S \cos^n(\alpha) = I_L \cdot K_S \left( \vec{R} \cdot \vec{V} \right)^n
$$
Só nos falta a posición da cámara.
Fragment shader final:
```c
#version 330 core
out vec4 FragColor;

in vec3 Normal;
in vec3 FragPos;

uniform vec3 viewPos; // <-----
uniform vec3 lightPos;
uniform vec3 lightColor;
uniform vec3 objColor;

void main() {
    // ambient
    float ambientI = 0.5;
    vec3 ambient = ambientI * lightColor;

    // diffuse
    vec3 norm = normalize(Normal);
    vec3 lightDir = normalize(lightPos - FragPos);
    float diff = max(dot(norm, lightDir), 0.0);
    vec3 diffuse = diff * lightColor;

    // specular
    float specularI = 1.0;
    vec3 viewDir = normalize(viewPos - FragPos); // <-----
    vec3 reflectDir = reflect(-lightDir, norm);
    float spec = pow(max(dot(viewDir, reflectDir), 0.0), 128);
    vec3 specular = specularI * spec * lightColor;

	// Resultado
    vec3 result = (ambient + diffuse + specular) * objectColor;
    FragColor = vec4(result, 1.0);
}
```






