## Conceptos xerais
Fenómeno de **persistencia**: permitenos percibir _movemento_ (xa que capturamos certa cantidade de imaxes por segundo)

**Tubo de Raios Catódicos**: consiste nun cátodo que, ao arrancarlle electróns, estes chocan contra unha pantalla de fósforo e xeran luz.
![[RaiosCatadicos.png | center | 200]]

**TCR (Terminales vectoriais)**: o haz de electróns recorre a pantalla según as ordes de debuxo.
![[ArquitecturaTCR.png | center | 300]]

**Terminais raster**: consisten nunha _matriz bidimensional_ na que se deciden qué pixeles teñen que estar encendidos ou apagados (os píxeles defínense pola intersección dunha liña e unha columna)  --> Rasterizado
\***Raster**: repersentación dunha _imaxe_ como unha _matriz de píxeles_
\***Pixel**: cada un dos _puntos de cor_ que se mostran na pantalla
![[Rasterizado.png| | center | 400]]

### Frame/color buffer: 
É unha _zona de memoria_ na que se almacena a matriz que contén o conxunto de valor dos píxeles --> Raster (imaxe)
+ _Profundidade de cor_: numero de cores dispoñibles en función de cantos bits se lle poden dedicar a cada cor. 
	+ Para profundidades de cor <= 8 bits, os valores dos píxeles fan referencia a unha paleta de cores (_taboa GLUT_): 1bit/pixel = 2 cores; ... ; 8bits/pixel = 256 cores (_VGA_)
	+ Para profundidades de cor > 8 bits, emprégase o _modelo RGB estándar_: a de 24 bits/pixel denomínase _Cor Real_, e conta con 8 bits para cada compoñente RGB ![[CorReal.png | center | 200]]

\***Gráficos vectoriais**: formato de imaxe composto por _obxetos xeométricos_ que permite modificar o tamaño da imaxe sen perder calidade.
\***Renderizado**: proceso de creación dunha imaxe a partires dun modelo, que consiste en calcular a cor de cada pixel da imaxe tendo en conta as propiedades do modelo a representar.

### Dobre buffer: 
**Problema**: ao frame buffer non lle da tempo a actualizarse antes de que cambie na pantalla
**Solución**: 2 frame buffers para ir intercambiando dunha imaxe á outra (`swap`). Así non chegamos a mostrar o buffer antes de que se actualice.
![[DobreBuffer.png| center | 400]]
### Buffer de profundidade:
Permite pintar _obxetos en profundidade_ (un detrás ou diante de outro)
**Problema**: algoritmo do pintor (os obxetos debuxanse no orde no que chegan)
**Solución**: Buffer z ou buffer de profundidade

---
## Introducción a OpenGL
OpenGl _interactúa específicamente coa GPU_ do sistema.
É unha biblioteca de C (**API**) que **non ten xestor de ventás**, polo que necesita _librarías auxiliares_ para xestionar as ventás (contexto de traballo OpenGL): Glfw (ver. OpenGL > 3.3)

### Modelo cámara sintética
É o método que ten OpenGL a grandes rasgos para xerar a escena.
![[CamaraSintetica.png| center | 500]]
Consta da escena (situación dos obxetos), da cámara (posición e orientación) e da proxección (enfoque da cámara)

### APP+GPU operacións fixas (modo inmediato)
As escenas parten dun conxunto de vétices (obxetos), que, ao inicio, se trasladan á posición desexada (obtemos a escena = vertices de todos os obxetos colocados na sua posición). Despois compomos as caras (ensamblado), especificando como un conxunto de vértices se deben unir para formalas. Agora faise un proceso de raster: bárrese as liñas de cada cara para atopar como se representan na pantalla, obtendo fragmentos. Despois coloreamos os fragmentos en función de certos algoritmos (shaders). A patrir de ahí, só nos queda mirar que obxeto está mais preto da pantalla para pintalo (z-buffer).
![[EsquemaOperacionsFixas.png]]

### Retained mode
Implementa os shaders, que se meten entre o proceso de colocación dos vértices e entre o proceso de coloración dos fragmentos, xa que as GPUs modernas implementan moitas características que lles permiten "operar con estes elementos moi eficientemente"

### Construcción de obxetos





