.. _doc_introduction_to_3d:

Introducción a 3D
=================

Crear un juego 3D puede ser desafiante. La coordenada Z extra hace que
muchas de las técnicas comunes que ayudan a hacer juegos 2D simplemente
no sirvan mas. Para ayudar en esta transición, vale mencionar que Godot
usa APIs muy similares para 2D y 3D. La mayoría de los nodos son iguales
y están presentes tanto en versiones 2D como 3D. De hecho, es
recomendable chequear el tutorial del platformer 3D, o los tutoriales de
kinematic 3D, los cuales son casi idénticos a su contraparte 2D.

En 3D, la matemática es un poco mas compleja que en 2D, por lo que
chequear :ref:`doc_vector_math`(que fue creado especialmente para
desarrolladores de juegos, no matemáticos ni ingenieros) ayudara a
cementar el camino hacia el desarrollo de juegos 3D eficientes.

Nodo Spatial
~~~~~~~~~~~~

:ref:`Node2D <class_Node2D>` es el nodo base para 2D.
:ref:`Control <class_Control>` es el nodo base para todo lo relacionado
con GUI. Siguiendo este razonamiento, el motor 3D usa el nodo
:ref:`Spatial <class_Spatial>` para todo lo que sea 3D.

.. image:: /img/tuto_3d1.png

Los nodos Spatial tienen transformaciones locales, las cuales son
relativas al nodo padre (en la medida que el nodo padre también sea o
**herede** el tipo Spatial). Esta transformación puede ser accedida como
un :ref:`Transform <class_Transform>` 4x3, o como 3 miembros
:ref:`Vector3 <class_Vector3>` representando posición, rotación Euler
(ángulos x, y, z) y escala.

.. image:: /img/tuto_3d2.png

Contenido 3D
~~~~~~~~~~~~

A diferencia de 2D, donde cargar contenido de imágenes y dibujar es
sencillo, en 3D se vuelve mas difícil. El contenido necesita ser creado
con herramientas 3D especiales (generalmente referidas como DCCs) y
exportadas hacia un formato de archivo de intercambio para ser importadas
en Godot (los formatos 3D no son tan estandarizados como las imágenes).

Modelos creados por DCC
-----------------------

Hay dos pipelines (conductos) para importar modelos 3D en Godot. La
primera y mas común es a través del importador :ref:`doc_importing_3d_scenes`,
el cual permite importar escenas enteras (como lucen en el DCC),
incluyendo animación, skeletal rigs, blend shapes, etc.

El segundo pipeline es a través del importador :ref:`doc_importing_3d_meshes`.
Este segundo método permite importar archivos simples .OBJ como recursos
mesh (malla), los cuales luego pueden ser puestos dentro de un nodo
:ref:`MeshInstance <class_MeshInstance>` para visualización.

Geometría generada
------------------

Es posible crear geometría personalizada usando el recurso
:ref:`Mesh <class_Mesh>` directamente, solo crea tus arreglos y
usa la función :ref:`Mesh.add_surface() <class_Mesh_add_surface>`,
que provee una API mas directa y ayudantes para indexar, generar
normales, tangentes, etc.

En todo caso, este método esta destinado para generar geometráa estática
(modelos que no serán actualizados a menudo), ya que crear arreglos
de vértices y enviarlos a la API 3D tiene un costo de rendimiento
significativo.

Geometría Inmediata
-------------------

Si, por otro lado, hay un requerimiento de generar geometría simple que
va a ser actualizada a menudo, Godot provee un nodo especial,
:ref:`ImmediateGeometry <class_ImmediateGeometry>` que provee una
API immediate-mode de estilo OpenGL 1.x para crear puntos, líneas
triángulos, etc.

2D en 3D
--------

Aunque Godot provee un motor 2D poderoso, muchos tipos de juegos usan
2D en un entorno 3D. Al usar una cámara fija (ortogonal o perspectiva)
que no rota, nodos como :ref:`Sprite3D <class_Sprite3D>` y
:ref:`AnimatedSprite3D <class_AnimatedSprite3D>` pueden ser usados
para crear juegos 2D que toman ventaja de la mezcla con fondos 3D,
parallax mas realista, efectos de luz y sombra, etc.

La desventaja es, por supuesto, esa complejidad agregada y un
rendimiento reducido en comparación con 2D puro, así como la falta de
referencia de trabajar en pixels.

Entorno
~~~~~~~

Mas allá de editar una escena, es a menudo común editar el entorno.
Godot provee un nodo :ref:`WorldEnvironment <class_WorldEnvironment>`
que permite cambiar el color de fondo, modo (como en, poner un skybox),
y aplicar varios tipos de efectos post-processing incorporados.
Los entornos también  pueden ser sobrescritos en la Cámara.

Viewport 3D
~~~~~~~~~~~

Editar escenas 3D es hecho en la pestaña 3D. Esta pestaña puede ser
seleccionada manualmente, pero será automáticamente habilitada cuando
se selecciona un nodo Spatial.

