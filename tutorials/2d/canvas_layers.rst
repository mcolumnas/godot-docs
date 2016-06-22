.. _doc_canvas_layers:

Capas Canvas
============

Viewport e ítems Canvas
-----------------------

Los nodos 2D regulares, como :ref:`Node2D <class_Node2D>`o
:ref:`Control <class_Control>` heredan desde :ref:`CanvasItem <class_CanvasItem>`,
que es la base para todos los nodos 2D. CanvasItems pueden ser
organizados en arboles y heredaran sus transformaciones. Esto implica
que cuando mueves al padre, el hijo se moverá también.

Estos nodos son puestos como hijos directos o indirectos de un
:ref:`Viewport <class_Viewport>`, y serán mostrados a través de el.

Viewport tiene una propiedad "canvas_transform"
:ref:`Viewport.set_canvas_transform() <class_Viewport_set_canvas_transform>`,
que permite transformar toda la jerarquía CanvasItem por una
transformación :ref:`Matrix32 <class_Matrix32>` personalizada. Los nodos
como :ref:`Camera2D <class_Camera2D>`, trabajan cambiando dicha
transformación.

Cambiar la transformación de canvas es útil porque es mucho mas
eficiente que mover que mover el ítem canvas raíz (y por lo tanto toda
la escena). Canvas transform es una matriz simple que afecta al dibujo
2D por completo, por lo que es la forma mas eficiente de hacer
scrolling.


No es suficiente...
-------------

Pero esto no es suficiente. A menudo hay situaciones donde el juego o
aplicación puede no querer *todo* transformado por la transformación
canvas. Ejemplos de esto son:

-  **Fondos Parallax**: Los fondos que se mueven mas lento que el resto
   del nivel.
-  **HUD**: Visualización Head's up, o interfaz de usuario. Si el mundo
   se mueve, el contador de vida, puntaje, etc. debe mantenerse estático.
-  **Transiciones**: Efectos usados para transiciones (fades, blends)
   pueden también querer mantenerse en una posición fija.

Como pueden estos problemas ser resueltos en un único árbol de escena?

CanvasLayers
------------

La respuesta es :ref:`CanvasLayer <class_CanvasLayer>`, que es un nodo
que agrega una capa 2D de renderizacion 2D separada para todos sus hijos
y nietos. Los hijos del viewport van a mostrarse por defecto en el layer
"0", mientras un CanvasLayer va a dibujarse en cualquier numero
(profundidad) de capa. Las capas con un numero mayor serán dibujados por
encima de los que tienen menor numero. CanvasLayers también tiene su
propia transformación, y no depende de la transformación de las otras
capas. Esto permite que la UI este fija en su lugar, mientras el mundo
se mueve.

Un ejemplo de esto es crear un fondo parallax. Esto puede ser hecho con
un CanvasLayer en la capa "-1". La pantalla con los puntos, contador
de vida y botón de pausa pueden también ser creada en la capa "1".

Aquí un diagrama de como funciona:

.. image:: /img/canvaslayers.png

Los CanvasLayers son independientes del orden de árbol, y solo dependen
de su numero de capa, por lo que pueden ser instanciados cuando se
precisan.

Rendimiento
-----------

Aunque no debería haber ninguna limitación de rendimiento, no es
recomendado usar una excesiva cantidad de layers para acomodar el
orden de dibujado de los nodos. La forma mas optima siempre será
acomodarlos por orden de árbol. Los nodos 2D también tienen una
propiedad para controlar su orden de dibujado (see :ref:`Node2D.set_z() <class_Node2D_set_z>`).
