
En las siguientes secciones analizaremos algunas de las cuestiones relacionadas con esta **comunicación entre procesos** o **IPC**.

Hay tres cuestiones aquí:

1. Cómo un proceso puede pasar información a otro
2. Hacer que dos o más procesos no se interpongan entre sí (Reserva asientos aerolínea)
3. Obtener la secuencia apropiada cuando hay dependencias presentes

Dos de estas cuestiones se aplican de igual forma a los hilos. La primera (el paso de información) es fácil para los hilos, ya que comparten un espacio de direcciones común. Sin embargo, las otras dos se aplican de igual forma a los hilos (mismos problemas, mismas soluciones).

# Condiciones de carrera

Los procesos que trabajan en conjunto pueden compartir cierto espacio de almacenamiento en el que pueden leer y escribir datos. El almacenamiento compartido puede estar en la memoria principal (posiblemente en una estructura de datos del kernel) o puede ser un archivo compartido; **la ubicación de la memoria compartida no cambia la naturaleza de la comunicación o los problemas que surgen**. 

Para ver cómo funciona la comunicación entre procesos en la práctica, consideremos un ejemplo simple pero común: un spooler de impresión. Cuando un proceso desea imprimir un archivo, introduce el nombre del archivo en un **directorio de spooler** especial. Otro proceso, el **demonio de impresión**, comprueba en forma periódica si hay archivos que deban imprimirse y si los hay, los imprime y luego elimina sus nombres del directorio.

Imagine que nuestro directorio de spooler tiene una cantidad muy grande de ranuras, numeradas como 0, 1, 2, …, cada una de ellas capaz de contener el nombre de un archivo. Imagine también que hay dos variables compartidas: ***sal***, que apunta al **siguiente archivo a imprimir**, y ***ent***, que apunta a la **siguiente ranura libre en el directorio**. En cierto momento, las ranuras de la 0 a la 3 están vacías (ya se han impreso los archivos) y las ranuras de la 4 a la 6 están llenas (con los nombres de los archivos en la cola de impresión). De manera más o menos simultánea, los procesos A y B deciden que desean poner en cola un archivo para imprimirlo. Esta situación se muestra en la figura:

![[CarreraCritica.png]]

El proceso A lee ent y guarda el valor 7 en una variable local, llamada **siguiente_ranura_libre**. Justo entonces ocurre una interrupción de reloj y la CPU decide que el proceso A se ha ejecutado durante un tiempo suficiente, por lo que conmuta al proceso B. El proceso B también lee **ent** y también obtiene un 7. De igual forma lo almacena en su variable local *siguiente_ranura_libre*. **En este instante, ambos procesos piensan que la siguiente ranura libre es la 7**.

Ahora el proceso B continúa su ejecución. **Almacena el nombre de su archivo en la ranura 7 y actualiza *ent* para que sea 8**. En cierto momento el **proceso A se ejecuta de nuevo**, partiendo del lugar en el que se quedó. ***Busca en siguiente_ranura_libre, encuentra un 7 y escribe el nombre de su archivo en la ranura 7, borrando el nombre que el proceso B acaba de poner ahí***. **Luego calcula siguiente_ranura_libre + 1**, que es 8 y fija ent para que sea 8. 

El directorio de spooler es ahora internamente consistente, por lo que el demonio de impresión no detectará nada incorrecto, pero el proceso B nunca recibirá ninguna salida. Situaciones como ésta, en donde dos o más procesos están leyendo o escribiendo algunos datos compartidos y el resultado final depende de quién se ejecuta y exactamente cuándo lo hace, se conocen como ***condiciones de carrera***.

# Regiones críticas

¿Cómo evitamos las condiciones de carrera? La clave para evitar problemas aquí y en muchas otras situaciones en las que se involucran la memoria compartida, los archivos compartidos y todo lo demás compartido es buscar alguna manera de prohibir que más de un proceso lea y escriba los datos compartidos al mismo tiempo. Dicho en otras palabras, lo que necesitamos es ***exclusión mutua***, cierta forma de asegurar que si un proceso está utilizando una variable o archivo compartido, los demás procesos se excluirán de hacer lo mismo. La dificultad antes mencionada ocurrió debido a que el proceso B empezó a utilizar una de las variables compartidas antes de que el proceso A terminara con ella.

