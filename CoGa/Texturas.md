# Texturas
O emprego de texturas **aumenta o realismo da imaxe sen incrementar o número de polígonos**, proporciona unha clave para a percepción da profundidade, permite simular efectos FX (sombras, reflexos,...), pero **ocupan espazo**.
Un **mapa de texturas** é unha colección ou _array n-dimensional_ de valores que se representan _sobre a xeometría_.

## Tipos de texturas
**Texturas 1D**: array;    **Texturas 2D**:  imaxes ordinarias;    **Texturas 3D**: so as aceptan algúns sistemas
![[tiposTexturas.png| center  | 400]]

### Texturas procedurais
A "imaxe" calcúlase a partir dunha **función matemática** (algoritmo que a xera). Ocupan **pouco espazo** de memoria (consumen CPU) e pódense **reescalar fácilmente**, polo que resultan últiles para _superficies de calquera tamaño_. Empréganse, por exemplo, para xerar _mapas de xogos_ (Minecraft co mapa ruido...).

## Técnicas de texturizado
O **texturizado** consiste en _sobrepor sobre os polígonos imaxes que aumenten o seu realismo_. Existen varias **técnicas**:
+ **Texture Mapping**: Crea unha textura sobre a superficie a partir dun _patrón_ (imaxe).
+ **Bump Map**: _Perturba a superficie_ mediante unha _modificación das normais_ (pode non considerarse mapeado no seu sentido estricto).
+ **Enviromental Map**: _Mapea o entorno sobre a superficie_ (reflexos, light mapping,...).
+ **Light Mapping**: Mapea un _mapa de iluminación_ sobre o obxecto.
### Bump Mapping
É un tipo de **mapeado topolóxico**, que **modifica as normais** das caras dun obxecto sen cambiar a súa xeometría. Estas normais calcúlanse en función dun _mapa de alturas_: unha matriz de datos $2^n\times2^n$, xeralmente unha imaxe. Posteriormente, combínanse coa _xeometría_ do obxecto e calcúlase o _modelo de iluminación_ para eses novos valores de normais.
### Light Mapping
O **brillo das superficies** nunha escena _calcúlase previamente_ e _almacénase en mapas de textura_ para o seu uso posterior. Os mapas de luz aplícanse con maior frecuencia a **obxectos estáticos** para proporcionar efectos de iluminación (coma a global) cun _custo moi baixo_.
![[lightMapping.png| center | 350]]

### Displacement Mapping
**Modifica a xeometría** dun obxecto mediante un mapa de texturas, pero xera unha gran cantidade de _xeometría adicional_. A **diferencia do Bump Mapping**, pode xerar _sombras_, _oclusións_ e se pode empregar para _sombreados_, pero é _lento_ e require dun Geometry Shader.

## Como se aplican as texturas?
Hai dúas formas: **mapeado directo** (a textura mapéase _sobre o obxecto_) ou **mapeado en dúas fases** (mapéase a imaxe sobre un obxecto 3D e despois se _proxecta sobre o obxecto_).
### Mapeado directo de texturas (coordenadas u, v)
Para calcular o _valor dun píxel_ determinado é necesario empregar a **relación** entre a _textura_ (u, v) e os _vértices_ superficie. O obxectivo é obter as **coordenadas de textura**: valores que nos permiten indicar _que coordenada da textura corresponde a cada vértice_. En xeral, este proceso é moi complexo e adoita empregar un programa de deseño CAD.
> Coordenadas (u, v):
> Unha textura 2D é representada mediante un patrón de datos 2D, T(u,v) onde u(x) e v(y) se denominan _texture coordinates_. Nun mapa de texturas, asóciaselle un único punto de T a cada punto (x, y, z) da xeometría do obxecto.
> ![[coordenadasUV.png| center | 250]]

<div style="page-break-after: always;"></div>

### Mapeado de texturas en OpenGL
#### Habilitar o mapeado de texturas:
No _main()_ ou no _InitOpengl()_:
`glEnable(GL_TEXTURE_2D);`
Para _deshabilitalo_:
`glDisable(GL_TEXTURE_2D);`
#### Ler e activar a textura (facela operativa):
`void glGenTextures(GLsizei n, GLuint *textures);`
+ _n_ := número de índices a crear.
+ _textures_ := punteiro ao primeiro elemento do array onde se almacearán os índices.

`void glBindTexture(GLenum targert, GLuint texture);`
+ _target_ := GL_TEXTURE_1D ou GL_TEXTURE_2D.
+ _texture_ := índice da textura que queremos enlazar (non en uso).
Serve tanto para facela operativa como para empregala despois ao voo, coma os VAO.

