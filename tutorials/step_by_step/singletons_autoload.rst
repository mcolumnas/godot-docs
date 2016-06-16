.. _doc_singletons_autoload:

Singletons (AutoCarga)
=====================

Introducción
------------

Los Singletons de escena son muy útiles, ya que representan un caso
de uso muy común, pero no es claro al principio de donde viene su
valor.

El sistema de escenas es muy útil, aunque propiamente tiene algunas
desventajas:

-  No hay lugar común donde almacenar información (como núcleo, ítems
   obtenidos, etc) entre dos escenas.
-  Es posible hacer una escena que carga otras escenas como hijos y
   las libera, mientras mantiene la información, pero si esto es hecho,
   no es posible correr una escena sola por si misma y esperar que
   funcione.
-  También es posible almacenar información persistente a disco en
   \`user://\` y hacer que las escenas siempre lo carguen, pero guardar
   y cargar eso mientras cambiamos de escenas es incomodo.

Por lo tanto, luego de usar Godot por un tiempo, se vuelve claro que
es necesario tener partes de una escena que:

-  Están siempre cargadas, no importa que escena es abierta desde el
   editor.
-  Puede mantener variables globales, como información de jugador,
   ítems, dinero, etc.
-  Puede manejar cambios de escenas y transiciones.
-  Solo ten algo que actúe como singleton, ya que GDScript no soporta
   variables globales por diseño.

Por eso, la opción para auto-cargar nodos y scripts existe.

AutoLoad (AutoCarga)
--------

AutoLoad puede ser una escena, o un script que hereda desde Nodo (un
Nodo sera creado y el script ajustado para el). Son agregados a el
proyecto en Escena > Configuración de Proyecto > pestaña AutoLoad.

Cada autoload necesita un nombre, este nombre sera el nombre del nodo,
y el nodo siempre sera agregado al viewport root (raíz) antes de que
alguna escena sea cargada.


.. image:: /img/singleton.png

Esto significa, que para un singleton llamado "jugadorvariables",
cualquier nodo puede accederlo al requerirlo:

::

    var jugador_vars = get_node("/root/jugadorvariables")

Conmutador(Switcher) personalizado de escena
------------------------------

Este corto tutorial explicara como hacer para lograr un conmutador
usando autoload. Para conmutar una escena de forma simple, el método
:ref:`SceneTree.change_scene() <class_SceneTree_change_scene>` basta
(descrito en :ref:`doc_scene_tree`), por lo que este método es para
comportamientos mas complejos de conmutación de escenas.

Primero descarga la plantilla desde aquí:
:download:`autoload.zip </files/autoload.zip>`, y ábrela.

Dos escenas están presentes, scene_a.scn y scene_b.scn en un proyecto
que salvo por estas escenas esta vació. Cada una es idéntica y contiene
un botón conectado a la llamada de retorno para ir a la escena opuesta.
Cuando el proyecto corre, comienza en scene_a.scn. Sin embargo, esto
no hace nada y tocar el boton no funciona.

global.gd
---------

Primero que nada, crea un script global.gd. La forma mas fácil de
crear un recurso desde cero es desde la pestaña del Inspector:

.. image:: /img/newscript.png

Guarda el script con el nombre global.gd:

.. image:: /img/saveasscript.png

El script debería estar abierto en el editor de scripts. El siguiente
paso sera agregarlo a autoload, para esto, ve a: Escena, Configuración
de Proyecto, Autoload y agrega un nuevo autoload con el nombre
"global" que apunta a ester archivo:

.. image:: /img/addglobal.png

Ahora, cuando la escena corre, el script siempre sera cargado.

Así que, yendo un poco atrás, en la función _ready(), la escena actual
sera traída. Tanto la escena actual como global.gd son hijos de root,
pero los nodos autocargados siempre están primeros. Esto implica
que el ultimo hijo de de root es siempre la escena cargada.

También, asegúrate que global.gd se extienda desde Nodo, de otra forma
no sera cargada.

::

    extends Node

    var escena_actual = null

    func _ready():
            var raiz = get_tree().get_root()
            escena_actual = raiz.get_child( raiz.get_child_count() - 1 )

Como siguiente paso, es necesaria la función para cambiar de escena.
Esta función va a borrar la escena actual y reemplazarla por la que se
pidio.

::

    func ir_escena(camino):

        # Esta función usualmente sera llamada de una señal de
        # llamada de retorno, o alguna otra función de la escena
        # que esta corriendo borrar la escena actual en este punto
        # puede ser una mala idea, porque puede estar dentro de una
        # llamada de retorno o función de ella. El peor caso va a
        # ser que se cuelgue o comportamiento no esperado.

        # La forma de evitar esto es difiriendo la carga para mas
        # tarde, cuando es seguro que ningún código de la escena
        # actual esta corriendo:

        call_deferred("_ir_escena_diferida",camino)


    func _ir_escena_diferida(camino):

        # Inmediatamente libera la escena actual,
        # no hay riesgo aquí.
        escena_actual.free()

        # Carga la nueva escena
        var s = ResourceLoader.load(camino)

        # Instancia la nueva escena
        escena_actual = s.instance()

        # Agrégalo a la escena activa, como hijo de root
        get_tree().get_root().add_child(escena_actual)

        # Opcional, para hacerlo compatible con la API SceneTree.change_scene()
        get_tree().set_current_scene( escena_actual )

Como mencionamos en los comentarios de arriba, realmente queremos evitar
la situación de tener la escena actual siendo borrada mientras esta
siendo usada (el código de sus funciones aun corriendo), por lo que
usando :ref:`Object.call_deferred() <class_Object_call_deferred>`
es recomendado en este punto. El resultado es que la ejecución de los
comandos en la segunda función van a suceder en un momento
inmediatamente posterior inmediato cuando no hay código de la escena
actual corriendo.

Finalmente, todo lo que queda es llenar las funciones vacías en
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
escenas al presionar el botón!

(Para cargar escenas con una barra de progreso, chequea el próximo
tutorial, :ref:`doc_background_loading`)