Algunas veces un proceso tiene que acceder a la memoria compartida o a archivos compartidos, o hacer otras cosas críticas que pueden producir carreras. Esa parte del programa en la que se accede a la memoria compartida se conoce como **región crítica** o **sección crítica**. Si pudiéramos ordenar las cosas de manera que dos procesos nunca estuvieran en sus regiones críticas al mismo tiempo, podríamos evitar las carreras.

Aunque este requerimiento evita las condiciones de carrera, no es suficiente para que los procesos en paralelo cooperen de la manera correcta y eficiente al utilizar datos compartidos. Necesitamos cumplir con cuatro condiciones para tener una buena solución:

1. No puede haber dos procesos de manera simultánea dentro de sus regiones críticas.
2. No pueden hacerse suposiciones acerca de las velocidades o el número de CPUs.
3. Ningún proceso que se ejecute fuera de su región crítica puede bloquear otros procesos.
4. Ningún proceso tiene que esperar para siempre para entrar a su región crítica.

En sentido abstracto, el comportamiento que deseamos se muestra en la figura:

![[ExclusionMutuaUsandoRegionesCriticas.png]]

Aquí el proceso A entra a su región crítica en el tiempo T<sub>1</sub>. Un poco después, en el tiempo T<sub>2</sub>el proceso B intenta entrar a su región crítica, pero falla debido a que otro proceso ya se encuentra en su región crítica y sólo se permite uno a la vez. En consecuencia, B se suspende temporalmente hasta el tiempo T<sub>3</sub> cuando A sale de su región crítica, con lo cual se permite a B entrar de inmediato.

# Exclusión mutua con espera ocupada

En esta sección examinaremos varias proposiciones para lograr la exclusión mutua, de manera que mientras un proceso esté ocupado actualizando la memoria compartida en su región crítica, ningún otro proceso puede entrar a su región crítica y ocasionar problemas.

## Deshabilitando interrupciones

En un sistema con un **solo procesador**, la solución más simple es hacer que cada proceso **deshabilite todas las interrupciones** justo después de entrar a su región crítica y las rehabilite justo después de salir. Con las interrupciones deshabilitadas, **no pueden ocurrir interrupciones de reloj**. Después de todo, la CPU sólo se conmuta de un proceso a otro como resultado de una interrupción del reloj o de otro tipo, y con las interrupciones desactivadas la CPU no se conmutará a otro proceso. Por ende, una vez que un proceso ha deshabilitado las interrupciones, puede examinar y actualizar la memoria compartida sin temor de que algún otro proceso intervenga.

Por lo general **este método es poco atractivo**, ya que **no es conveniente dar a los procesos de usuario el poder para desactivar las interrupciones**. Suponga que uno de ellos lo hiciera y nunca las volviera a activar.

Por otro lado, con frecuencia es conveniente para el mismo kernel **deshabilitar las interrupciones por unas cuantas instrucciones** mientras actualiza variables o listas. Por ejemplo, si ocurriera una interrupción mientras la lista de procesos se encuentra en un estado inconsistente, podrían producirse condiciones de carrera. La conclusión es que a menudo deshabilitar interrupciones es una técnica útil dentro del mismo sistema operativo, pero no es apropiada como mecanismo de exclusión mutua general para los procesos de usuario.

En un multinúcleo (es decir, sistema con multiprocesadores) al deshabilitar las interrupciones de una CPU no se evita que las demás CPUs interfieran con las operaciones que la primera CPU está realizando. En consecuencia, se requieren esquemas más sofisticados.

## Variables candado (Solución software) (No funciona)

Considere tener una sola variable compartida (de candado), que al principio es 0. Cuando un proceso desea entrar a su región crítica primero evalúa el candado. Si este candado es 0, el proceso lo fija en 1 y entra a la región crítica.

Por desgracia, esta idea contiene exactamente el mismo error fatal que vimos en el directorio de spooler. Suponga que un proceso lee el candado y ve que es 0. Antes de que pueda fijar el candado a 1, otro proceso se planifica para ejecutarse y fija el candado a 1. Cuando el primer proceso se ejecuta de nuevo, también fija el candado a 1 y por lo tanto dos procesos se encontrarán en sus regiones críticas al mismo tiempo.

