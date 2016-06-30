.. _doc_using_tilemaps:

Usando Tilemaps
~~~~~~~~~~~~~~~

Introducción
------------

Los Tilemaps son una forma simple y rapida de hacer niveles para juegos
2D. Basicamente, empiezas con un monton de tiles (baldosas, piezas) de
referencia que pueden ser puestas en una grilla, tantas veces como se
desee:

.. image:: /img/tilemap.png

Las colisiones tambien pueden ser agregadas a los tiles, permitiendo
tanto juegos de desplazamiento lateral (side scrolling) o con vista
desde arriba (top down).

Haciendo un tileset
-------------------

Para empezar, un tileset tiene que ser hecho. Aqui hay algunos tiles
para ello. Estan todos en la misma imagen porque los artistas a menudo
preferien esto. Tenerlos como imagenes separadas tambien funciona.

.. image:: /img/tileset.png

Crea un nuevo proyecto y mueve la imagen png superior a su directorio.

Estaremos creando un recurso :ref:`TileSet <class_TileSet>`.
Mientras que este recurso exporta propiedades, es bastante dificil
ingresarle datos complejos y mantenerlo:

.. image:: /img/tileset_edit_resource.png

Hay suficientes propiedades para arreglarselas, y con algo de esfuerzo
editando de esta forma puede funcionar, pero la forma mas simple de
editar y mantener un tileset es con la herramienta de exportar!

Escena TileSet
--------------

Crea una nueva escena con un nodo regulgar o Node2D como raiz. Para cada
tile, agrega un sprite como hijo. Ya que los tiles aqui son de 50x50,
habilitar snap puede ser una buena idea (Editar > Usar Snap, Mostrar
Grilla y en Configurar Snap, Step Grilla 50 y 50).

Si mas de un tile esta presente en la imagen de origen, asegurate de
usar la propiedad region del sprite para ajustar cual parte de la textura
sera usada.

Finalmente, asegurate de ponerle un nombre correcto a tu sprite, esto
para que, en ediciones posteriores al tileset (por ejemplo, si se agrega
colision, se cambia region, etc), el tile igual sera **identificado
correctamente y actualizado**. Este nombre debe ser unico.

Suena como un monton de requerimientos, asique aqui hay un screenshot
que muestra donde esta todo lo importante:

.. image:: /img/tile_example.png

Continua agregando todos los tiles, ajustando los offsets si es necesario
(si tiene multiples tiles en una sola imagen). De nuevo, recuerda que sis
nombres deben ser unicos.

.. image:: /img/tile_example2.png

Colision
---------

Para agregar colision a un tile, crea un hijo StaticBody2D para cada
sprite. Este es un nodo de colision estatica. Luego, como hijo de
StaticBody2D, crea una CollisionShape2D o CollisionPolygon. La ultima
es recomendada por ser mas sencilla de editar:

.. image:: /img/tile_example3.png

Finalmente, edita el poligono, esto le dara una colision al tile.
**Recuerda usar snap!**. Usando snap nos aseguramos que los poligonos
de colisiones estan alineados correctamente, permitiendo al personaje
caminar sin problema de tile a tile. Ademas **no escales o muevas** la
colision y/o los nodos de poligonos. Dejalos con offset 0,0, con escala
1,1 y rotacion 0 respecto al sprite padre.

.. image:: /img/tile_example4.png

Sigue agregando colisiones a los tiles hasta que este pronto. Ten en
cuenta que BG es solo un fondo, por lo que no debe tener colision.

.. image:: /img/tile_example5.png

OK! Esta pronto! Recuerdo guardar la escena para ediciones futuras,
llamala "tileset_edit.scn" o algo similar.

Exportando un TileSet
-------------------

Con la escena creada y abierta en el editor, el siguiente paso sera
crear el tileset. Usa Escena > Convertir A > TileSet desde el menu
Escena:

.. image:: /img/tileset_export.png

Luego elije el nombre de archivo, como "mistiles.res". Asegurate que
la opcion "Mergear con existentes" esta habilitada. De esta forma, cada
vez que el recurso de tileset es sobrescrito, los tiles existentes seran
mezclados y actualizados (son referenciados por su nombre unico, asi que
nuevamente, **nombre tus tiles adecuadamente**).

.. image:: /img/tileset_merge.png

Usando el TileSet en un TileMap
-------------------------------

Crea una nueva escena, usa cualquier nodo o node2d como raiz, luego
crea un :ref:`TileMap <class_TileMap>` como hijo.

.. image:: /img/tilemap_scene.png

Ve hasta la propiedad tileset de este nodo y asigna el que creamos en
los pasos previos:

.. image:: /img/tileset_property.png

Tambien ajusta el tamaño de cell (celda) a '50', ya que ese es el valor
usado por los tiles. Quadrant size es un valor de puesta a punto, que
significa que el motor va a dibujar y escoger el tilempa en bloques
de baldosas de 16x16. Esta valor suele estar bien y no necesita ser
cambiado, pero puede ser usado para poner a punto el rendimiento
en casos especificos (si sabes lo q estas haciendo).

Pintando tu mundo
-----------------

Con todo pronto, asegurate que el nodo TileMap esta seleccionado. Una
grilla roja va a aparecer en la pantalla, permitiendo pintar en ella con
el tile seleccionado en la paleta izquierda.

.. image:: /img/tile_example6.png

Para evitar mover y seleccionar el nodo TileMap accidentalmente (algo
comun ya que es un nodo gigante), es recomendable que lo bloquees, usando
el boton de candado:

.. image:: /img/tile_lock.png

Offset y artefactos de escala
-----------------------------

Cuando usas una sola textura para todos los tiles, escalando el tileset
(o aun moverlo a un lugar que no esta alineado en pixels) probablemente
resultara en artefactos de filtrado como este:

.. image:: /img/tileset_filter.png

Esto no puede ser evitado, ya que es como funciona el filtro bilinear
por hardware. Entonces, para evitar esta situacion, hay algunas soluciones,
intenta la que te parezca mejor:

-  Usa una sola imagen para cada tile, esto removera los artefactos pero
   puede ser pesado de implementar, asi que intenta las opciones que estan
   abajo primero.
-  Deshabilita el filtrado ya sea para la textura del tileset o el
   cargador de imagenes entero (ve el tutorial sobre pipeline de assets
   :ref:`doc_managing_image_files`).
-  Habilita pixel snap (ajusta: "Escena > Configuracion de Proyecto" >
   Display/use_2d_pixel_snap").
-  Escalado de Viewport puede a menudo ayudar para encoger el mapa (ve
   el tutorial :ref:`doc_viewports`).