`void glBindTexture(GL_TEXTURE_2D, 0);` $\rightarrow$ Non queremos usar ningunha
`glDeleteTextures(GLsizei num, const GLuint *nomes);`
#### Almacear en memoria ($256\times256:
Unha textura é especificada por un array de **texels**, que poderíamos crear manualmente:
`GLubyte imageData1[3*256*256]` $\rightarrow$ RGB * ancho * alto

Para **crear unha textura** podemos:
+ Escribir un programa que a xere procedurais
+ Lela dun ficheiro imaxes $\rightarrow$ empregamos as librerías adicionais STB:
```c
#define STB_IMAGE_IMPLEMENTATION
#include <stb_image.h>
int myCargaTexturas(char* nome){
int textura;
glGenTextures(1, &textura);
glBingTexture(GL_TEXTURE_2D, textura);
int width, height, nrChannels;
// RGB, RGBA
unsigned char *data = stbi_load(nome, &width, &height, &nrChannels, 0);
}
```

#### Definir a textura:
`void glTexImage2D(GLenum obxectivo, GLint nivel, GLint componhentes, GLsizei ancho,GLsizei alto, GLint borde, GLenum formato, GLenum tipo, const GLvoid *pixels);`
+ _obxectivo_ := define que textura hai que definir (GL_TEXTURE_2D ou 1D).
+ _nivel_ := indica o nivel de detalle das texturas, normalmente 0.
+ _componhentes_ := indica o número de compoñentes de cor empregados en cada píxel.
+ _ancho, alto_ := especifican o ancho e o alto da textura; deben ser potencia de 2.
+ _borde_ := controla o número de píxels de borde que vai empregar OpenGL.
+ _formato_ := indica o tipo de valores de cor esperados (p.e.: GL_RGB).
+ _tipo_ := tipo da variable que almacena os valores de cor por píxel (p.e.: GL_UNSIGNED_BYTE).
+ _píxels_ := punteiro á variable que almacena as cores por píxel.

#### Pegar a textura:
**OpenGL 1.2**:
```c
glBindTexture(GL_TEXTURE_2D, texture[0]);

glBegin(GL_QUADS);
	glTexCoord2f(0.0, 0.0);
	glVertex3fv(vertices[0]);
	glTexCoord2ff(0.0, 1.0);
	glVertex3fv(vertices[3]);
	glTexCoord2f(1.0, 1.0);
	glVertex3fv(vertices[4]);
	glTexCoord2f(1.0, 0.0);
	glVertex3fv(vertices[1]);
glEnd();

glCallList(1);
```
Os _programas de deseño_ xa nos proporcionan as coordenadas de textura (u, v).

<div style="page-break-after: always;"></div>

**OpenGL 3.3**:
A carga é igual, pero temos que _especificar as coordenadas de textura_:
```c
float vertices[] = {
//  vertices                normais                texturas
    -0.5f, 0.5f, 0.5f,      0.0f, 0.0f, 1.0f,      0.0, 0.0,
    -0.5f, -0.5f, 0.5f,     0.0f, 0.0f, 1.0f,      0.0, 1.0,
    0.5f, -0.5f, 0.5f,      0.0f, 0.0f, 1.0f,      1.0, 1.0,
    0.5f, 0.5f, 0.5f,       0.0f, 0.0f, 1.0f,      1.0, 0.0,
    -0.5f, 0.5f, 0.5f,      0.0f, 0.0f, 1.0f,      0.0, 0.0,
    0.5f, -0.5f, 0.5f,      0.0f, 0.0f, 1.0f,      1.0, 1.0
};

// vertices
glVertexAttribPointer(0, 3, GL_FLOAT, GL_FALSE, 8 * sizeof(float), (void*)0);
glEnableVertexAttribArray(0);

// normales
glVertexAttribPointer(1, 3, GL_FLOAT, GL_FALSE, 8 * sizeof(float), (void*)(3 * sizeof(float)));
glEnableVertexAttribArray(1);

// textures
glVertexAttribPointer(2, 2, GL_FLOAT, GL_FALSE, 8 * sizeof(float), (void*)(6 * sizeof(float)));
glEnableVertexAttribArray(2);
```

No vertex shader:
```c
#version 330 core
layout (location = 0) in vec3 aPos;
layout (location = 1) in vec3 aColor;
layout (location = 2) in vec2 aTexCoord; // <-----
out vec2 TexCoord;
out vec3 ourColor;
uniform mat4 transform;

void main() {
    gl_Position = transform * vec4(aPos, 1.0);
    ourColor = aColor; 
    TexCoord = vec2(aTexCoord.x, aTexCoord.y); // <-----
}
```