Ahora el lector podría pensar que podemos resolver este problema si leemos primero el valor de candado y después lo verificamos de nuevo justo antes de almacenar el nuevo valor en él, pero en realidad eso no ayuda. La condición de carrera se produce ahora si el segundo proceso modifica el candado justo después que el primer proceso haya terminado su segunda verificación.

## Alternancia estricta (Software) (Incumple condición 3)

![[AlternanciaEstricta.png]]
           a) Proceso 1                                                    b) Proceso 2

La variable entera turno (que al principio es 0) lleva la cuenta acerca de a qué proceso le toca entrar a su región crítica y examinar o actualizar la memoria compartida. Al principio, el proceso 0 inspecciona turno, descubre que es 0 y entra a su región crítica. El proceso 1 también descubre que es 0 y por lo tanto se queda en un ciclo estrecho, evaluando turno en forma continua para ver cuándo se convierte en 1. 

A la acción de evaluar en forma continua una variable hasta que aparezca cierto valor se le conoce como ***espera ocupada*** (espera activa). Por lo general se debe evitar, ya que **desperdicia tiempo de la CPU**. La espera ocupada sólo se utiliza cuando hay una expectativa razonable de que la espera será corta. A **un candado que utiliza la espera ocupada** se le conoce como ***candado de giro***.

Cuando el proceso 0 sale de la región crítica establece turno a 1, para permitir que el proceso 1 entre a su región crítica. Suponga que el proceso 1 sale rápidamente de su región crítica, de manera que ambos procesos se encuentran en sus regiones no críticas, con turno establecido en 0. Ahora el proceso 0 ejecuta todo su ciclo rápidamente, saliendo de su región crítica y estableciendo turno a 1. En este punto, turno es 1 y ambos procesos se están ejecutando en sus regiones no críticas.

De repente, el proceso 0 termina en su región no crítica y regresa a la parte superior de su ciclo. Por desgracia no puede entrar a su región crítica ahora, debido a que turno es 1 y el proceso 1 está ocupado con su región no crítica. El proceso 0 espera en su ciclo while hasta que el proceso 1 establezca turno a 0. Dicho en forma distinta, **tomar turnos no es una buena idea cuando uno de los procesos es mucho más lento que el otro**.

***Esta situación viola la condición 3 antes establecida***: **el proceso 0 está siendo bloqueado por un proceso que no está en su región crítica**. 

## Solución de Peterson (Software)

![[SolucionPeterson.png]]

Antes de utilizar las variables compartidas (es decir, antes de entrar a su región crítica), cada proceso llama a *entrar_region* con su propio número de proceso (0 o 1) como parámetro. Esta llamada hará que espere, si es necesario, hasta que sea seguro entrar. Una vez que haya terminado con las variables compartidas, el proceso llama a *salir_region* para indicar que ha terminado y permitir que los demás procesos entren, si así lo desea.

El proceso 0 llama a entrar_region. Indica su interés estableciendo su elemento del arreglo y fija turno a 0. Como el proceso 1 no está interesado, entrar_region regresa de inmediato. Si ahora el proceso 1 hace una llamada a entrar_region, se quedará ahí hasta que interesado \[0] sea FALSE, un evento que sólo ocurre cuando el proceso 0 llama a salir_region para salir de la región crítica.

Ahora considere el caso en el que ambos procesos llaman a entrar_region casi en forma simultánea. Ambos almacenarán su número de proceso en turno. Cualquier almacenamiento que se haya realizado al último es el que cuenta; el primero se sobrescribe y se pierde. Suponga que el proceso 1 almacena al último, por lo que turno es 1. Cuando ambos procesos llegan a la instrucción while, el proceso 0 la ejecuta 0 veces y entra a su región crítica. El proceso 1 itera y no entra a su región crítica sino hasta que el proceso 0 sale de su región crítica.

> **¿Espera activa?** : Sí, la solución de Peterson contiene epera activa, el while se ejecuta continuamente mientras no se cede el turno.

## La instrucción TSL (Ayuda del hardware)

Ahora veamos una proposición que requiere un poco de ayuda del hardware. Algunas computadoras, en especial las diseñadas con varios procesadores en mente, tienen una instrucción tal como:

	TSL REGISTRO, CANDADO

