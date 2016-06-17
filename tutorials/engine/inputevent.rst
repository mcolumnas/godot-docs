.. _doc_inputevent:

InputEvent (Evento de Entrada)
==========

Que es?
-----------

Gestionar entradas es usualmente complejo, no importa el SO o la
plataforma. Para facilitarlo un poco, un objeto especial incorporado
se provee, :ref:`InputEvent <class_InputEvent>`. Este tipo de datos
puede ser configurado para contener varios tipos de eventos de
entrada. Input Events viaja a través del motor y puede ser recibido
en múltiples lugares, dependiendo del propósito.


Como funciona?
-----------------

Cada evento de entrada se origina desde el usuario/jugador (aunque es
posible generar un InputEvent y dárselo de vuelta al motor, lo cual
es util para gestos). El objeto OS para cada plataforma va a leer
eventos desde el dispositivo, luego pasándoselo al MainLoop. Como
:ref:`SceneTree <class_SceneTree>` es la implementación por defecto
de MainLoop, los eventos se envían allí. Godot provee una función para
obtener el objeto SceneTree actual:
**get_tree()**

Pero SceneTree no sabe que hacer con este evento, por lo que se lo va
a dar a los viewports, empezando desde "root"  :ref:`Viewport <class_Viewport>`
(el primer nodo del arbol de escena). Viewport hace una cantidad
considerable de cosas con la entrada recibida, en orden:

.. image:: /img/input_event_flow.png

1. Primero, intentara darle la entrada a la GUI, y ver si algún control
   lo recibe. De ser asi, :ref:`Control <class_Control>` será llamado por
   la función virtual :ref:`Control._input_event() <class_Control__input_event>` y la señal
   "input_event" sera emitida (esta función es reimplementable por
   script al heredarla). Si el control quiere "consumir" el evento, va
   a llamar :ref:`Control.accept_event() <class_Control_accept_event>` y el evento
   no se esparcirá mas.
2. Si la GUI no quiere el evento, la función standard _input sera
   llamada en cualquier nodo con procesamiento de entrada habilitado
   (habilítalo con :ref:`Node.set_process_input() <class_Node_set_process_input>` y sobrescribe
   :ref:`Node._input() <class_Node__input>`). Si alguna función consume
   el evento, puede llamar :ref:`SceneTree.set_input_as_handled() <class_SceneTree_set_input_as_handled>`,
   y el evento no se esparcirá mas.
3. Si aun nadie consumió el evento, la llamada de retorno unhandled será
   invocada (habilítala con
   :ref:`Node.set_process_unhandled_input() <class_Node_set_process_unhandled_input>`
   y sobrescribe :ref:`Node._unhandled_input() <class_Node__unhandled_input>`).
   Si alguna funcion consume el evento, puede llamar a :ref:`SceneTree.set_input_as_handled() <class_SceneTree_set_input_as_handled>`,,
   y el evento no se esparcirá mas.
4. Si nadie ha querido el evento hasta ahora, y una :ref:`Camera <class_Camera>`
   es asignada al viewport, un rayo al mundo de física (en la dirección
   de rayo del clic) será emitido. Si este rayo toca un objeto, llamara
   a la funcion :ref:`CollisionObject._input_event() <class_CollisionObject__input_event>`
   en el objeto físico relevante (los cuerpos reciben esta llamada de
   retorno por defecto, pero las áreas no. Esto puede ser configurado a
   través de las propiedades :ref:`Area <class_Area>`).
5. Finalmente, si el evento no fue utilizado, será pasado al siguiente
   Viewport en el árbol, de otra forma será ignorado.

Anatomía de un InputEvent
------------------------

:ref:`InputEvent <class_InputEvent>` es solo un tipo base incorporado,
no representa nada y solo contiene información básica, como el ID
de evento (el cual es incrementado para cada evento), índice de
dispositivo, etc.

InputEvent tiene un miembro "type". Al asignarlo, se puede volver
diferentes tipos de entrada. Todo tipo de InputEvent tiene propiedades
diferentes, de acuerdo a su rol.

Ejemplo de un tipo de evento cambiante.

::

    # crear evento
    var ev = InputEvent()
    # ajustar tipo index (índice)
    ev.type = InputEvent.MOUSE_BUTTON
    # button_index solo esta disponible para el tipo de arriba
    ev.button_index = BUTTON_LEFT

Hay varios tipo de InputEvent, descritos en la tabla de abajo:

