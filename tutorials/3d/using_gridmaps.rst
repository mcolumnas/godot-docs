.. _doc_using_gridmaps:

Usando gridmaps
~~~~~~~~~~~~~~~

Introducción
------------

Los :ref:`Gridmaps <class_GridMap>` son una forma simple y rápida de
crear niveles para juegos 3D. Piensa de ellos como una versión 3D de los
nodos :ref:`TileMap<doc_using_tilemaps>`. De forma similar, empiezas con
una librería predefinida de mallas 3D que pueden ser puestas en un grid,
justo como si estuvieras haciendo un nivel con una cantidad ilimitada de
bloques Lego.

Las colisiones también pueden ser agregadas a las mallas, como harías
con los mosaicos de un tilemap.

Creando un MeshLibrary
----------------------

Para empezar, necesitas un :ref:`class_MeshLibrary`, el cual es una
colección de mallas que pueden ser usadas en un gridmap. Aquí hay
algunas mallas que puedes usar para configurarlo.

.. image:: /img/meshes.png

Abre una nueva escena y crea el nodo raíz (esto es importante, ya que
sin nodo raíz, no será capaz de generar el MeshLibrary!). Luego, crea
un nodo :ref:`class_MeshInstance`:

.. image:: /img/mesh_meshlib.png

Si no necesitas aplicar física a los bloques de construcción, eso es
todo lo que tienes que hacer. En la mayoría de los casos sin embargo,
vas a necesitar que tu bloque genere colisiones, así que veamos como
agregarlas.

Colisiones
----------

Para asignar una :ref:`class_CollisionShape` y :ref:`class_PhysicsBody`
a las mallas, la forma más simple es hacerlo mientras creas el
MeshLibrary. De forma alternativa, también puedes editar una
MeshLibrary existente desde dentro del inspector de GridMap, pero solo
CollisionShapes pueden ser definidos allí y no PhysicsBody.

Para darle a las mallas un CollisionShape, simplemente agrega nodos hijos
al nodo MeshInstance. Típicamente se agregan los PyhisicsBody y
CollisionShape necesarios en este orden:


.. image:: /img/collide_mesh_meshlib.png

Puedes ajustar el orden de acuerdo a tus necesidades.

Exportando la MeshLibrary
-------------------------

Para exportar, ve a ``Escena > Convertir a.. > MeshLibrary..``. y la
guardas como un recurso.

.. image:: /img/export_meshlib.png

Ahora estás listo para usar el nodo GridMap.

Usando MeshLibrary en un GridMap
--------------------------------

Crea una nueva escena usando cualquier nodo como raíz, luego agrega un
nodo GridMap. Despues, carga el MeshLibrary que recién exportaste.

.. image:: /img/load_meshlib.png

Ahora, puedes construir tu propio nivel como te parezca mejor. Usa clic
izquierdo para agregar mosaicos y botón derecho para quitarlos. Puedes
ajustar el nivel del piso cuando necesitas poner mallas en alturas
específicas.


.. image:: /img/gridmap.png

Como mencionamos arriba, también puedes definir nuevas CollisionShapes
en esta etapa siguiendo estos pasos:

.. image:: /img/load_collisionshape.png

Lo lograste!

Recordatorio
------------

-  Se cuidadoso antes de escalar mallas si no estás usando mallas
   uniformes.
-  Hay muchas formas de usar gridmaps, se creativo!