**Lee el contenido de la palabra de memoria candado y lo guarda en el registro RX, y después almacena un valor distinto de cero en la dirección de memoria candado**. Se garantiza que las operaciones de leer la palabra y almacenar un valor en ella serán indivisibles; ningún otro procesador puede acceder a la palabra de memoria sino hasta que termine la instrucción. La CPU que ejecuta **la instrucción TSL bloquea el bus de memoria para impedir que otras CPUs accedan a la memoria hasta que termine**.

**Es importante observar que bloquear el bus de memoria es una acción muy distinta de la de deshabilitar las interrupciones**. Al deshabilitar las interrupciones y después realizar una operación de lectura en una palabra de memoria, seguida de una operación de escritura, no se evita que un segundo procesador en el bus acceda a la palabra entre la lectura y la escritura. De hecho, si se deshabilitan las interrupciones en el procesador 1 no se produce efecto alguno en el procesador 2. **La única forma de mantener el procesador 2 fuera de la memoria hasta que el procesador 1 termine es bloquear el bus**, para lo cual se requiere una herramienta de hardware especial.

Para usar la instrucción TSL **necesitamos una variable compartida** (candado) **que coordine el acceso a la memoria compartida**. Cuando candado es 0, cualquier proceso lo puede fijar en 1 mediante el uso de la instrucción TSL y después una lectura o escritura en la memoria compartida. Cuando termina, el proceso establece candado de vuelta a 0 mediante una instrucción *move* ordinaria.

¿Cómo se puede utilizar esta instrucción para evitar que dos procesos entren al mismo tiempo en sus regiones críticas? La solución se proporciona en la figura 2-25, donde se muestra una subrutina de cuatro instrucciones en un lenguaje ensamblador ficticio:

![[InstruccionTSL.png]]

La primera instrucción copia el antiguo valor de candado en el registro y después fija el candado a 1. Después, el valor anterior se compara con 0. Si es distinto de cero, el candado ya estaba cerrado, por lo que el programa sólo regresa al principio y lo vuelve a evaluar. Tarde o temprano se volverá 0 y la subrutina regresará, con el bloqueo establecido. Es muy simple quitar el bloqueo. El programa sólo almacena un 0 en candado. No se necesitan instrucciones especiales de sincronización.

Ahora tenemos una solución simple y directa para el problema de regiones críticas. Antes de entrar a su región crítica, un proceso llama a *entrar_region*, que lleva a cabo una ***espera ocupada*** hasta que el candado está abierto. Al igual que con todas las soluciones basadas en regiones críticas, los procesos deben llamar a *entrar_region* y *salir_region* en los momentos correctos para que el método funcione. Si un proceso hace trampa, la exclusión mutua fallará.

## Dormir y despertar

Tanto la solución de **Peterson** como las soluciones mediante **TSL** son correctas, pero todas tienen el defecto de **requerir la espera ocupada**. 

Este método no sólo desperdicia tiempo de la CPU, sino que también puede tener efectos inesperados. Considere una computadora con dos procesos: H con prioridad alta y L con prioridad baja. Las reglas de planificación son tales que H se ejecuta cada vez que se encuentra en el estado listo. En cierto momento, con L en su región crítica, H cambia al estado listo para ejecutarse. Entonces H empieza la espera ocupada, pero como L nunca se planifica mientras H está en ejecución, L nunca tiene la oportunidad de salir de su región crítica, por lo que H itera en forma indefinida. A esta situación se le conoce algunas veces como el problema de ***inversión de prioridades***.

Veamos ahora ciertas primitivas de comunicación entre procesos que **bloquean en vez de desperdiciar tiempo de la CPU** cuando no pueden entrar a sus regiones críticas. Una de las más simples es el par ***sleep*** (dormir) y ***wakeup*** (despertar). **Sleep** es una llamada al sistema que **hace que el proceso que llama se bloquee** o desactive, es decir, que se suspenda **hasta que otro proceso lo despierte**. La llamada ***wakeup*** **tiene un parámetro, el proceso que se va a despertar o activar**. ***De manera alternativa***, **tanto sleep como wakeup tienen un parámetro, una dirección de memoria** que se utiliza para **asociar las llamadas** a sleep con las llamadas a wakeup.

### El problema del productor consumidor

