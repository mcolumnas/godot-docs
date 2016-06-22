.. _doc_canvas_layers:

Capas Canvas
============

Viewport e items Canvas
-----------------------

Los nodos 2D regulares, como :ref:`Node2D <class_Node2D>`o
:ref:`Control <class_Control>` heredan desde :ref:`CanvasItem <class_CanvasItem>`,
que es la base para todos los nodos 2D. CanvasItems pueden ser
organizados en arboles y heredaran sus tranformaciones. Esto implica
que cuando mueves al padre, el hijo se movera tambien.

Estos nodos son puestos como hijos directos o indirectos de un
:ref:`Viewport <class_Viewport>`, y seran mostrados a traves de el.

Viewport tiene una propiedad "canvas_transform"
:ref:`Viewport.set_canvas_transform() <class_Viewport_set_canvas_transform>`,
que permite transformar toda la jerarquia CanvasItem por una
transformacion :ref:`Matrix32 <class_Matrix32>` personalizada. Los nodos
como :ref:`Camera2D <class_Camera2D>`, trabajan cambiando dicha
transformacion.

Cambiar la transformacion de canvas es util porque es mucho mas
eficiente que mover que mover el item canvas raiz (y por lo tanto toda
la escena). Canvas transform es una matriz simple que afecta al dibujo
2D por completo, por lo que es la forma mas eficiente de hacer
scrolling.


No es suficiente...
-------------

Pero esto no es suficiente. A menudo hay situaciones donde el juego o
aplicacion puede no querer *todo* transformado por la transformacion
canvas. Ejemplos de esto son:

-  **Fondos Parallax**: Los fondos que se mueven mas lento que el resto
   del nivel.
-  **HUD**: visualizacion Head's up, o interfaz de usuario. Si el mundo
   se mueve, el contador de vida, puntaje, etc. debe mantenerse estatico.
-  **Transiciones**: Efectos usados para transiciones (fades, blends)
   pueden tambien querer mantenerse en una posicion fija.

Como pueden estos problemas ser resueltos en un unico arbol de escena?

CanvasLayers
------------

La respuesta es :ref:`CanvasLayer <class_CanvasLayer>`, que es un nodo
que agrega una capa 2D de renderizacion 2D separada para todos sus hijos
y nietos. Los hijos del viewport van a mostrarse por defecto en el layer
"0", mientras un CanvasLayer va a dibujarse en cualquier numero
(profundidad) de capa. Las capas con un numero mayor seran dibujados por
encima de los que tienen menor numero. CanvasLayers tambien tiene su
propia transformacion, y no depende de la transformacion de las otras
capas. Esto permite que la UI este fija en su lugar, mientras el mundo
se mueve.

Un ejemplo de esto es crear un fondo parallax. Esto puede ser hecho con
un CanvasLayer en la capa "-1". La pantalla con los puntos, contador
de vida y boton de pausa pueden tambien ser creada en la capa "1".

Aqui un diagrama de como funciona:

.. image:: /img/canvaslayers.png

Los CanvasLayers son indepentiendes del orden de arbol, y solo dependen
de su numero de capa, por lo que pueden ser instanciados cuando se
precisan.

Rendimiento
-----------

Aunque no deberia haber ninguna limitacion de rendimiento, no es
recomendado usar una excesiva cantidad de layers para acomodar el
orden de dibujado de los nodos. La forma mas optima siempre sera
acomodarlos por orden de arbol. Los nodos 2D tambien tienen una
propiedad para controlar su orden de dibujado (see :ref:`Node2D.set_z() <class_Node2D_set_z>`).
