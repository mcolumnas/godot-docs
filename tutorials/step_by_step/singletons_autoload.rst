.. _doc_singletons_autoload:

Singletons (AutoCarga)
=====================

Introduccion
------------

Los Singletons de escena son muy utiles, ya que representan un caso
 de uso muy comun, pero no es claro al principio de donde viene su
 valor.

El sistema de escenas es muy util, aunque propiamente tiene algunas
desventajas:

-  No hay lugar comun donde almacenar informacion (como nucleo, items
   obtenidos, etc) entre dos escenas.
-  Es posible hacer una escena que carga otras escenas como hijos y
   las libera, mientras mantiene la informacion, pero si esto es hecho,
   no es posible correr una escena sola por si misma y esperar que
   funcione.
-  Tambien es posible almacenar informacion persistente a disco en
   \`user://\` y hacer que las escenas siempre lo carguen, pero guardar
   y cargar eso mientras cambiamos de escenas es incomodo.

Por lo tanto, luego de usar Godot por un tiempo, se vuelve claro que
es necesario tener partes de una escena que:

-  Estan siempre cargadas, no importa que escena es abierta desde el
   editor.
-  Puede mantener variables globales, como informacion de jugador,
   items, dinero, etc.
-  Puede manejar cambios de escenas y transiciones.
-  Solo ten algo que actue como singleton, ya que GDScript no soporta
   variables globales por diseño.

Por eso, la opcion para auto-cargar nodos y scripts existe.

So, after using Godot for a while, it becomes clear that it

AutoLoad (AutoCarga)
--------

AutoLoad puede ser una escena, o un script que hereda desde Nodo (un
Nodo sera creado y el script ajustado para el). Son agregados a el
proyecto en Escena > Configuracion de Proyecto > pestaña AutoLoad.

Cada autoload necesita un nombre, este nombre sera el nombre del nodo,
y el nodo siempre sera agregado al viewport root (raiz) antes de que
alguna escena sea cargada.


.. image:: /img/singleton.png

Esto significa, que para un singleton llamado "jugadorvariables",
cualquier nodo puede accederlo al requerirlo:

::

    var jugador_vars = get_node("/root/jugadorvariables")

Conmutador(Switcher) personalizado de escena
------------------------------

Este corto tutorial explicara como hacer para lograr un conmutador
usando autoload. Para conmutar una escena de forma simple, el metodo
:ref:`SceneTree.change_scene() <class_SceneTree_change_scene>` basta
(descrito en :ref:`doc_scene_tree`), por lo que este metodo es para
comportamientos mas complejos de conmutacion de escenas.

Primero descarga la plantilla desde aqui:
:download:`autoload.zip </files/autoload.zip>`, y abrela.

Dos escenas estan presentes, scene_a.scn y scene_b.scn en un proyecto
que salvo por estas escenas esta vacio. Cada una es identica y contiene
un boton conectado a la llamada de retorno para ir a la escena opuesta.
Cuando el proyecto corre, comienza en scene_a.scn. Sin embargo, esto
no hace nada y tocar el boton no funciona.

global.gd
---------

Primero que nada, crea un script global.gd. La forma mas facil de
crear un recurso desde cero es desde la pestaña del Inspector:

.. image:: /img/newscript.png

Guarda el script con el nombre global.gd:

.. image:: /img/saveasscript.png

El script deberia estar abierto en el editor de scripts. El siguiente
paso sera agregarlo a autoload, para esto, ve a: Escena, Configuracion
de Proyecto, Autoload y agrega un nuevo autoload con el nombre
"global" que apunta a ester archivo:

.. image:: /img/addglobal.png

Ahora, cuando la escena corre, el script siempre sera cargado.

Asique, yendo un poco atras, en la funcion _ready(), la escena actual
sera traida. Tanto la escena actual como global.gd son hijos de root,
pero los nodos autocargados siempre estan primeros. Esto implica
que el ultimo hijo de de root es siempre la escena cargada.

Tambien, asegurate que global.gd se extienda desde Nodo, de otra forma
no sera cargada.

::

    extends Node

    var escena_actual = null

    func _ready():
            var raiz = get_tree().get_root()
            escena_actual = raiz.get_child( raiz.get_child_count() - 1 )

Como siguiente paso, es necesaria la funcion para cambiar de escena.
Esta funcion va a borrar la escena actual y reemplazarla por la que se
pidio.

::

    func ir_escena(camino):

        # Esta funcion usualmente sera llamada de una señal de
        # llamada de retorno, o alguna otra funcion de la escena
        # que esta corriendo borrar la escena actual en este punto
        # puede ser una mala idea, porque puede estar dentro de una
        # llamada de retorno o funcion de ella. El peor caso va a
        # ser que se cuelgue o comportamiento no esperado.

        # La forma de evitar esto es difiriendo la carga para mas
        # tarde, cuando es seguro que ningun codigo de la escena
        # actual esta corriendo:

        call_deferred("_ir_escena_diferida",camino)


    func _ir_escena_diferida(camino):

        # Inmediatamente libera la escena actual,
        # no hay riesgo aqui.
        escena_actual.free()

        # Carga la nueva escena
        var s = ResourceLoader.load(camino)

        # Instancia la nueva escena
        escena_actual = s.instance()

        # Add it to the active scene, as child of root
        get_tree().get_root().add_child(escena_actual)

        # optional, to make it compatible with the SceneTree.change_scene() API
        get_tree().set_current_scene( escena_actual )

Como mencionamos en los comentarios de arriba, realmente queremos evitar
la situacion de tener la escena actual siendo borrada mientras esta
siendo usada (el codigo de sus funciones aun corriendo), por lo que
usando :ref:`Object.call_deferred() <class_Object_call_deferred>`
es recomendado en este punto. El resultado es que la ejecucion de los
comandos en la segunda funcion van a suceder en un momento
inmediatamente posterior inmediato cuando no hay codigo de la escena
actual corriendo.

Finalmente, todo lo que queda es llenar las funciones vacias en
scene_a.gd y scene_b.gd:

::

    #agrega a scene_a.gd

    func _on_goto_scene_pressed():
            get_node("/root/global").ir_escena("res://scene_b.scn")

y

::

    #agrega a scene_b.gd

    func _on_goto_scene_pressed():
            get_node("/root/global").ir_escena("res://scene_a.scn")

Finalmente, al correr el proyecto es posible conmutar entre ambas
escenas al presionar el boton!

(Para cargar escenas con una barra de progreso, chequea el proximo
tutorial, :ref:`doc_background_loading`)