+-------------------------------------------------------------------+--------------------+-----------------------------------------+
| Evento                                                            | Type Index         | Descripción                             |
+-------------------------------------------------------------------+--------------------+-----------------------------------------+
| :ref:`InputEvent <class_InputEvent>`                              | NONE               | Evento de entrada vacío.                |
+-------------------------------------------------------------------+--------------------+-----------------------------------------+
| :ref:`InputEventKey <class_InputEventKey>`                        | KEY                | Contiene un valor scancode y unicode,   |
|                                                                   |                    | además de modificadores.                |
+-------------------------------------------------------------------+--------------------+-----------------------------------------+
| :ref:`InputEventMouseButton <class_InputEventMouseButton>`        | MOUSE_BUTTON       | Contiene información de clics, como     |
|                                                                   |                    | botón, modificadores, etc.              |
+-------------------------------------------------------------------+--------------------+-----------------------------------------+
| :ref:`InputEventMouseMotion <class_InputEventMouseMotion>`        | MOUSE_MOTION       | Contiene información de movimiento, como|
|                                                                   |                    | pos. relativas y absolutas, velocidad   |
+-------------------------------------------------------------------+--------------------+-----------------------------------------+
| :ref:`InputEventJoystickMotion <class_InputEventJoystickMotion>`  | JOYSTICK_MOTION    | Contiene información de ejes análogos   |
|                                                                   |                    | de Joystick/Joypad                      |
+-------------------------------------------------------------------+--------------------+-----------------------------------------+
| :ref:`InputEventJoystickButton <class_InputEventJoystickButton>`  | JOYSTICK_BUTTON    | Contiene información de botones de      |
|                                                                   |                    | Joystick/Joypad                         |
+-------------------------------------------------------------------+--------------------+-----------------------------------------+
| :ref:`InputEventScreenTouch <class_InputEventScreenTouch>`        | SCREEN_TOUCH       | Contiene información multi-touch de     |
|                                                                   |                    | presionar/soltar. (solo disponible en   |
|                                                                   |                    | dispositivos móviles)                   |
+-------------------------------------------------------------------+--------------------+-----------------------------------------+
| :ref:`InputEventScreenDrag <class_InputEventScreenDrag>`          | SCREEN_DRAG        | Contiene información multi-touch de     |
|                                                                   |                    | arrastre (solo en disp. móviles)        |
+-------------------------------------------------------------------+--------------------+-----------------------------------------+
| :ref:`InputEventAction <class_InputEventAction>`                  | SCREEN_ACTION      | Contiene una acción genérica. estos     |
|                                                                   |                    | eventos suelen generarse por el program-|
|                                                                   |                    | ador como feedback. (mas de esto abajo) |
+-------------------------------------------------------------------+--------------------+-----------------------------------------+

Acciones
-------

Un InputEvent puede o no representar una acción predefinida. Las
acciones son útiles porque abstraen el dispositivo de entrada cuando
programamos la lógica de juego. Esto permite:

-  Que el mismo código trabaje en diferentes dispositivos con diferentes
   entradas (por ej. teclado en PC, Joypad en consola).
-  Entrada a ser reconfigurada en tiempo de ejecución.

Las acciones pueden ser creadas desde la Configuración de Proyecto en
la pestaña Actions. Lee :ref:`doc_simple_2d_game-input_actions_setup` para una
explicación de como funciona el editor de acciones.

Cualquier evento tiene los metodos :ref:`InputEvent.is_action() <class_InputEvent_is_action>`,
:ref:`InputEvent.is_pressed() <class_InputEvent_is_pressed>` y :ref:`InputEvent <class_InputEvent>`.

De forma alternativa, puede desearse suplir al juego de vuelta con
una acción desde el código (un buen ejemplo es detectar gestos).
SceneTree (derivado de MainLoop) tiene un método para esto:
:ref:`MainLoop.input_event() <class_MainLoop_input_event>`.
Se usaría normalmente de esta forma:

::

    var ev = InputEvent()
    ev.type = InputEvent.ACTION
    # ajustar como move_left, presionado
    ev.set_as_action("move_left", true)
    # feedback
    get_tree().input_event(ev)

InputMap (Mapa de controles)
--------

Personalizar y re mapear entrada desde código es a menudo deseado. Si
tu worflow depende de acciones, el singleton :ref:`InputMap <class_InputMap>`
es ideal para reasignar o crear diferentes acciones en tiempo de
ejecución. Este singleton no es guardado (debe ser modificado
manualmente) y su estado es corrido desde los ajustes de proyecto
(engine.cfg). Por lo que cualquier sistema dinámico de este tipo
necesita guardar su configuración de la forma que el programador
lo crea conveniente.
