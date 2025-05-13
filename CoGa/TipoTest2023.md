### 1. Tengo un juego en mi ordenador que tiene las siguientes tasas de refresco:  
50f/s durante 100 frames, 25f/s durante 100 frames, 10f/s durante 100 frames, 25f/s durante 100 frames.  
¿Cuál es la tasa media de refresco en frames por segundo?

- a. = 25 f/s  
- b. = 20 f/s  
- c. = 15 f/s  
- d. = Otra

---

### 2. ¿Desde qué comienza el proceso de rasterizado, cuánto tiempo se necesitará para barrer cada columna durante el refresco de pantalla con una resolución de 640×480 y una velocidad de refresco de 30f/s?

- a. (640×480) / (1/30) segundos  
- b. (640×480) * (1/30) segundos  
- c. (1/30) / (640×480) segundos  
- d. Depende si es la primera o la última columna.

---

### 3. ¿Qué son las listas de visualización en OpenGL?

- a. Una herramienta para dibujar objetos en 3D.  
- b. Una herramienta para almacenar y reutilizar comandos de dibujo en 3D.  
- c. Una herramienta para crear efectos de iluminación en escenas 3D.  
- d. Una herramienta para simplificar el código y hacerlo más compacto.

---

### 4. ¿Para qué se utiliza la pila de matrices en OpenGL?

- a. Para almacenar las texturas de los objetos en una escena en 3D.  
- b. Para almacenar las transformaciones geométricas aplicadas a objetos en una escena 3D.  
- c. Para almacenar las transformaciones del world space al clip space.  
- d. Para almacenar los vértices de los objetos en una escena 3D.

---

### 5. ¿Qué tipo de estructura de datos se utiliza para la pila de matrices de transformación en OpenGL?

- a. FIFO  
- b. LIFO  
- c.  
- d. LIFO y FIFO, dependiendo si es de la transformación (LIFO), vista (FIFO). Se multiplican y solo se almacena la última.

---

### 6. Para pasar del view space al clip space se utiliza:

- a. La matriz de proyección.  
- b. La matriz de vista.  
- c. La matriz de transformación.  
- d. La matriz de vista por la de proyección por la transformación.

---

### 7. En el modelo de iluminación de Phong:

- a. Se considera la escena en un conjunto.  
- b. Se consideran los objetos individualmente.  
- c. La iluminación entre los objetos se realiza automáticamente, en la CPU o GPU si disponemos una dedicada.  
- d. Mejora velocidad de renderizado al optimizar el trazado de rayos.

---

### 8. La luz especular:

- a. Mejora el efecto del modelo de iluminación debido al coeficiente de especularidad.  
- b. Mejora el efecto del modelo de iluminación debido al coeficiente de reflexión especular.  
- c. Mejora el efecto del modelo de iluminación debido al coeficiente de especularidad y coeficiente de reflexión especular.  
- d. Mejora el efecto del modelo de iluminación al incluir la posición del usuario en la ecuación.

---

### 9. ¿Cuál es la función del modelo de sombreado en OpenGL?

- a. Definir la posición de los vértices en una malla.  
- b.  
- c. Especificar el material y la textura de un objeto.  
- d. Calcular la intensidad de la iluminación en cada punto de la superficie de un objeto.  
- e. Generar las sombras de un objeto sobre otro.

---

### 10. ¿Cuál es la diferencia entre el modelo de sombreado Gouraud y el modelo de sombreado de Phong?

- a. El modelo de sombreado Gouraud es más adecuado para superficies planas, mientras que el modelo de sombreado Phong funciona mejor con superficies curvas.  
- b. El modelo de sombreado Gouraud calcula la intensidad de la iluminación en los vértices de un objeto y luego interpola, mientras que el modelo de sombreado Phong calcula la intensidad a partir de la interpolación del valor de las normales de la superficie.  
- c. El modelo de sombreado Gouraud calcula la intensidad de la iluminación en los vértices de un objeto y se interpola a cada fragmento, mientras que el modelo de sombreado Phong calcula la intensidad a partir de la interpolación del valor de los fragmentos adyacentes.  
- d. Es el mismo, Gouraud es el nombre del inventor y Phong el del algoritmo.

---

### 11. El color en el interior de una cara en OpenGL 1.2 se calcula:

- a. Mediante el modelo de iluminación.  
- b. Durante el proceso de rasterizado.  
- c. Mediante el proceso de sombreado una vez se determina si la cara es visible o no.  
- d. Mediante el color de la cara, y la luz incidente sobre la misma.

---

### 12. El color en el interior de una cara en OpenGL 3.3 se calcula:

- a. Mediante el modelo de iluminación.  
- b. Durante el proceso de rasterizado.  
- c. En el vertex shader mediante el cálculo de la posición de los fragmentos y las normales.  
- d. Mediante el color de cada fragmento.

---

### 13. Cuando dibujamos los objetos y teniendo en cuenta el efecto z-buffer. ¿El proceso de render es más efectivo si?

- a. Dibujamos primero los objetos cercanos y luego los lejanos.  
- b. Dibujamos primero los objetos lejanos y luego los cercanos.  
- c. El z-buffer resuelve el problema en base, principalmente, a dos ecuaciones.  
- d. No se ve afectado por el z-buffer.

---

### 14. ¿Cuántos fragmentos pueden existir en escena?

- a. Tantos como píxeles.  
- b. Tantos como píxeles por el número de objetos.  
- c. Tantos como píxeles por la superficie en píxeles de caras.  
- d. Otros.

---

### 15. Las variables uniformes:

- a. Similares a las constantes y mantienen fijo su valor en el shader.  
- b. Permiten enviar valores desde el programa principal a los shaders.  
- c. Permiten comunicar los shaders entre sí.  
- d. Son variables normales: int, float, vectores o matrices.

---

### 16. En el alpha test:

- a. Se compara el valor de alfa de cada fragmento con un valor umbral predefinido.  
- b. Se realizan cálculos matemáticos complejos en la geometría de los objetos para determinar si son visibles o no.  
- c. Se aplican texturas RGBA a los objetos en una escena.  
- d. Se optimiza el trabajo del fragment shader.
