.. _doc_inputevent:

InputEvent (Evento de Entrada)
==========

Que es?
-----------

Gestionar entradas es usualmente compejo, no importa el SO o la
plataforma. Para facilitarlo un poco, un objeto especial incorporado
se provee, :ref:`InputEvent <class_InputEvent>`. Este tipo de datos
puede ser configurado opara contener varios tipos de eventos de
entrada. Input Events viaja a traves del motor y puede ser recibido
en multiples lugares, dependiendo del proposito.


Como funciona?
-----------------

Cada evento de entrada se origina desde el usuario/jugador (aunque es
posible generar un InputEvent y darselo de vuelta al motor, lo cual
es util para gestos). El objeto OS para cada plataforma va a leer
eventos desde el dispositivo, luego pasandoselo al MainLoop. Como
:ref:`SceneTree <class_SceneTree>` es la implementacion por defecto
de MainLoop, los eventos se envian alli. Godot provee una funcion para
obtener el objeto SceneTree actual:
**get_tree()**

Pero SceneTree no sabe que hacer con este evento, por lo que se lo va
a dar a los viewports, empezando desde "root"  :ref:`Viewport <class_Viewport>`
(el primer nodo del arbol de escena). Viewport hace una cantidad
considerable de cosas con la entrada recibida, en orden:

.. image:: /img/input_event_flow.png

1. Primero, intentara darle la entrada a la GUI, y ver si algun control
   lo recibe. De ser asi, :ref:`Control <class_Control>` sera llamado por
   la funcion virtual :ref:`Control._input_event() <class_Control__input_event>` y la señal
   "input_event" sera emitida (esta funcion es reimplementable por
   script al heredarla). Si el control quiere "consumir" el evento, va
   a llamar :ref:`Control.accept_event() <class_Control_accept_event>` y el evento
   no se esparcira mas.
2. Si la GUI no quiere el evento, la funcion standard _input sera
   llamada en cualquier nodo con procesamiento de entrada habilitado
   (habilitalo con :ref:`Node.set_process_input() <class_Node_set_process_input>` y sobrescribe
   :ref:`Node._input() <class_Node__input>`). Si alguna funcion consume
   el evento, puede llamar :ref:`SceneTree.set_input_as_handled() <class_SceneTree_set_input_as_handled>`,
   y el evento no se esparcira mas.
3. Si aun nadie consumio el evento, la llamada de retorno unhandled sera
   invocada (habilitala con
   :ref:`Node.set_process_unhandled_input() <class_Node_set_process_unhandled_input>`
   y sobrescribe :ref:`Node._unhandled_input() <class_Node__unhandled_input>`).
   Si alguna funcion consume el evento, puede llamar a :ref:`SceneTree.set_input_as_handled() <class_SceneTree_set_input_as_handled>`,,
   y el evento no se espercira mas.
4. Si nadie ha querido el evento hasta ahora, y una :ref:`Camera <class_Camera>`
   es asignada al viewport, un rayo al mundo de fisica (en la direccion
   de rayo del click) sera emitido. Si este rayo toca un objeto, llamara
   a la funcion :ref:`CollisionObject._input_event() <class_CollisionObject__input_event>`
   en el objeto fisico relevante (los cuerpos reciben esta llamada de
   retorno por defecto, pero las areas no. Esto puede ser configurado a
   reaves de las propiedades :ref:`Area <class_Area>`).
5. Finalmente, si el evento no fue utilizado, sera pasado al siguiente
   Viewport en el arbol, de otra forma sera ignorado.

Anatomia de un InputEvent
------------------------

:ref:`InputEvent <class_InputEvent>` es solo un tipo base incorporado,
no representa nada y solo contiene informacion basica, como el ID
de evento (el cual es incrementado para cada evento), indice de
dispositivo, etc.

InputEvent tiene un miembro "type". Al asignarlo, se puede volver
diferentes tipos de entrada. Todo tipo de InputEvent tiene propiedades
diferentes, de acuerdo a su rol.

Ejempo de un tipo de evento cambiante.

::

    # crear evento
    var ev = InputEvent()
    # ajustar tipo index (indice)
    ev.type = InputEvent.MOUSE_BUTTON
    # button_index solo esta disponible para el tipo de arriba
    ev.button_index = BUTTON_LEFT

Hay varios tipo de InputEvent, descritos en la table de abajo:

