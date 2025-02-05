# Introducción a OpenGL
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