.. image:: /img/tuto_3d3.png

Los controles por defectos para la navegación de escena 3D es similar
a Blender (apuntando a tener algún tipo de consistencia en el pipeline
de software libre..), pero hay opciones incluidas para personalizar
los botones de mouse y el comportamiento para ser similar a otras
herramientas en la Configuración del Editor:

.. image:: /img/tuto_3d4.png

Sistema de coordenadas
----------------------

Godot usa el sistema `métrico <http://en.wikipedia.org/wiki/Metric_system>`__
para todo. La física 3D y otras áreas estas ajustadas para esto, así
que intentar usar una escala diferente suele ser una mala idea (a no ser
que sepas lo que estas haciendo).

Cuando se trabaja con assets 3D, siempre es mejor trabajar en la escala
correcta (ajusta tu DCC a métrico). Godot permite escalar luego de
importar y, mientras que funciona en la mayoría de los casos, en
situaciones raras puede introducir problemas de precisión de punto
flotante (y por lo tanto, glitches y artefactos) en áreas delicadas
como renderizacion o física. Asique, asegurate que tus artistas siempre
trabajen en la escala correcta!

La coordenada Y es usada para "arriba", aunque en la mayoría de los
objetos que requieren alineación (como luces, cámaras, colisionadores de
capsula, vehículos, etc.), el eje Z es usado como la dirección "hacia
donde apuntan". Esta convención a grandes líneas implica que:

-  **X** son los lados
-  **Y** es arriba/abajo
-  **Z** es adelante/atras

Espacio y manipulación de gizmos
--------------------------------

Mover objetos en la vista 3D es hecho a través de gizmos de manipulación.
Cada eje es representado por un color: Rojo, Verde, Azul representa X,
Y, Z respectivamente. Esta convención se aplica a la grilla y otros gizmos
también (ademas en el lenguaje de shader, orden de componentes para
Vector3, Color, etc.).

.. image:: /img/tuto_3d5.png

Algunos a atajos de teclado útiles:

-  Para aplicar snap a movimiento o rotación, presiona la tecla "s"
   mientras mueves, escalas o rotas.
-  Para centrar la vista en el objeto seleccionado, presiona la tecla "f".

Menú de vista
-------------

Las opciones de vista con controladas por el menú vista. Presta atención
a este pequeño menú dentro de la ventana porque a menudo es pasado por
alto!

.. image:: /img/tuto_3d6.png

Iluminación por defecto
-----------------------

La vista 3D tiene algunas opciones por defecto en iluminación:

-  Hay una luz direccional que vuelve los objetos visibles mientras
   se esta editando por defecto. No es visible cuando se corre el juego.
-  Hay una luz sutil de entorno por defecto para iluminar lugares donde
   no llega luz y que permanezcan visibles. Tampoco es visible cuando
   se corre el juego (y cuando la luz por defecto esta apagada).

Estas pueden ser apagadas al alternar la opción "Usar luz por defecto":

.. image:: /img/tuto_3d8.png

Personalizar esto (y otros valores de vista por defecto) también es
posible desde el menú de configuración:

.. image:: /img/tuto_3d7.png

Que abre esta ventana, permitiendo personalizar el color de la luz
ambiente y la dirección de la luz por defecto

.. image:: /img/tuto_3d9.png

Camarás
-------

No importa cuantos objetos son ubicados en el espacio 3D, nada va a
ser mostrado a no ser que :ref:`Camera <class_Camera>` también este
agregado a la escena. Las cámaras pueden trabajar ya sea en proyección
ortogonal o perspectiva:

.. image:: /img/tuto_3d10.png

Las cámaras están asociadas y solo despliegan hacia un viewport padre o
abuelo. Ya que la raíz del árbol de escena es un viewport, las cámaras
van a mostrarse en el por defecto, pero si se desean sub_viewports (tanto
como render targets o picture-in-picture), necesitan sus propias cámaras
hijo para desplegar.

.. image:: /img/tuto_3d11.png

Cuando se manejen muchas cámaras, las siguientes reglas se siguen para
cada viewport:

-  Si no hay cámaras presentes en el árbol de escena, la primera que
   entre se volverá la cámara activa. Las siguientes cámaras que entren
   a la escena serán ignoradas (a no ser que se ajusten a *current*).
-  Si una cámara tiene la propiedad *current* habilitada, será usada
   no importa si existen otras cámaras en la escena. Si la propiedad se
   ajusta, se volverá activa, reemplazando la cámara previa.
-  Si una cámara activa deja el árbol de escena, la primer cámara en el
   orden de árbol tomara su lugar.

Luces
------

No hay limitacion en el numero de luces ni de los tipos de luces en
Godot. Tantas como se quieran pueden ser agregadas (mientras que el
rendimiento lo permita). Los Shadow maps son, sin embargo, limitados.
Cuanto mas se usan, menor es la calidad total.

Es posible usar :ref:`doc_light_baking`, para evitar usar una gran
cantidad de luces en tiempo real y mejorar el rendimiento.