<div style="page-break-after: always;"></div>

## Parámetros de texturas
**Sobre o obxecto**: problemas de _tamaño_. Que facer se a textura non abarca todo o obxecto.
`glTexParameterf(GL_TEXTURE_2D, GL_TEXTURE_WRAP_S, GL_CLAMP);`
`glTexParameterf(GL_TEXTURE_2D, GL_TEXTURE_WRAP_T, GL_REPEAT);`
![[textures.png| center]]

**Sobre a pantalla**: problemas de _escala_. Que facer se, ao rasterizar, cada píxel da imaxe non se
corresponde en tamaño ao que se ve en pantalla (texels píxels).
- _Magnificación_: Se a superficie é maior cá textura, cada píxel vaise corresponder cun anaco pequeno de texel.
- _Minificación_: Se a superficie é menor cá textura, cada píxel vaise corresponder cun conxunto de texels contiguos.
![[magnifMinif.png| center | 300]]

`glTexParameteri(GL_TEXTURE_2D, GL_TEXTURE_MIN_FILTER, GL_LINEAR);`
`glTexParameteri(GL_TEXTURE_2D, GL_TEXTURE_MAG_FILTER, GL_LINEAR);`
![[nearestLInear.png| center | 250]]


**Outros efectos**: _mezclas_. Que facer se o obxecto final tiña unha cor.
`glTexEnvi(GL_TEXTURE_ENV,GL_TEXTURE_ENV_MODE,GLint valor);`

Onte o `valor` pode ser: 
+ `GL_REPLACE` $\rightarrow$ reemplaza a cor pola textura
- `GL_MODULATE` $\rightarrow$ multiplícanse cor e textura
- `GL_DECAL` $\rightarrow$ substitúe tendo en conta a canle alpha
- `GL_BLEND` $\rightarrow$ multiplica a cor do píxel pola cor da textura e a do entorno

## Mapeado multicapa en OpenGL 3.3
En OpenGL 3.3 é posible e doado empregar **máis dunha textura á vez** sobre un obxecto. Para isto empréganse as denominadas **unidades de textura**, que permiten ir _activando ou desactivando_ as texturas manualmente.
```c
glActiveTexture(GL_TEXTURE0);
glBindTexture(GL_TEXTURE_2D, texture);
glActiveTexture(GL_TEXTURE1);
glBindTexture(GL_TEXTURE_2D, texture2);
```

### Pasos a seguir:
**Ler dúas texturas e asignarlles distinto identificador**.

**Modificar o shader**:
```c
in vec2 TexCoord;
uniform sampler2D texture1;
uniform sampler2D texture2;
void main() {
FragColor = mix(texture(texture1, TexCoord), texture(texture2,
TexCoord), 0.3);
}
```

**Mapear as texturas sobre as variables uniformes**:
```c
glUseProgram(shaderProgram);
glUniformli(glGetUniformLocation(shaderProgram, "texture1"), 0);
glUniformli(glGetUniformLocation(shaderProgram, "texture2"), 1);
```

**Empregalas**:
```c
glActiveTexture(GL_TEXTURE0);
glBindTexture(GL_TEXTURE_2D, texture);
glActiveTexture(GL_TEXTURE1);
glBindTexture(GL_TEXTURE_2D, texture2);
```

			<div style="page-break-after: always;"></div>

## Transparencia Alpha
OpenGL pode **simular obxectos transparentes**. A forma máis sinxela de facer unha transparencia é mediante a _técnica da porta ou fiestra transparente_.
![[transparenciaAlpha.png| center | 550]]

### Alpha Test
O **modelo RGBA** acepta o valor alpha. O test _compara o valor do fragmento cun valor de referencia_ (por defecto, 0). A comparación faise en [0, 1] e habilítase con `glEnable(GL_ALPHA_TEST)`.

A **función alpha** (`glAlphaFunc(GLenum func, GLclampf ref)`) ten por defecto o valor `GL_ALWAYS`, pero acepta outros valores: `GL_NEVER`, `GL_LESS`, `GL_EQUAL`, `GL_GREATER`, ...

Para o **blending**: 
`glEnable(GL_BLEND), glBlendFunc(GL_SRC_ALPHA, GL_ONE_MINUS_SRC_ALPHA)`.

Nós só imos traballar con _texturas que teñan xa incorporado o canal alpha_ (PNG e GIFA), polo que
debemos ter en conta o **número total de canles** na función de lectura da imaxe.



