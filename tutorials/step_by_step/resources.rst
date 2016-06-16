.. _doc_resources:

Recursos
========

Nodos y recursos
----------------

Hasta ahora, los Nodos (:ref:`Nodes <class_Node>`) han sido el tipo
de datos mas importante en Godot, ya que la mayoria de los
comportamientos y caracteristicas del motor estan implementadas a
traves de estos. Hay, sin embargo, otro tipo de datos que es igual
de importante. Son los recursos (:ref:`Resource <class_Resource>`).

Mientras que los *Nodos* se enfocan en comportamiento, como dibujar
un sprite, dibujar un modelo 3d, fisica, controles GUI, etc,
los **Recursos** con meros **contenedores de datos**. Esto implica
que no realizan ninguna accion ni procesan informacion. Los recursos
solo contienen datos.

Ejemplos de recursos:
:ref:`Texture <class_Texture>`,
:ref:`Script <class_Script>`,
:ref:`Mesh <class_Mesh>`,
:ref:`Animation <class_Animation>`,
:ref:`Sample <class_Sample>`,
:ref:`AudioStream <class_AudioStream>`,
:ref:`Font <class_Font>`,
:ref:`Translation <class_Translation>`,
etc.

Cuando Godot guarda o carga (desde disco) una escena (.scn o .xml),
una imagen (png, jpg), un script (.gd) o basicamente cualquier cosa,
ese archivo es considerado un recurso.

Cuando un recurso es cargado desde disco, **siempre es cargado una
sola vez**.  Esto significa, que si hay una copia del recurso ya
cargada en memoria, tratar de leer el recurso nuevamente va a
devolver la misma copia una y otra vez. Esto debido al hecho de que
los recursos son solo contenedores de datos, por lo que no hay
necesidad de tenerlos duplicados.

Tipicamente, cada objeto en Godot (Nodo, Recurso, o cualquier cosa)
puede exportar propiedades, las cuales pueden ser de muchos tipos
(como un string o cadena, integer o numero entero, Vector2 o vector
de 2 dimensiones, etc). y uno de esos tipos puede ser un recurso.
Esto implica que ambos nodos y recursos pueden contener recursos
como propiedades. Para hacerlo un poco mas visual:

.. image:: /img/nodes_resources.png

Externo vs Incorporado (Built-in)
---------------------

Las propiedades de los recursos pueden referenciar recursos de dos
maneras, *externos* (en disco) o **incorporados**.

Para ser mas especifico, aqui hay una :ref:`Texture <class_Texture>`
en un nodo :ref:`Sprite <class_Sprite>`:

.. image:: /img/spriteprop.png

Presionando el boton ">" a la derecha de la vista previa, permite ver
y editar las propiedades del recurso. Una de las propiedades (path)
muestra de donde vino. En este caso, desde una imagen png.

.. image:: /img/resourcerobi.png

Cuando el recurso proviene de un archivo, es considerado un recurso
*externo*. Si el la propiedad camino es borrada (o nunca tuvo), es
considerado un recurso incorporado.

Por ejemplo, si el camino \`"res://robi.png"\` es borrado de la
propiedad path en el ejemplo superior, y la escena es guardada, el
recurso sera guardado dentro del archivo de escena .scn, ya no
referenciando externamente a "robi.png". Sin embargo, aun si esta
guardado de forma incorporada, y aunque la escena puede ser
instanciada muchas veces, el recurso se seguira leyendo siempre una
vez. Eso significa que diferentes escenas del robot Robi que sean
instanciadas al mismo tiempo conservaran la misma imagen.

Cargando recursos desde codigo
------------------------------

Cargar recursos desde codigo es facil, hay dos maneras de hacerlo. La
primera es usar load(), asi:

::

    func _ready():
            var res = load("res://robi.png") # el recurso es cargado cuando esta linea se ejecuta
            get_node("sprite").set_texture(res)

La segunda forma es mas optima, pero solo funciona con un parametro
de cadena constante, porque carga el recurso en tiempo de compilacion.

::

    func _ready():
            var res = preload("res://robi.png") # el recurso se carga en tiempo de compilacion
            get_node("sprite").set_texture(res)

Cargar escenas
--------------

Las escenas son recursos, pero hay una excepcion. Las escenas
guardadas a disco son del tipo :ref:`PackedScene <class_PackedScene>`,
esto implica que la escena esta empacada dentro de un recurso.

Para obtener una instancia de la escena, el metodo
:ref:`PackedScene.instance() <class_PackedScene_instance>` debe ser usado.

::

    func _on_shoot():
            var bullet = preload("res://bullet.scn").instance()
            add_child(bullet)

Este metoo crea los nodos en jerarquia, los configura (ajusta todas
las propiedades) y regresa el nodo raiz de la escena, el que puede
ser agregado a cualquier nodo.

Este enfoque tiene varia ventajas. Como la funcion :ref:`PackedScene.instance() <class_PackedScene_instance>`
es bastante rapida, agregar contenido extra a la escena puede ser
hecho de forma eficiente. Nuevos enemigos, balas, efectos, etc pueden
se aregados o quitados rapidamente, sin tener que cargarlos
nuevamente de disco cada vez. Es importante recordar que, como siempre,
las imagenes, meshes (mallas), etc son todas compartidas entre las
instancias de escena.

Liberando recursos
------------------

Los recursos se extienden de :ref:`Reference <class_Reference>`.
Como tales, cuando un recurso ya no esta en  uso, se liberara a si
mismo de forma automatica. Debido a que, en la mayoria de los casos,
los Recursos estan contenidos en Nodos, los scripts y otros
recursos, cuando el nodo es quitado o liberado, todos los recursos
hijos son liberados tambien.

Scripting
---------

Como muchos objetos en Godot, no solo nodos, los recursos pueden ser
scripts tambien. Sin embargo, no hay mucha ganancia, ya que los
recursos son solo contenedores de datos.