Dos procesos comparten un búfer común, de tamaño fijo. Uno de ellos (el productor) coloca información en el búfer y el otro (el consumidor) la saca (también es posible generalizar el problema de manera que haya m productores y n consumidores).

El problema surge cuando el productor desea colocar un nuevo elemento en el búfer, pero éste ya se encuentra lleno. La solución es que el productor se vaya a dormir (se desactiva) y que se despierte (se active) cuando el consumidor haya quitado uno o más elementos. De manera similar, si el consumidor desea quitar un elemento del búfer y ve que éste se encuentra vacío, se duerme hasta que el productor coloca algo en el búfer y lo despierta.

Este método suena lo bastante simple, pero produce los mismos tipos de condiciones de carrera que vimos antes con el directorio de spooler. **Para llevar la cuenta del número de elementos en el búfer, necesitamos una variable** (***cuenta***). Si el número máximo de elementos que puede contener el búfer es N, el código del productor comprueba primero si cuenta es N. Si lo es, el productor se duerme; si no lo es, el productor agrega un elemento e incrementará cuenta.

El código del consumidor es similar: primero evalúa cuenta para ver si es 0. Si lo es, se duerme; si es distinta de cero, quita un elemento y disminuye el contador cuenta. **Cada uno de los procesos también comprueba si el otro se debe despertar y de ser así, lo despierta**.

Para expresar llamadas al sistema como **sleep** y **wakeup** en C, las mostraremos como llamadas a rutinas de la biblioteca.

![[Productor_Consumidor.png]]

Los procedimientos *insertar_elemento* y *quitar_elemento*, que no se muestran aquí, se encargan de colocar elementos en el búfer y sacarlos del mismo.

Es posible que ocurra la siguiente situación: el búfer está vacío y el consumidor acaba de leer cuenta para ver si es 0. En ese instante, el planificador decide detener al consumidor en forma temporal y empieza a ejecutar el productor. El productor inserta un elemento en el búfer, in- crementa cuenta y observa que ahora es 1. Razonando que cuenta era antes 0, y que por ende el con- sumidor debe estar dormido, el productor llama a wakeup para despertar al consumidor.

Por desgracia, el consumidor todavía no está lógicamente dormido, por lo que la señal para despertarlo se pierde. Cuando es turno de que se ejecute el consumidor, evalúa el valor de cuenta que leyó antes, encuentra que es 0 y pasa a dormirse. Tarde o temprano el productor llenará el búfer y también pasará a dormirse. **Ambos quedarán dormidos para siempre**.

La esencia del problema aquí es que una señal que se envía para despertar a un proceso que no está dormido (todavía) se pierde. Si no se perdiera, todo funcionaría. **Una solución rápida es modificar las reglas para agregar al panorama un** ***bit de espera de despertar***. Cuando se envía una señal de despertar a un proceso que sigue todavía despierto, se fija este bit. Más adelante, cuando el proceso intenta pasar a dormir, si el bit de espera de despertar está encendido, se apagará pero el proceso permanecerá despierto. Este bit es una alcancía para almacenar señales de despertar.

Aunque el bit de espera de despertar logra su cometido en este ejemplo simple, es fácil construir ejemplos con tres o más procesos en donde un bit de espera de despertar es insuficiente. Podríamos hacer otra modificación y agregar un segundo bit de espera de despertar, tal vez hasta 8 o 32 de ellos, pero en principio el problema sigue ahí.

## Semáforos

Tienen dos operaciones, **down** y **up** (generalizaciones de sleep y wakeup, respectivamente). La operación down en un semáforo comprueba si el valor es mayor que 0. De ser así, disminuye el valor (es decir, utiliza una señal de despertar almacenada) y sólo continúa. Si el valor es 0, el proceso se pone a dormir sin completar la operación down por el momento. Las acciones de **comprobar el valor, modificarlo y posiblemente pasar a dormir**, se realizan en conjunto como una sola **acción atómica** indivisible. sta atomicidad es **absolutamente esencial** para resolver problemas de
sincronización y evitar condiciones de carrera.

La operación up incrementa el valor del semáforo direccionado. Si uno o más procesos estaban inactivos en ese semáforo, sin poder completar una operación down anterior, el sistema **selecciona uno de ellos** (al azar) y permite que complete su operación down. **Ningún proceso se bloquea al realizar una operación up**.

