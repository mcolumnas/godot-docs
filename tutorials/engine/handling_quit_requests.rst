.. _doc_handling_quit_requests:

Manejando requerimientos de quit (abandono)
============================================

Abandonando
-----------

La mayoria de las plataformas tienen la opcion de pedir que la
aplicacion abandone. En escritorios, esto es usualmente hecho con el
icono "x" en la barra de titulo de la ventana. En Android, el boton
back (atras) es usado para abandonar cuando estas en la ventana
principal (o para ir atras si no es la ventana principal).

Manejando la notificacion
-------------------------

El :ref:`MainLoop <class_MainLoop>` tiene una notificacion especial
que es enviada a todos los nodos cuando se requiere abandonar:
MainLopp.NOTIFICATION_WM_QUIT.

Se maneja de la siguiente forma (en cualquier nodo):

::

    func _notification(what):
        if (what == MainLoop.NOTIFICATION_WM_QUIT_REQUEST):
            get_tree().quit() # comportamiento por defecto

Cuando desarrollas aplicaciones moviles, abandonar no es deseado a no
ser que el usuario este en la pantalla principal, por lo que el
comportamiento puede cambiar.

Es importante notar que por defecto, las aplicaciones de Godot tienen
comportamiento incorporado para abandonar cuando se les pide quit,
esto puede ser cambiado:

::

    get_tree().set_auto_accept_quit(false)
