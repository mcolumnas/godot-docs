.. _doc_mouse_and_input_coordinates:

Coordenadas de mouse y entrada
===========================

Acerca
------

La razon de este pequeño tutorial es aclarar muchos errores comunes
sobre las coordenadas de entrada, obtener la posicion del mouse y
resolucion de pantalla, etc.

Coordanadas de pantalla de hardware
-----------------------------------

Usar coordenadas de hardware tiene sentido en el caso de escribir UIs
complejas destinada a correr en PC, como editores, MMOs, herramientas,
etc. De todas formas, no tiene mucho sentido fuera de ese alcance.

Coordenadas de pantalla de viewport
-----------------------------------

Godot usa viewports para mostrar contenido, los cuales puede ser
escalados de varias maneras (vee el tutorial
:ref:`doc_multiple_resolutions`). Usa, pues, las funciones de los nodos
para obtener las coordenadas de mouse y tamaño del viewport, por
ejemplo:

::

    func _input(ev):
       # Mouse en coordenadas viewport

       if (ev.type==InputEvent.MOUSE_BUTTON):
           print("El mouse fue Click/Unclick en: ",ev.pos)
       elif (ev.type==InputEvent.MOUSE_MOTION):
           print("Movimiento de mouse en: ",ev.pos)

       # Imprime el tamaño del viewport

       print("La resolucion del viewport es: ",get_viewport_rect().size)

    func _ready():
        set_process_input(true)

Alternativamente es posible pedir al viewport la posicion del mouse:

::

    get_viewport().get_mouse_pos()
