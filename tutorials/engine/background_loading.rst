.. _doc_background_loading:

Carga en segundo plano
======================

Cuando cambias la escena principal de tu juego (por ejemplo yendo a un
nuevo nivel), puedes querer mostrar una pantalla de carga con alguna
indicacion del progreso. El metodo principal de carga
(``ResourceLoader::load`` or just ``load`` from gdscript) bloquea tu
thread (hilo) mientras los recursos estan siendo cargados, por lo que
no es bueno. Este documento discute la clase
``ResourceInteractiveLoader`` para pantallas de carga mas suaves.

ResourceInteractiveLoader
-------------------------

La clase ``ResourceInteractiveLoader`` te permite cargar el recurso en
etapas. Cada vez que el metodo ``poll`` es llamado, una nueva etapa es
cargada, y el control se devuelve al que llamo. Cada etapa es
generalmente un sub-recurso que es cargado por el recurso principal. Por
ejemplo, si vas a cargar una escena que tiene 10 imagenes, cada imagen
sera una etapa.

Uso
---

La forma de usarlo es generalmente la siguiente:

Obteniendo un ResourceInteractiveLoader
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

::

    Ref<ResourceInteractiveLoader> ResourceLoader::load_interactive(String p_path);

Este metodo te dara un ResourceInteractiveLoader que usaras para
gestionar la operacion de carga.

Polling
~~~~~~~

::

    Error ResourceInteractiveLoader::poll();

Usa este metodo para avanzar el progreso de la carga. Cada llamada a
``poll`` cargara la siguiente etapa de tu recurso. Ten en cuenta que
cada etapa es un recurso "atomico" completo, como una imagen, una malla,
por lo que llevara varios frames para cargarlo.

Devuleve ``OK`` si no hay errores, ``ERR_FILE_EOF`` cuando la carga
termina. Cualquier otro valor implicar que hubo un error y la carga
se detuvo.

Progreso de carga (opcional)
~~~~~~~~~~~~~~~~~~~~~~~~~~~~

Para consultar el progreso de la carga, usa los siguientes metodos:

::

    int ResourceInteractiveLoader::get_stage_count() const;
    int ResourceInteractiveLoader::get_stage() const;

``get_stage_count`` devuelve el numero total de etapas a cargar.
``get_stage`` devuelve la etapa actual siendo cargada.

Forzar que complete (opcional)
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

::

    Error ResourceInteractiveLoader::wait();

Usa este metodo si necesitas cargar el recurso entero en el frame
actual, sin pasos adicionales.

Obtener el recurso
~~~~~~~~~~~~~~~~~~~~~~

::

    Ref<Resource> ResourceInteractiveLoader::get_resource();

Si todo va bien, usa este metodo para traer tu recurso cargado.

Ejemplo
-------

Este ejemplo demuestra como cargar una nueva escena. Consideralo en el
contexto del ejemplo :ref:`doc_singletons_autoload`.

Primero debemos declarar algunas variables e inicializar
``current_scene'' con la escena principal del juego:

::

    var loader
    var wait_frames
    var time_max = 100 # milisegundos
    var current_scene

    func _ready():
        var root = get_tree().get_root()
        current_scene = root.get_child(root.get_child_count() -1)

La funcion ``goto_scene`` es llamada desde el juego cuando la escena
necesita ser conmutada. Pide una carga interactiva, y llama
``set_progress(true)`` para empezar a hacer polling de la carga en la
llamada de retorno ``_progress``. Tambien inicia una animacion de
"carga", que puede mostrar una barra de progreso o pantalla de carga,
 etc.

::

    func goto_scene(path): # el juego pide cambiar a este escena
        loader = ResourceLoader.load_interactive(path)
        if loader == null: # chequear errores
            show_error()
            return
        set_process(true)

        current_scene.queue_free() # dejar de lado la escena vieja

        # Empieza tu animacion "loading..."("cargando"...)
        get_node("animation").play("loading")

        wait_frames = 1

``_process`` es donde se le hace polling a la carga. ``poll`` es
llamado, y luego nos encargamos del valor de retorno de esa llamada.
``OK`` significa mantente haciendo polling, ``ERR_FILE_EOF`` que la
carga esta completa, cualquier otra cosa que hubo un error. Tambien
fijate que salteamos un frame (via ``wait_frames``, ajustada en la
funcion ´´goto_scene´´) para permitir que la pantalla de carga se
muestre.

Fijate como usar ``OS.get_ticks_msec`` para controlar cuanto tiempo
bloqueamos el thread. Algunas etapas podran cargarse realmente
rapido, lo que significa que podriamos sumar mas de una llamada a
``poll`` en un frame, algunas pueden tomar mucho mas que el valor de
``time_max``, por lo que ten en cuenta que no tenemos control
preciso sobre estos timing.


