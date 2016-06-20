.. _doc_pausing_games:

Pausando juegos
===============

Pausa?
------

En la mayoría de los juegos es deseable, en algún punto, interrumpir el
juego para hacer algo mas, como descansar o cambiar opciones. Sin
embargo esto no es tan simple como parece. El juego podría estar
detenido, pero puede quererse que algunos menús y animaciones continúen
trabajando.

Implementar un control fino de lo que puede ser pausado (y lo que no) es
un montón de trabajo, por lo que un framework simple para pausar es
incluido en Godot.

Como funciona la pausa
----------------------

Para poner el modo pausa, el estado de pausa debe ser ajustado. Esto
es hecho llamando :ref:`SceneTree.set_pause() <class_SceneTree_set_pause>` con
el argumento "true":


::

    get_tree().set_pause(true)

Hacerlo tendrá el siguiente comportamiento:

-  La física 2D y 3D será detenida.
-  _process y _fixed_process no serán mas llamados en los nodos.
-  _input y _input_event no serán mas llamados tampoco.

Esto efectivamente detiene del juego por completo. Llamar esta función
desde un script, por defecto, resultara en un estado no recuperable
(nada seguirá funcionando!).

Lista blanca de nodos
-------------------

Antes de habilitar la pausa, asegúrate que los nodos que deben seguir
trabajando durante la pausa están en la lista blanca. Esto es hecho
editando la propiedad "Pause Mode" en el nodo:

.. image:: /img/pausemode.png

Por defecto todos los nodos tienen esta propiedad en el estado "Inherit"
(Heredar). Esto significa, que solo van a procesar (o no) dependiendo
en que esta ajustada esta propiedad en el nodo padre. Si el padre esta en
"Inherit", entonces se chequeara el abuelo, y así sigue. Al final, si
un estado no puede ser encontrado en ninguno de los abuelos, el estado
de pausa de SceneTree es usado. Esto implica que, por defecto, cuando el
juegos es pausado todo nodo será pausado.

Asique los tres estados posibles para nodos son:


-  **Inherit**: Procesar dependiendo en el estado del padre, abuelo,
   etc. El primer padre que tenga un estado no heredado.
-  **Stop**: Detiene el nodo no importa que (e hijos en el modo
   Inherit). Cuando en pausa, este nodo no procesa.
-  **Process**: Procesar el nodo no importa que (y los hijos en modo
   Inherit). Pausado o no, este nodo procesara.

Ejemplo
-------

Un ejemplo de esto es crear una ventana emergente o panel con controles
dentro, y ajustar su modo pausa a "Process" luego solo esconderlo:

.. image:: /img/pause_popup.png

Solo con ajustar la raíz de la ventana emergente de pausa a "Process",
todos los hijos y nietos heredaran el estado. De esta forma, esta rama
del árbol de escena continuara trabajando cuando este en pausa.

Finalmente, haz que cuando el botón de pausa es presionado (cualquier
botón servirá), se habilite la pausa y muestre la pantalla de pausa.

::

    func _on_pause_button_pressed():
        get_tree().set_pause(true)
        get_node("pause_popup").show()

Para quitar la pausa, solo has lo opuesto cuando la ventana de pausa
es cerrada:


::

    func _on_pause_popup_close_pressed():
        get_node("pause_popup").hide()
        get_tree().set_pause(false)

Y eso debería ser todo!
