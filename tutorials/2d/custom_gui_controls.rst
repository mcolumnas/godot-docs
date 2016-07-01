.. _doc_custom_gui_controls:

Controles GUI personalizados
============================

Tantos controles...
-------------------

De todas formas nunca son suficientes. Crear tus propios controles
personalizados que actúan exactamente como quieres es una obsesión de
casi todo programador GUI. Godot provee muchos, pero puede que no
funcionen exactamente en la forma que deseas. Antes de contactar a
los desarrolladoras con un pull-request para soportar barras de
desplazamiento diagonales, al menos seria bueno saber como crear
estos controles fácilmente desde script.

Dibujando
---------

Para dibujar, es recomendado chequear el tutorial :ref:`doc_custom_drawing_in_2d`.
Lo mismo aplica. Vale la pena mencionar algunas funciones debido a su utilidad
para dibujar, asique las detallaremos a continuación:

Chequeando el tamaño de un control
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

A diferencia de los nodos 2D, "size"(tamaño) es muy importante para los
controles, ya que ayuda a organizarlos de forma apropiada. Para esto, se
provee el método :ref:`Control.get_size() <class_Control_get_size>`.
Chequearlo durante _draw() es vital para asegurar que todo este dentro
de los limites.

Chequeando el foco
~~~~~~~~~~~~~~~~~~

Algunos controles (como botones o editores de texto) podrían proveer
foco de entrada para teclado o joypad. Ejemplos de esto son ingresar
texto o presionar un botón. Esto es controlado con la función
:ref:`Control.set_focus_mode() <class_Control_set_focus_mode>`.
Cuando dibujas, y si el control soporta foco de entrada, siempre es
deseable mostrar algún tipo de indicador (highight, box, etc) para
indicar que este es el control que tiene el foco. Para chequear por
este estatus, existe :ref:`Control.has_focus() <class_Control_has_focus>`.

::

    func _draw():
        if (has_focus()):
             draw_selected()
        else:
             draw_normal()

Tamaño
------

Como mencionamos antes, el tamaño es muy importante para los controles.
Esto permite distribuirlos adecuadamente, cuando se ajustan en grillas,
contenedores, o anclas. Los controles la mayoría del tiempo proveen un
*minimum size* para ayudar a distribuirlos adecuadamente. Por ejemplo,
si los controles son puestos verticalmente encima uno de otro usando
un :ref:`VBoxContainer <class_VBoxContainer>`, el tamaño mínimo nos
asegura que tu control personalizado no será aplastado por los demás
controles en el contenedor.

Para proveer esta llamada de retorno, solo sobrescribe
:ref:`Control.get_minimum_size() <class_Control_get_minimum_size>`,
por ejemplo:
::

    func get_minimum_size():
        return Vector2(30,30)

O de forma alternativa, ajústalo a través de una función:

::

    func _ready():
        set_custom_minimum_size( Vector2(30,30) )

Entrada
-----

Los controles proveen algunos ayudantes para hacer la gestión de eventos
de entrada mucho mas sencillos que los nodos regulares.

Eventos de entrada
~~~~~~~~~~~~~~~~~~

Hay algunos tutoriales sobre entrada antes de este, pero es valioso
mencionar que los controles tienen un método especial de entrada que
solo funciona cuando:

-  El puntero del mouse esta sobre el control.
-  El botón fue presionado sobre el control (control siempre captura
   la entrada hasta que el botón se suelta)
-  El control provee foco de teclado/joypad con
   :ref:`Control.set_focus_mode() <class_Control_set_focus_mode>`.

Esta simple función es
:ref:`Control._input_event() <class_Control__input_event>`.
Solo sobrescríbela en tu control. No hay necesidad de ajustar el
procesamiento.

::

    extends Control

    func _input_event(ev):
       if (ev.type==InputEvent.MOUSE_BUTTON and ev.button_index==BUTTON_LEFT and ev.pressed):
           print("Botón izquierdo de mouse presionado!")

Para mas información sobre los eventos mismos, chequea el tutorial
:ref:`doc_inputevent`.

Notificaciones
~~~~~~~~~~~~~~

Los controles también pueden tener muchas notificaciones útiles para
las cuales no hay llamada de retorno, pero pueden ser chequeadas con
la llamada de retorno _notification:


::

    func _notification(what):

       if (what==NOTIFICATION_MOUSE_ENTER):
          pass # el mouse entro al área de este control
       elif (what==NOTIFICATION_MOUSE_EXIT):
          pass # el mouse salió del área de este control
       elif (what==NOTIFICATION_FOCUS_ENTER):
          pass # el control tiene foco
       elif (what==NOTIFICATION_FOCUS_EXIT):
          pass # el control perdió el foco
       elif (what==NOTIFICATION_THEME_CHANGED):
          pass # el tema usado para dibujar el control cambio
          # update y redraw son recomendados si se usa un tema
       elif (what==NOTIFICATION_VISIBILITY_CHANGED):
          pass # el control se volvió visible/invisible
          # chequea el nuevo estado si is_visible()
       elif (what==NOTIFICATION_RESIZED):
          pass # el control cambio de tamaño, chequea el nuevo tamaño
          # con get_size()
       elif (what==NOTIFICATION_MODAL_CLOSED):
          pass # para ventanas emergentes modales, notificación
               # que la ventana emergente fue cerrada
