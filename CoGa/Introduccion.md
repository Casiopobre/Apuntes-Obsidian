# Ideas xerales
Fenómeno de **persistencia**: permitenos percibir _movemento_ (capturamos certa cantidade de imaxes por segundo)

**Tubo de Raios Catódicos**: "arráncanselle electróns dun cátodo", que, cando chocan contra a pantalla de fósforo, xeran luz
![[RaiosCatadicos.png | center | 200]]

**TCR (Terminales vectoriais)**: o haz de electróns recorre a pantalla según as ordes de debuxo.

**Terminais raster**: consisten nunha matriz na que se deciden qué pixeles teñen que estar encendidos ou apagados  --> Rasterizado
![[Rasterizado.png| | center | 400]]

**Frame/color buffer**: é unha _zona de memoria_ na que se almacena a matriz de 0 e 1 que indica os píxeles que teñen que estar encendidos ou apagados --> Imaxes
+ _Profundidade de cor_: numero de cores dispoñibles en función de cantos bits se lle poden dedicar a cada cor
==Ver diapositivas de conceptos (31)==

## Dobre buffer: 
**Problema**: ao frame buffer non lle da tempo a actualizarse antes de que cambie na pntalla
**Solución**: 2 frame buffers para ir intercambiando dunha imaxe á outra (`swap`), para non chegar a ver como se crea
![[Pasted image 20250129164049.png| center | 400]]
## Buffer de profundidade:
Permite pintar obxetos en profundidade (un detrás ou diante de outro)
**Problema**: algoritmo do pintor (oss obxetos debuxanse no orde no que chegan)
**Solución**: Buffer z 