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

Externo vs incorporado
---------------------

Las propiedades de los recursos pueden referencias recursos de dos
maneras, *externos* (en disco) o **incorporados**.

Para ser mas especifico, aqui hay una :ref:`Texture <class_Texture>`
en un nodo :ref:`Sprite <class_Sprite>`:

.. image:: /img/spriteprop.png

Presionando el boton ">" a la derecha de la vista previa, permite ver
y editar las propiedades del recurso. Una de las propiedades (path)
muestra de donde vino. En este caso, desde una imagen png.

.. image:: /img/resourcerobi.png

When the resource comes from a file, it is considered an *external*
resource. If the path property is erased (or never had a path to begin
with), it is then considered a built-in resource.

For example, if the path \`"res://robi.png"\` is erased from the "path"
property in the above example, and then the scene is saved, the resource
will be saved inside the .scn scene file, no longer referencing the
external "robi.png". However, even if saved as built-in, and even though
the scene can be instanced multiple times, the resource will still
always be loaded once. That means, different Robi robot scenes instanced
at the same time will still share the same image.

Loading resources from code
---------------------------

Loading resources from code is easy, there are two ways to do it. The
first is to use load(), like this:

::

    func _ready():
            var res = load("res://robi.png") # resource is loaded when line is executed
            get_node("sprite").set_texture(res)

The second way is more optimal, but only works with a string constant
parameter, because it loads the resource at compile-time.

::

    func _ready():
            var res = preload("res://robi.png") # resource is loaded at compile time
            get_node("sprite").set_texture(res)

Loading scenes
--------------

Scenes are also resources, but there is a catch. Scenes saved to disk
are resources of type :ref:`PackedScene <class_PackedScene>`,
this means that the scene is packed inside a resource.

To obtain an instance of the scene, the method
:ref:`PackedScene.instance() <class_PackedScene_instance>`
must be used.

::

    func _on_shoot():
            var bullet = preload("res://bullet.scn").instance()
            add_child(bullet)

This method creates the nodes in hierarchy, configures them (sets all
the properties) and returns the root node of the scene, which can be
added to any other node.

The approach has several advantages. As the
:ref:`PackedScene.instance() <class_PackedScene_instance>`
function is pretty fast, adding extra content to the scene can be done
efficiently. New enemies, bullets, effects, etc can be added or
removed quickly, without having to load them again from disk each
time. It is important to remember that, as always, images, meshes, etc
are all shared between the scene instances.

Freeing resources
-----------------

Resource extends from :ref:`Reference <class_Reference>`.
As such, when a resource is no longer in use, it will automatically free
itelf. Since, in most cases, Resources are contained in Nodes, scripts
or other resources, when a node is removed or freed, all the children
resources are freed too.

Scripting
---------

Like any object in Godot, not just nodes, resources can be scripted too.
However, there isn't generally much of a win, as resources are just data
containers.
