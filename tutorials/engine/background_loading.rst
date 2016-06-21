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

Algunas funciones extra ayudantes. ``update_progress`` actualiza la
barra de progreso o puede tambien actualizar una animacion en pausa
(la animacion representa la carga completa de principio a fin).
``set_new_scene`` pone la escena recientemente cargada en el arbol.
Como es una escena que se esta cargando, ``instance()`` necesita
ser llamado en el recurso obtenido desde el que carga (loader).

::

    func update_progress():
        var progress = float(loader.get_stage()) / loader.get_stage_count()
        # actualizamos tu barra de progreso?
        get_node("progress").set_progress(progress)

        # o actualizamos una animacion de progreso?
        var len = get_node("animation").get_current_animation_length()

        # llama esto en una animacion en pausa
        # Usa "true" como el segundo parametro para forzar la animacion a actualizarse
        get_node("animation").seek(progress * len, true)

    func set_new_scene(scene_resource):
        current_scene = scene_resource.instance()
        get_node("/root").add_child(current_scene)

Usando multiples threads
------------------------

ResourceInteractiveLoader puede ser usando desde multiples threads.
Un par de cosas a tener en cuenta si lo intentas:

Usa un semaforo
~~~~~~~~~~~~~~~

Mientras tu thread espera por el pedido de un nuevo recurso a el thread
principal, usa un semaforo para dormir (en lugar de un bucle ocupado o
algo similar).

No bloquear el thread principal durante el polling
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

Si tenes un mutex para permitir llamados desde el thread principal a
tu clase de carga, no le hagas lock mientras llamas a ``pol`` en el
que carga. Cuando un recurso termina de cargarse, puede requerir algunos
recursos desde las APIs de bajo nivel (VisualServer, etc), lo cual puede
necesitar hacer lock al thread principal para adquirirlos. Esto puede
causar un deadlock (punto muerto) si el thread principal esta esperando
por tu mutex mientras tu thread esta esperamdo cargar un recurso.

Clase ejemplo
-------------

Tu puedes encontrar una clase de ejemplo para cargar recursos en threads
aqui: :download:`resource_queue.gd </files/resource_queue.gd>`. El uso
es el siguiente:

::

    func start()

Llamar luego que instancias la clase para empezar el thread.

::

    func queue_resource(path, p_in_front = false)

Encola un recurso. Usa los parametros opcionales "p_in_front" para
ponerlo al frente de la cola.

::

    func cancel_resource(path)

Remueve un recurso desde la cola, descartando cualquier carga hecha.

::

    func is_ready(path)

Retorna true si el recurso termino de cargar y esta listo para ser
recuperado.

::

    func get_progress(path)

Obten el progreso de un recurso. Retorna -1 para error (por ejemplo si
el recurso no esta en la cola), o un numero entre 0.0 y 1.0 con el
progreso de la carga. Usar principalmente con propositos cosmeticos
(actualizar barras de progreso, etc), usa ``is_ready`` para encontrar
si un recurso esta realmente listo.


::

    func get_resource(path)

Retorna el recurso completamente cargado, o null para error. Si el
recurso no termino de cargar (``is_ready`` regresa falso), bloqueara
tu thread y terminara la carga. Si el recurso no esta en la misma cola,
llamara ``ResourceLoader::load`` para cargarla con normalidad y
retornarla.

Ejemplo:
~~~~~~~~

::

    # inicializar
    queue = preload("res://resource_queue.gd").new()
    queue.start()

    # supone que tu juego empieza con una presentacion de 10 segundos, durante los cuales el usuario no puede
    # interactuar con el juego. Durante ese tiempo sabemos que no usaran el menu pausa, por lo que podemos
    # encolarlo para que cargue durante la presentacion:
    queue.queue_resource("res://pause_menu.xml")
    start_curscene()

    # mas tarde cuando el usuario presiona el boton pausa por primer vez:
    pause_menu = queue.get_resource("res://pause_menu.xml").instance()
    pause_menu.show()

    # cuando se necesita una escena nueva:
    queue.queue_resource("res://level_1.xml", true) # usa "true" como segundo parametro para ponerlo al frente
                                                    # de la cola, pausando la carga de cualqueir otro
                                                    # recurso

    # para chequear el progreso
    if queue.is_ready("res://level_1.xml"):
        show_new_level(queue.get_resource("res://level_1.xml"))
    else:
        update_progress(queue.get_process("res://level_1.xml"))

    #   cuando el usuario sale fuera de la zona trigger en tu juego Metroidvania:
    queue.cancel_resource("res://zone_2.xml")

**Nota**: este codigo en su forma actual no ha sido testeado en
escenarios reales. Preguntale a punto en IRC (#godotengine en
irc.freenode.net) por ayuda.