+-------------------------------------------------------------------+--------------------+-----------------------------------------+
| Evento                                                            | Type Index         | Descripcion                             |
+-------------------------------------------------------------------+--------------------+-----------------------------------------+
| :ref:`InputEvent <class_InputEvent>`                              | NONE               | Evento de entrada vacio.                |
+-------------------------------------------------------------------+--------------------+-----------------------------------------+
| :ref:`InputEventKey <class_InputEventKey>`                        | KEY                | Contiene un valor scancode y unicode,   |
|                                                                   |                    | ademas de modificadores.                |
+-------------------------------------------------------------------+--------------------+-----------------------------------------+
| :ref:`InputEventMouseButton <class_InputEventMouseButton>`        | MOUSE_BUTTON       | Contiene informacion de clicks, como    |
|                                                                   |                    | boton, modificadores, etc.              |
+-------------------------------------------------------------------+--------------------+-----------------------------------------+
| :ref:`InputEventMouseMotion <class_InputEventMouseMotion>`        | MOUSE_MOTION       | Contiene informacion de movimiento, como|
|                                                                   |                    | pos. relativas y absolutas, velocidad   |
+-------------------------------------------------------------------+--------------------+-----------------------------------------+
| :ref:`InputEventJoystickMotion <class_InputEventJoystickMotion>`  | JOYSTICK_MOTION    | Contiene informacion de ejes analogos   |
|                                                                   |                    | de Joystick/Joypad                      |
+-------------------------------------------------------------------+--------------------+-----------------------------------------+
| :ref:`InputEventJoystickButton <class_InputEventJoystickButton>`  | JOYSTICK_BUTTON    | Contiene informacion de botones de      |
|                                                                   |                    | Joystick/Joypad                         |
+-------------------------------------------------------------------+--------------------+-----------------------------------------+
| :ref:`InputEventScreenTouch <class_InputEventScreenTouch>`        | SCREEN_TOUCH       | Contiene informacion multi-touch de     |
|                                                                   |                    | presionar/soltar. (solo disponible en   |
|                                                                   |                    | dispositivos moviles)                   |
+-------------------------------------------------------------------+--------------------+-----------------------------------------+
| :ref:`InputEventScreenDrag <class_InputEventScreenDrag>`          | SCREEN_DRAG        | Contiene informacion multi-touch de     |
|                                                                   |                    | arrastre (solo en disp. moviles)        |
+-------------------------------------------------------------------+--------------------+-----------------------------------------+
| :ref:`InputEventAction <class_InputEventAction>`                  | SCREEN_ACTION      | Contiene una accion generica. estos     |
|                                                                   |                    | eventos suelen generarse por el program-|
|                                                                   |                    | ador como feedback. (mas de esto abajo) |
+-------------------------------------------------------------------+--------------------+-----------------------------------------+

Acciones
-------

Un InputEvent puede o no representar una accion predefinida. Las
acciones son utiles porque abstraen el dispositivo de entrada cuando
programamos la logica de juego. Esto permite:

-  Que el mismo codigo trabaje en diferentes dispositivos con diferentes
   entradas (por ej. teclado en PC, Joypad en consola).
-  Entrada a ser reconfigurada en tiempo de ejecucion.

Las acciones pueden ser creadas desde la Configuracion de Proyecto en
la pestaña Actions. Lee :ref:`doc_simple_2d_game-input_actions_setup` para una
explicacion de como funciona el editor de acciones.

Cualquier evento tiene los metodos :ref:`InputEvent.is_action() <class_InputEvent_is_action>`,
:ref:`InputEvent.is_pressed() <class_InputEvent_is_pressed>` y :ref:`InputEvent <class_InputEvent>`.

De forma alternativa, puede desearse suplir al juego de vuelta con
una accion desde el codigo (un buen ejemplo es detectar gestos).
SceneTree (derivado de MainLoop) tiene un metodo para esto:
:ref:`MainLoop.input_event() <class_MainLoop_input_event>`.
Se usaria normalmente de esta forma:

::

    var ev = InputEvent()
    ev.type = InputEvent.ACTION
    # ajustar como move_left, presionado
    ev.set_as_action("move_left", true)
    # feedback
    get_tree().input_event(ev)

InputMap (Mapa de controles)
--------

Personalizar y re mapear entrada desde codigo es a menudo deseado. Si
tu worflow depende de acciones, el singleton :ref:`InputMap <class_InputMap>`
es ideal para reasignar o crear diferentes acciones en tiempo de
ejecucion. Este singleton no es guardado (debe ser modificado
manualmente) y su estado es corrido desde los ajustes de proyecto
(engine.cfg). Por lo que cualquier sistema dinamico de este tipo
necesita guardar su configuracion de la forma que el programador
lo crea conveniente.
