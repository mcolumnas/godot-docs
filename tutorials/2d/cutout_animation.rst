.. _doc_cutout_animation:

Animación Cutout
================

Que es?
~~~~~~~~~~~

Cut-out es una técnica de animación en 2D donde las piezas de papel (u
otro material similar) son cortadas en formas especiales y acomodadas
una sobre la otra. Los papeles son animados y fotografiados, frame por
frame usando técnica stop-motion (mas info
`aqui <http://en.wikipedia.org/wiki/Cutout_animation)>`__).

Con el advenimiento de la era digital, esta técnica se hizo posible
usando computadores, lo que resulto en un incremento de la cantidad de
shows de TV animados usando Cut-out digital. Ejemplos notables son `South
Park <http://en.wikipedia.org/wiki/South_Park>`__ or `Jake and the Never
Land Pirates <http://en.wikipedia.org/wiki/Jake_and_the_Never_Land_Pirates>`__.

En juegos de video, esta técnica también se volvió muy popular. Ejemplos
de esto son `Paper Mario <http://en.wikipedia.org/wiki/Super_Paper_Mario>`__
o `Rayman Origins <http://en.wikipedia.org/wiki/Rayman_Origins>`__ .

Cutout en Godot
~~~~~~~~~~~~~~~

Godot provee algunas herramientas para trabajar con este tipo de assets,
pero su diseño en conjunto lo hace ideal para el workflow. La razón es
que, a diferencia de otras herramientas hechas para esto, Godot tiene
las siguiente ventajas:

-  ** El sistema de animación esta completamente integrado con el motor**:
   Esto significa, las animaciones pueden controlar mucho mas que solo el
   movimiento de los objetos, como texturas, tamaño de sprites, pivots,
   opacidad, modulación de color, etc. Todo puede ser animado y mezclado.
-  **Mezcla con tradicional**: AnimatedSprite permite mezclar animación
   tradicional, muy util para objetos complejos, como la forma de las
   manos y pies, cambiar la expresión facial, etc.
-  **Elementos con Formas Personalizadas**: Pueden ser creados con
   :ref:`Polygon2D <class_Polygon2D>` permitiendo la mezcla de animación
   UV, deformaciones, etc.
-  **Sistema de partículas**: Puede ser mezclado con la jerarquía de
   animación tradicional, útil para efectos de magia, jetpacks, etc.
-  **Colisionadores personalizados**: Ajusta los colisionadores e
   influencia áreas en diferentes partes del esqueleto, excelente para
   jefes, juegos de pelea, etc.
-  **Árbol de animación**: Permite combinaciones complejas y mezclas de
   varias animaciones, de la misma forma que funciona en 3D.

Y mucho mas!

Haciendo el GBot!
~~~~~~~~~~~~~~~

Para este tutorial, vamos a usar como contenido del demo las piezas de
el personaje `GBot <https://www.youtube.com/watch?v=S13FrWuBMx4&list=UUckpus81gNin1aV8WSffRKw>`,
creado por Andreas Esau.

.. image:: /img/tuto_cutout_walk.gif

Obtén tus assets: :download:`gbot_resources.zip </files/gbot_resources.zip>`.

Construyendo el rig
~~~~~~~~~~~~~~~~~~~

Crea un Node2D vacío como raíz de la escena, trabajaremos bajo el:

.. image:: /img/tuto_cutout1.png

OK, el primer nodo del modelo que vamos a crear será la cadera(hip).
Generalmente, tanto en 2D como 3D, la cadera es la raíz del esqueleto.
Esto hace mas sencillo animarlo:

.. image:: /img/tuto_cutout2.png

Luego será el torso. El torso necesita ser un hijo de la cadera, así
que crea un sprite hijo y carga el torso, luego acomódalo adecuadamente:

.. image:: /img/tuto_cutout3.png

Esto luce bien. Veamos si nuestra jerarquía trabaja como un esqueleto
al rotar el torso:

.. image:: /img/tutovec_torso1.gif

Ouch, no luce bien! El pivot de rotación esta mal, esto implica que
debe ser ajustado.

Esta pequeña cruz en el medio de el :ref:`Sprite <class_Sprite>` es
el pivot de rotación:

.. image:: /img/tuto_cutout4.png

Ajustando el pivot
~~~~~~~~~~~~~~~~~~

El pivot puede ser ajustado al cambiar la propiedad *offset* en el
Sprite:

.. image:: /img/tuto_cutout5.png

Sin embargo, hay una forma de hacerlo mas visualmente. Mientras flotas
sobre el punto deseado como pivot, simplemente presiona la tecla "v" para
mover el pivot allí para el sprite seleccionado. Alternativamente, hay
una herramienta en la barra de herramientas que tiene una función similar.

.. image:: /img/tutovec_torso2.gif

Ahora luce bien! Continuemos agregando piezas del cuerpo, empezando por
el brazo derecho. Asegúrate de poner los sprites en jerarquía, así sus
rotaciones y traslaciones son relativas al padre:

.. image:: /img/tuto_cutout6.png

Esto parece fácil, asique continua con el brazo derecho. El resto
debería ser simple! O tal vez no:

.. image:: /img/tuto_cutout7.png

Bien. Recuerda tus tutoriales, Luke. En 2D, los nodos padre aparecen
debajo de los nodos hijos. Bueno, esto apesta. Parece que Godot no
soporta rigs cutout después de todo. Vuelve el año próximo, tal vez
para la 3.0.. no espera. Solo bromeaba! Funciona bien.

Pero como puede ser resuelto este problema? Queremos que el brazo
izquierdo aparezca detrás de la cadera y el torso. Para esto, podemos
mover los nodos detrás de la cadera (ten en cuenta que puedes eludir
este paso al ajustar la propiedad Z del Node2D, pero entonces no
aprenderías todo esto!):

.. image:: /img/tuto_cutout8.png

Pero entonces, perdemos el orden de la jerarquia, lo que nos permite
controlar el esqueleto como.. un esqueleto. Hay alguna esperanza?..
Por supuesto!

Nodo RemoteTransform2D
~~~~~~~~~~~~~~~~~~~~~~

Godot provee un nodo especial, :ref:`RemoteTransform2D <class_RemoteTransform2D>`.
Este nodo transformara nodos que están en algún otro lugar en la
jerarquía, al aplicar la transformación en los nodos remotos.

Esto permite tener un orden de visibilidad independiente de la
jerarquía.

Simplemente crea dos nodos mas como hijos del torso, remote_arm_l y
remote_hand_l y vincúlalos a los sprites:

.. image:: /img/tuto_cutout9.png

Mover los nodos de transformación remota hará mover los sprites,
para posar y animar fácilmente al personaje:

.. image:: /img/tutovec_torso4.gif

Completando el esqueleto
~~~~~~~~~~~~~~~~~~~~~~~~

Completa el esqueleto siguiendo los mismos pasos para el resto de las
partes. La escena resultante debería lucir similar a esto:

.. image:: /img/tuto_cutout10.png

El rig resultante será fácil de animar. Al seleccionar los nodos y
rotarlos puedes animarlo eficientemente usando forward kinematics (FK).

Para objetos y rigs simples esto esta bien, sin embargo los siguientes
problemas son comunes:

-  Seleccionar sprites puede volverse difícil para rigs complejos, y
   el árbol de escena termina siendo usado debido a la dificultar de
   hacer clic sobre los sprites adecuados.
-  A menudo es deseable usar Inverse Kinematics (IK) para las
   extremidades.

Para solucionar estos problemas, Godot soporta un método simple de
esqueletos.

Esqueletos
~~~~~~~~~~

Godot en realidad no soporta *verdaderos* esqueletos, pero contiene un
ayudante (helper) para crear "huesos" entre nodos. Esto es suficiente
para la mayoría de los casos, pero la forma como funciona no es
completamente obvia.

Como ejemplo, vamos a volver un esqueleto el brazo derecho. Para
crear esqueletos, una cadena de nodos debe ser seleccionada desde
la cima al fondo:

.. image:: /img/tuto_cutout11.png

Luego, la opción para crear un esqueleto esta localizada en Editar >
Esqueleto > Crear Huesos:

.. image:: /img/tuto_cutout12.png

Esto agregara huesos cubriendo el brazo, pero el resultado no es lo
que esperabas.

.. image:: /img/tuto_cutout13.png

Parece que los huesos fueron desplazados hacia arriba en la jerarquía.
La mano se conecta al brazo, y al brazo al cuerpo. Entonces la pregunta
es:

-  Porque la mano carece de un hueso?
-  Porque el brazo se conecta al cuerpo?

Esto puede ser extraño al comienzo, pero tendrá sentido mas tarde.
En sistemas tradicionales de esqueletos, los huesos tienen una posición,
una orientación y un largo. En Godot, los huesos son mas que nada
ayudantes por lo que conectan al nodo actual con el padre. Por esto,
**alternar un nodo como un hueso solo lo conectara con el padre**.

Así que, con este conocimiento. Hagamos lo mismo nuevamente así tenemos
un esqueleto útil.

El primer paso es crear no nodo endpoint (punto final). Cualquier tipo
de nodo lo hará, pero :ref:`Position2D <class_Position2D>`es preferido
porque será visible en el editor. El nodo endpoint asegurara que el
ultimo nodo tenga orientación.

.. image:: /img/tuto_cutout14.png

Ahora seleccionar la cadena completa, desde el endpoint hasta el brazo
para crear huesos

.. image:: /img/tuto_cutout15.png

El resultado se parece mucho mas a un esqueleto, y ahora el brazo y el
antebrazo puede ser seleccionado y animado.