::

    func _process(time):
        if loader == null:
            #  no hace falta procesar mas
            set_process(false)
            return

        if wait_frames > 0: # espera por frames para permitir que la animacion "loading" se muestre
            wait_frames -= 1
            return

        var t = OS.get_ticks_msec()
        while OS.get_ticks_msec() < t + time_max: # usa "time_max" para controlar durante cuanto tiempo bloqueamos este thread


            # haciendole poll al loader
            var err = loader.poll()

            if err == ERR_FILE_EOF: # la carga termino
                var resource = loader.get_resource()
                loader = null
                set_new_scene(resource)
                break
            elif err == OK:
                update_progress()
            else: # error durante la carga
                show_error()
                loader = null
                break

Some extra helper functions. ``update_progress`` updates a progress bar,
or can also update a paused animation (the animation represents the
entire load process from beginning to end). ``set_new_scene`` puts the
newly loaded scene on the tree. Because it's a scene being loaded,
``instance()`` needs to be called on the resource obtained from the
loader.

::

    func update_progress():
        var progress = float(loader.get_stage()) / loader.get_stage_count()
        # update your progress bar?
        get_node("progress").set_progress(progress)

        # or update a progress animation?
        var len = get_node("animation").get_current_animation_length()

        # call this on a paused animation. use "true" as the second parameter to force the animation to update
        get_node("animation").seek(progress * len, true)

    func set_new_scene(scene_resource):
        current_scene = scene_resource.instance()
        get_node("/root").add_child(current_scene)

Using multiple threads
----------------------

ResourceInteractiveLoader can be used from multiple threads. A couple of
things to keep in mind if you attempt it:

Use a Semaphore
~~~~~~~~~~~~~~~

While your thread waits for the main thread to request a new resource,
use a Semaphore to sleep (instead of a busy loop or anything similar).

Not blocking main thread during the polling
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

If you have a mutex to allow calls from the main thread to your loader
class, don't lock it while you call ``poll`` on the loader. When a
resource is finished loading, it might require some resources from the
low level APIs (VisualServer, etc), which might need to lock the main
thread to acquire them. This might cause a deadlock if the main thread
is waiting for your mutex while your thread is waiting to load a
resource.

Example class
-------------

You can find an example class for loading resources in threads here:
:download:`resource_queue.gd </files/resource_queue.gd>`. Usage is as follows:

::

    func start()

Call after you instance the class to start the thread.

::

    func queue_resource(path, p_in_front = false)

Queue a resource. Use optional parameter "p_in_front" to put it in
front of the queue.

::

    func cancel_resource(path)

Remove a resource from the queue, discarding any loading done.

::

    func is_ready(path)

Returns true if a resource is done loading and ready to be retrieved.

::

    func get_progress(path)

Get the progress of a resource. Returns -1 on error (for example if the
resource is not on the queue), or a number between 0.0 and 1.0 with the
progress of the load. Use mostly for cosmetic purposes (updating
progress bars, etc), use ``is_ready`` to find out if a resource is
actually ready.

::

    func get_resource(path)

Returns the fully loaded resource, or null on error. If the resource is
not done loading (``is_ready`` returns false), it will block your thread
and finish the load. If the resource is not on the queue, it will call
``ResourceLoader::load`` to load it normally and return it.

Example:
~~~~~~~~

::

    # initialize
    queue = preload("res://resource_queue.gd").new()
    queue.start()

    # suppose your game starts with a 10 second custscene, during which the user can't interact with the game.
    # For that time we know they won't use the pause menu, so we can queue it to load during the cutscene:
    queue.queue_resource("res://pause_menu.xml")
    start_curscene()

    # later when the user presses the pause button for the first time:
    pause_menu = queue.get_resource("res://pause_menu.xml").instance()
    pause_menu.show()

    # when you need a new scene:
    queue.queue_resource("res://level_1.xml", true) # use "true" as the second parameter to put it at the front
                                                    # of the queue, pausing the load of any other resource

    # to check progress
    if queue.is_ready("res://level_1.xml"):
        show_new_level(queue.get_resource("res://level_1.xml"))
    else:
        update_progress(queue.get_process("res://level_1.xml"))

    # when the user walks away from the trigger zone in your Metroidvania game:
    queue.cancel_resource("res://zone_2.xml")

**Note**: this code in its current form is not tested in real world
scenarios. Ask punto on IRC (#godotengine on irc.freenode.net) for help.
