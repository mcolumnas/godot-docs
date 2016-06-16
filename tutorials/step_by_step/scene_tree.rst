.. _doc_scene_tree:

Arbol de Escena (Scene Tree)
===============

Introduccion
------------

Aqui es donde las cosas se empiezan a poner abstractas, pero no entres
en panico, ya que no hay nada mas profundo que esto.

En los tutoriales previos, todo gira al rededor del concepto de Nodos,
las escenas estan hechas de ellos, y se vuelven activas cuando entran
a el *arbol de escena*.

Esto merece ir un poco mas profundo. De hecho, el sistema de escenas no
es siquiera un componente de nucleo de Godot, ya que es posible
ignorarlo y hacer un script (o codigo C++) que habla directamente con
los servidores. Pero hacer un juego de esa forma seria un monton de
trabajo y esta reservado para otros usos.

MainLoop (Ciclo Principal)
--------------------------

La forma en la que Godot trabaja internamente es la siguiente. Esta la
clase :ref:`OS <class_OS>`, la cual es la unica instancia que corre al
principio. Luego, todos los controladores, servidores, lenguajes de
scripting, sistema de escena, etc. son cargados.

Cuando la inicializacion esta completa, la clase :ref:`OS <class_OS>`
necesita el aporte de un :ref:`MainLoop <class_MainLoop>` para correr.
Hasta este punto, toda esta maquinaria es interna (puedes chequear el
archivo main/main.cpp en el codigo guente si alguna vez estas interesado
en ver como funciona esto internamente).

El programa de usuario, o juego, comienza en el MainLoop. Esta clase
tiene algunos metodos, para inicializacion, idle (llamadas de retorno
sincronizadas con los frames), fija (llamadas de retorno sincronizadas
con fisica), y entrada. Nuevamente, Esto es realmente de bajo nivel y
cuando haces juegos en Godot, escribir tu propio MainLoop ni siquiera
tiene sentido.

SceneTree (Arbol de Escena)
---------------------------

Una de las formas de explicar como Godot trabaja, es verlo como un
motor de juegos de alto nivel sobre una middleware (logica de
intercambio de informacion entre aplicaciones) de bajo nivel.

El sistema de escenas es el motor de juegos, mientras que :ref:`OS <class_OS>`
y los servidores son la API de bajo nivel.

En cualquier caso, el sistema de escenas provee su propio main loop al
sistema operativo, :ref:`SceneTree <class_SceneTree>`.

Esto es automaticamente instanciado y ajustado cuando se corre la
escena, no hay necesidad de trabajo extra.

Es importante saber que esta clase existe porque tiene algunos usos
importantes:

-  Contiene la raiz :ref:`Viewport <class_Viewport>`,
   cuando una escena se abre por primera vez, es agregado como un hijo
   de ella para volverse parte del *Arbol de Escena* (mas sobre esto
   luego)
-  Contiene informacion sobre los grupos, y tiene lo necesario para
   llamar a todos los nodos en un grupo, u obtener una lista de ellos.
-  Contiene algo de funcionalidad sobre estados globales, como son
   ajustar el modo de pausa, o terminar el proceso.

Cuando un nodo es parte de un Arbol de Escena, el singleton
:ref:`SceneTree <class_SceneTree>` puede ser obtenido simeplemente
llamando :ref:`Node.get_tree() <class_Node_get_tree>`.

Viewport Raiz
-------------

La raiz :ref:`Viewport <class_Viewport>`
siempre esta en lo mas alto de la escena. Desde un nodo, puede ser
obtenia de dos formas diferentes:

::

        get_tree().get_root() # acceso via scenemainloop
        get_node("/root") # acceso via camino absoluto

Este nodo contiene el viewport principa, cualquier cosa que sea hijo
de un :ref:`Viewport <class_Viewport>`
es dibujado dentro de el por defecto, por lo que tiene sentido que por
encima de todos los nodos siempre haya un nodo de este tipo, de otra
forma no se veria nada!

Mientras que otros viewports pueden ser creados en la escena (para
efectos de pantalla dividida o similar), este es el unico que nunca
es creado por el usuario. Es creado automaticamente dentro de
SceneTree.

Scene Tree (Arbol de Escena)
----------------------------

Cuando un nodo es conectado, directa o indirectamente, a la raiz del
viewport, se vuelve parte del *Scene Tree*.

Esto significa que, como se explico en tutoriales previos, obtendra
los llamados de retorno _enter_tree() y _ready() (as como _exit_tree())

.. image:: /img/activescene.png

Cuando los nodos entran a *Scene Tree*, se vuelven activos. Obtienen
acceso a todo lo que necesitan para procesar, obtener entradas,
mostrar 2D y 3D, notificaciones, reproducir sonidos, grupos, etc.
Cuando son removidos de la *Scene Tree*, lo pierden.

Orden del arbol
---------------

La mayoria de las operaciones con Nodos en Godot, como dibujar 2D,
procesar u obtener notificaciones son hechas en el orden de arbol.
Esto significa que los padres y hermanos con menor orden van a ser
notificados antes que el nodo actual.

.. image:: /img/toptobottom.png

"Volverse activo" por entrar la *Scene Tree*
--------------------------------------------

#. Una escena es cargada desde disco o creada por scripting.
#. El nodo raiz de dicha escena (solo una raiz, recuerdan?) es agregado
   como un hijo del Viewport "root" (desde SceneTree), o hacia
   cualquier hijo o nieto de el.
#. Todo nodo de la escena recientemente agregada, recibira la
   notificacion "enter_tree" ( llamada de retorno _enter_tree() en
   GDScript ) en orden de arriba hacia abajo.
#. Una notificacion extra, "ready" ( llamada de retorno _ready() en
   GDScript) se provee por conveniencia, cuado un nodo y todos sus
   hijos estan dentro de la escena activa.
#. Cuando una escena (o parte de ella) es removida, reciben la
   notificacion "exit scene" ( llamada de retorno _exit_tree()) en
   GDScript) en orden de abajo hacia arriba.

Cambiando la escena actual
----------------------

Luego que una escena es cargada, suele desearse cambiar esta escena
por otra. La forma simple de hacer esto es usar la funcion
:ref:`SceneTree.change_scene() <class_SceneTree_change_scene>`:

::

    func _mi_nivel_fue_completado():
        get_tree().change_scene("res://levels/level2.scn")

Esta es una forma facil y rapida de cambiar de escenas, pero tiene
la desventaja de que el juego se detendra hasta que la nuva escena
esta cargada y corriendo. En algun punto de tu juego, puede ser
deseable crear una pantalla de carga con barra de progresa adecuada,
con indicadores animados o carga por thread (en segundo plano).
Esto debe ser hecho manualmente usando autoloads (ve el proximo
capitulo!) y :ref:`doc_background_loading`.