> En semáforos: SOLO SE DESBLOQUEA A UNO!

Lo normal es implementar up y down como llamadas al sistema, en donde el sistema
operativo **deshabilita brevemente todas las interrupciones**, mientras evalúa el semáforo, lo actualiza y pone el proceso a dormir, si es necesario. Como todas estas acciones requieren sólo unas cuantas instrucciones, no hay peligro al deshabilitar las interrupciones. Si se utilizan **varias CPUs**, cada semáforo **debe estar protegido por una variable de candado**, en donde se utilicen las instrucciones TSL para asegurar que sólo una CPU a la vez pueda examinar el semáforo.

### El problema del productor consumidor resuelto con semáforos

Esta solución utiliza **tres semáforos**: uno llamado ***llenas*** para contabilizar el número de ranuras llenas, otro llamado ***vacías*** para contabilizar el número de ranuras vacías y el último llamado ***mutex***, para asegurar que el productor y el consumidor no tengan acceso al búfer al mismo tiempo. Al principio llenas es 0, vacías es igual al número de ranuras en el búfer y mutex es 1.

> Los semáforos que se inicializan a 1 y son utilizados por dos o más procesos para asegurar que sólo uno de ellos pueda entrar a su región crítica en un momento dado se llaman **semáforos binarios**.

El semáforo **mutex se utiliza para la exclusión mutua**. El **otro uso de los semáforos** es para la **sincronización**. Los semáforos **vacías y llenas** se necesitan para **garantizar que ciertas secuencias de eventos ocurran o no**. En este caso, aseguran que el productor deje de ejecutarse cuando el búfer esté lleno y que el consumidor deje de ejecutarse cuando el búfer esté vacío.

![[Productor_Consumidor_Semaforos.png]]

## Mutexes

Cuando no se necesita la habilidad del semáforo de contar, algunas veces se utiliza una versión simplificada, llamada mutex. Los mutexes son buenos sólo para administrar la exclusión mutua para cierto recurso compartido o pieza de código.

Un mutex es una variable que puede estar en uno de dos estados: ***abierto*** (desbloqueado) o ***cerrado*** (bloqueado). En la práctica se implementan con un entero, en donde **0 indica que está abierto** y todos los demás valores indican que está cerrado.

Cuando un hilo (o proceso) necesita **acceso** a una región crítica, llama a **mutex_lock** (Lo mismo que down).  Si el mutex ya se encuentra cerrado, el hilo que hizo la llamada se bloquea hasta que el hilo que está en la región crítica termine y llame a **mutex_unlock** (Como up).

Como los mutexes son tan simples, se pueden implementar con facilidad en espacio de usuario, siempre y cuando haya una instrucción TSL. El código para **mutex_lock y mutex_unlock** para utilizar estos procedimientos con un paquete de **hilos de nivel usuario** se muestra a continuación:

![[Implement_mutex.png]]

El código de mutex_lock es similar al código de entrar_region de la solución de Peterson, solo que este último realiza una espera ocupada, en algún momento dado el reloj se agotará y algún otro proceso se programará para ejecutarlo. Tarde o temprano el proceso que mantiene el mutex pasa a ejecutarse y lo libera.

Con los hilos (**de usuario**), la situación es distinta debido a que **no hay un reloj que detenga los hilos** que se han ejecutado por demasiado tiempo. En consecuencia, un hilo que intente adquirir un mutex mediante la espera ocupada iterará en forma indefinida y nunca adquirirá el mutex debido a que **nunca permitirá que algún otro hilo se ejecute y libere el mutex**.

Cuando este último procedimiento no puede adquirir un mutex, llama a **thread_yield** para entregar la CPU a otro hilo. En consecuencia, **no hay espera ocupada**. Como thread_yield es sólo **una llamada al planificador de hilos en espacio de usuario, es muy rápido**. Como consecuencia, ni mutex_lock ni mutex_unlock requieren llamadas al kernel.

> Algunas veces un paquete de hilos ofrece una llamada **mutex_trylock que adquiere el mutex o devuelve un código de falla, pero no bloquea**. Esta llamada otorga al hilo la flexibilidad de decidir qué hacer a continuación, si hay alternativas además de esperar.

