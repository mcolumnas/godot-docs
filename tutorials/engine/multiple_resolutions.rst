.. _doc_multiple_resolutions:

Resoluciones múltiples
======================

Resolución base
---------------

Una resolución de pantalla base para el proyecto puede ser especificada
en configuración de proyecto.

.. image:: /img/screenres.png

Sin embargo, lo que hace no es completamente obvio. Cuando corre en PC,
el motor intentara ajustara esta resolución (o usar algo mas chico si
falla). En equipos móviles, consolas u otros dispositivos con una
resolución fija o renderizacion de pantalla completa, esta resolución
será ignorada y la resolución nativa se usara en su lugar. Para
compensar esto, Godot ofrece muchas formas de controlar como la pantalla
va a cambiar de tamaño y estirarse a diferentes tamaños de pantalla.

Resizing (Cambiar de tamaño)
-----------------

Hay varios tipos de dispositivos, con varios tipos de pantalla, los
cuales a su vez tienen diferente densidad de pixels y resolución.
Manejarlos todos puede ser un montón de trabajo, asique Godot trata
de hacer la vida del desarrollador un poco mas fácil. El nodo
:ref:`Viewport <class_Viewport>` tiene varias funciones para manejar
el cambio de tamaño, y el nodo root de la escena es siempre un viewport
(las escenas cargadas son instanciadas como hijos de el, y siempre se
pueden acceder llamando ``get_tree().get_root()`` o ``get_node("/root")``).

En cualquier caso, mientras cambiar los parámetros del viewport root es
probablemente la forma mas flexible de atender este problema, puede ser
un montón de trabajo, código y adivinanza, por lo que Godot provee un
rango de parámetros simple en la configuración de proyecto para manejar
múltiples resoluciones.

Configuraciones de estiramiento
-------------------------------

Las configuraciones de estiramiento están localizadas en la
configuración de proyecto, es solo un montón de variables de
configuración que proveen varias opciones:

.. image:: /img/stretchsettings.png

Modo de estiramiento
--------------------

-  **Disabled**: Lo primero es el modo de estiramiento. Por defecto
   esta deshabilitado, lo que significa que no hay estiramiento (cuanto
   mas grande la pantalla o ventana, mas grande la resolución, siempre
   igualando pixeles 1:1).
-  **2D**: En este modo, la resolución especificada en display/width y
   display/height en la configuración del proyecto será estirada para
   cubrir la pantalla entera. Esto implica que 3D no sera afectado (
   solo va a renderizar a una resolución mas alta) y 2D también será
   renderizado a una resolución mayor, solo que agrandada.
-  **Viewport**: El escalado de viewport es diferente, el :ref:`Viewport <class_Viewport>`
   root se ajusta como un render target, y aun renderiza precisamente
   a la resolución especificada en la sección ``display/`` de la
   configuración de proyecto. Finalmente, este viewport es copiado y
   escalado para entrar a la pantalla. Este modo es útil cuando trabajas
   con juegos que requieren precisión de pixeles, o solo con el motivo
   de renderizar a una resolución mas baja para mejorar la performance.

.. image:: /img/stretch.png

Aspecto de estiramiento
-----------------------

-  **Ignore**: Ignorar la relación de aspecto cuando se estire la
   pantalla. Esto significa que la resolución original será estirada
   para entrar en la nueva, aun cuando sea mas ancha o mas angosta.
-  **Keep**: Mantener la relación de aspecto cuando se estire la
   pantalla. Esto implica que la resolución original será mantenida
   cuando se ajuste a una nueva, y barras negras serán agregadas a los
   costados o borde superior e inferior de la pantalla.
-  **Keep Width**: Mantener la relación de aspecto cuando se estira
   la pantalla, pero si la resolución de pantalla es mas alta que la
   resolución especificada, será estirada verticalmente (y una mayor
   resolución vertical será reportada en el viewport, proporcionalmente)
   Esta es usualmente la mejor opción para crear GUIs o HUDs que
   se escalan, asi algunos controles pueden ser anclados hacia abajo
   (:ref:`doc_size_and_anchors`).
-  **Keep Height**: Mantener la relación de aspecto cuando se estira
   la pantalla, pero si la pantalla resultante es mas ancha que la
   resolución especificada, será estirada horizontalmente (y mas
   resoluciones horizontales serán reportadas en el viewport,
   proporcionalmente). Esta es usualmente la mejor opción para juegos
   2D que tienen scroll(desplazamiento) horizontal (como juegos de
   plataforma)
