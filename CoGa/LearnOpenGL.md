## The graphics pipeline
Son unha serie de funcións que collen **datos de entrada** (vertex data = unha serie de vértices cos seus respectivos atributos) e ao final **devolven un frame**.
### 1. Vertex Shader
Colle os vertices e os coloca no espazo.

### 2. Shape  Assembly
Colle as posicións dos vertices e os conecta según unha primitiva (unha forma como un triangulo, unha liña...)

## 3. Geometry Shader
Pode engadir vertices ecrear outras primitivas

### 4. Rasterización
Transformanse as formas en píxeles

### 5. Fragment Shader
Colorea os píxeles dependendo dunha serie de atributos (podense ter varias cores para un só pixel debido a obxetos que se sobrepoñen)

### 6. Test and blending
Soluciona a superposición de obxetos

---

> Os shaders son un obxeto de OpenGL, que se atopan en memoria, polo que só podemos acceder a eles por referencia. De feito, tódolos obxetos de OpenGL son accedidos por referencia. Os shaders son funcións que se executan na GPU e se comunican co resto do programa mediante os inputs/outputs.
![[vertexShaderAttrib.png| center]]

### Going 3D
![[matricesTransformacion.png]]