En este caso al tratarse de hilos en espacio de usuario, no nos preocupa el acceso compartido ya que los hilos comparten el mismo espacio direccionable, pero los procesos teniendo en cuenta que utilizamos memoria compartida para guardar el buffer de datos ¿cómo pueden compartir l**a variable turno** en el algoritmo de Peterson **o los semáforos** en un búfer común?

Hay dos respuestas. En primer lugar, **algunas de las estructuras de datos compartidas** (como los semáforos) **se pueden almacenar en el kernel** y se accede a ellas sólo a través de llamadas al sistema. En segundo lugar, **la mayoría de los sistemas operativos modernos ofrecen la manera de que los procesos compartan cierta porción de su espacio de direcciones con otros procesos**. De esta forma se pueden compartir los búferes y otras estructuras de datos. En el peor de los casos, que nada sea posible, se puede utilizar un
archivo compartido.

> Si dos o más procesos comparten la mayoría de, o todos, sus espacios de direcciones, la distinción entre procesos e hilos se elimina casi por completo, pero sin duda está presente. Dos procesos que comparten un espacio de direcciones común siguen teniendo distintos archivos abiertos, temporizadores de alarma y otras propiedades correspondientes a cada proceso, mientras que los hilos dentro de un solo proceso las comparten. Y siempre es verdad que varios procesos que comparten un espacio de direcciones común nunca tendrán la eficiencia de los hilos de nivel usuario, ya que el kernel está muy involucrado en su administración.

### Mutexes con Pthreads

Pthreads proporciona varias funciones que se pueden utilizar para sincronizar los hilos. 

Como es de esperarse, se pueden **crear** y **destruir**. Las llamadas para realizar estas operaciones son **pthread_mutex_init** y **pthread_mutex_destroy**, respectivamente. También se pueden cerrar mediante **pthread_mutex_lock**, que trata de **adquirir el mutex** y se **bloquea si ya está cerrado**. Además hay una opción **para tratar de cerrar un mutex y falla con un código de error en vez de bloquearlo, si ya se encuentra bloqueado**. Esta llamada es **pthread_mutex_trylock** y permite que un hilo realice en efecto la espera ocupada, si alguna vez se requiere. Por último, **pthread_mutex_unlock abre un mutex y libera exactamente un hilo si uno o más están en espera**. Los mutexes también pueden tener atributos, pero éstos se utilizan sólo para propósitos especializados.

![[pthread_mutex.png]]

#### Variables de condición
Pthreads ofrece un segundo mecanismo de sincronización: **las variables de condición**.  Los mutexes son buenos para permitir o bloquear el acceso a una región crítica. Las variables de condición **permiten que los hilos se bloqueen debido a que cierta condición no se está cumpliendo**.

Considerando el **productor-consumidor**: Los mutexes hacen que sea posible realizar la comprobación en forma atómica sin interferencia de los otros hilos, pero al descubrir que el búfer está lleno, **el productor necesita una forma de bloquearse y ser despertado posteriormente**. Esto es lo que permiten las variables de condición.

![[variables_condicion_pthread.png]]

Como podríamos esperar, hay llamadas para crear y destruir variables de condición. Las operaciones primarias en las variables de condición son ***pthread_cond_wait*** y ***thread_cond_signal***. **La primera bloquea el hilo que hace la llamada hasta que algún otro hilo le señala** (utilizando la segunda llamada). La llamada a ***pthread_cond_broadcast*** **se utiliza cuando hay varios hilos potencialmente bloqueados y esperan la misma señal**.

El patrón es que un hilo cierre un mutex y después espere en una variable condicional cuando no pueda obtener lo que desea. En algún momento dado, otro hilo lo señalará y podrá continuar. La llamada a **pthread_cond_wait cierra y abre en forma atómica el mutex** que contiene. Por esta razón, el mutex es uno de los parámetros.

También vale la pena observar que las variables de condición (a diferencia de los semáforos) **no tienen memoria**. Si se envía una señal a una variable de condición en la que no haya un hilo en espera, se pierde la señal. Los programadores deben tener **cuidado de no perder las señales**.

![[mutex_variables_prod_cons.png]]

> Con mutex adquieren el acceso a la región crítica, pero si se verifica la condición para que se bloqueen, al hacer el pthread_cond_wait se libera el mutex para dejar que el otro hilo lo uso y no se queden los dos bloqueados. (Supongo)

## Monitores