Finalmente, crea endpoints en todas las extremidades adecuadas y conecta
el esqueleto completo con huesos hasta la cadera:

.. image:: /img/tuto_cutout16.png

Al fin! El esqueleto completo fue rigged! Mirando de cerca, se puede ver
que hay un segundo conjunto de endpoints en las manos. Esto tendrá
sentido pronto.

Ahora que el esqueleto completo fue rigged, el próximo paso es ajustar
IK chains. IK chains permiten un control mas natural de las extremidades.

IK chains (cadenas IK)
~~~~~~~~~

IK chains es una poderosa herramienta de animación. Imagina que quieres
posar el pie de un personaje en una posición especifica en el piso. Sin IK
chains, cada movimiento del pise requerirá rotar y posicionar varios
huesos mas. Esto seria bastante complejo y nos llevaría a resultados
imprecisos.

Y si pudiéramos mover el pie y dejar que el resto de la pierna se ajuste
sola?

Este tipo de pose se llama IK (Inverse Kinematic - Cinematica Inversa).

Para crear una cadena IK, simplemente selecciona una cadena de huesos
desde el endpoint hasta la base de la cadena. Por ejemplo, para crear
una cadena IK para la pierna derecha, selecciona lo siguiente:

.. image:: /img/tuto_cutout17.png

Para habilitar esta cadena para IK. Ve a Editar > Esqueleto > Crear
Cadena IK.

.. image:: /img/tuto_cutout18.png

Como resultado, la base de la cadena se volverá *Amarilla*

.. image:: /img/tuto_cutout19.png

Una vez que la cadena IK ha sido configurada, simplemente toma cualquiera
de los huesos en la extremidades, cualquier hijo o nieto de la base de la
cadena y trata de moverlo. El resultado será placentero, satisfacción
garantizada!

.. image:: /img/tutovec_torso5.gif

Animación
~~~~~~~~~

La siguiente sección será una colección de consejos para crear
animaciones para tus rigs. Si no esta seguro sobre como funciona
el sistema de animación en godot, refréscalo chequeando nuevamente
:ref:`doc_animations`.

Animación 2D
------------

Cuando hagas animación en 2D, un ayudante estará presente en el menú
superior. Este ayudante solo aparece cuando la ventana del editor de
animación este abierta:

.. image:: /img/tuto_cutout20.png

El botón de llave insertara keyframes de posicion(loc)/rotación(rot)/
escala(scl) a los objetos o huesos seleccionados. Esto depende en la
mascara habilitada. Los ítems verdes insertaran claves mientras que
los rojos no, asique modifica la mascara de inserción para tu preferencia.

Pose de descanso
~~~~~~~~~~~~~~~~

Este tipo de rigs no tiene una pose de "descanso", así que es recomendado
crear una pose de descanso de referencia en una de las animaciones.

Simplemente sigue los siguientes pasos.

1. Asegúrate que el rig este en "descanso" (sin hacer ninguna pose especifica).

2. Crea una nueva animación, renómbrala a "descanso".

3. Selecciona todos los nodos (la selección de caja debería funcionar bien)

4. Selecciona "loc" y "rot" en el menú superior.

5. Presiona el botón de llave. Las llaves serán insertadas para todo,
   creando una pose por defecto.

.. image:: /img/tuto_cutout21.png

Rotación
~~~~~~~~

Animar estos modelos significa solo modificar la rotación de los nodos.
Lugar y escala raramente son usados, con la única excepción de
mover el rig entero desde la cadera (la cual es el nodo raíz).

Como resultado, cuando insertas claves, solo el botón "rot" necesita
ser presionado la mayoría del tiempo:


.. image:: /img/tuto_cutout22.png

Esto evitara la creación de pistas extra de animación para la posición
que no seran utilizadas.

Keyframing IK
~~~~~~~~~~~~~

Cuando se editan cadenas IK, no es necesario seleccionar la cadena entera
para agregar keyframes. Seleccionar el endpoint de la cadena e insertar
un keyframe también insertara automáticamente keyframes hasta la base de
de la cadena. Esto hace la tarea de animar extremidades mucho mas
simple.

Moviendo sprites por delante y detrás de otros.
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

RemoteTransform2D trabaja en la mayoría de los casos, pero a veces es
realmente necesario tener un nodo encima y debajo de otros durante
una animación. Para ayudar con esto existe la propiedad "Behind Parent"
en cualquier Node2D:

.. image:: /img/tuto_cutout23.png

Ajustes de transición de curvas por lotes
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

Cuando se crean animaciones realmente complejas y se insertan muchos
keyframes (cuadros clave), editar las curvas individuales de los keyframes
puede volverse una tarea interminable. Para esto, el Editor de Animación
tiene un pequeño menú donde es fácil cambiar todas las curvas. Solo
selecciona cada uno de los keyframes y (generalmente) aplica la curva
de transición "Out-In" para una animación suave:

.. image:: /img/tuto_cutout24.png
