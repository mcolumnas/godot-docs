.. _doc_ray-casting:

Ray-casting (Emision de rayos)
==============================

Introducción
------------

Una de las tareas mas comunes en el desarrollo de juegos es emitir un
rayo (o un objeto con una forma personalizada) y chequear que golpea.
Esto permite que comportamientos complejos, AI, etc. tengan lugar.
Este tutorial va a explicar como hacer esto en 2D y 3D.

Godot guarda toda la información de bajo nivel del juego en servidores,
mientras que la escena es solo una interfaz. Como tal, ray casting en
general es una tarea de bajo-nivel. Para raycasts simples, nodos como
:ref:`RayCast <class_RayCast>` y :ref:`RayCast2D <class_RayCast2D>`
funcionaran, ya que regresaran en cada frame cual es el resultado del
raycast.

Muchas veces, sin embargo, ray-casting necesita ser un proceso mas
interactivo por lo que una forma de hacer esto por código debe existir.

Espacio
-------

En el mundo físico, Godot guarda todas las colisiones de bajo nivel e
información física en un *space*. El actual espacio 2D (para física 2D)
puede ser obtenido al llamar
:ref:`CanvasItem.get_world_2d().get_space() <class_CanvasItem_get_world_2d>`.
Para 3D, es :ref:`Spatial.get_world().get_space() <class_Spatial_get_world>`.

El espacio resultante :ref:`RID <class_RID>` puede ser usado en
:ref:`PhysicsServer <class_PhysicsServer>` y
:ref:`Physics2DServer <class_Physics2DServer>` para 3D y 2D
respectivamente.

Accediendo al espacio
---------------------

La física de Godot corre por defecto en el mismo thread que la lógica del
juego, pero puede ser ajustada para correr en un thread separado para
funcionar mas eficientemente. Debido a esto, el único momento donde acceder
al espacio es seguro es durante la llamada de retorno
:ref:`Node._fixed_process() <class_Node__fixed_process>`.
Accediéndola desde fuera de esta función puede resultar en un error
debido a que el espacio esta *locked*(bloqueado).

Para realizar cónsulas en espacio físico, la clase
:ref:`Physics2DDirectSpaceState <class_Physics2DDirectSpaceState>` y
ref:`PhysicsDirectSpaceState <class_PhysicsDirectSpaceState>` deben
ser usadas.

En código, para 2D spacestate, este código debe ser usado:

::

    func _fixed_process(delta):
        var space_rid = get_world_2d().get_space()
        var space_state = Physics2DServer.space_get_direct_state(space_rid)

Por supuesto, hay un atajo mas simple:

::

    func _fixed_process(delta):
        var space_state = get_world_2d().get_direct_space_state()

Para 3D:

::

    func _fixed_process(delta):
        var space_state = get_world().get_direct_space_state()

Consulta Raycast
----------------

Para realizar una consulta raycast 2D, el método
:ref:`Physics2DDirectSpaceState.intersect_ray() <class_Physics2DDirectSpaceState_intersect_ray>`
debe ser usado, por ejemplo:

::

    func _fixed_process(delta):
        var space_state = get_world().get_direct_space_state()
        # usa coordenadas globales, no locales al nodo
        var result = space_state.intersect_ray( Vector2(0,0), Vector2(50,100) )

El resultado es un diccionario. Si el rayo no pego contra nada, el
diccionario estará vacío. Si pego algo entonces tendrá la información de
colisión:

::

        if (not result.empty()):
            print("Golpe en el punto: ",result.position)

El diccionario de resultado de colisión, cuando pega en algo, tiene este
formato:

::

    {
       position:Vector2 # punto en el world space para la colisión
       normal:Vector2 # normal en el world space para colisión
       collider:Object # Objeto colisionado o null (si no esta asociado)
       collider_id:ObjectID # Objeto contra el que colisiono
       rid:RID # RID contra el que colisiono
       shape:int # indice de forma del colisionador
       metadata:Variant() # metadata del colisionador
    }

    # En caso de 3D, Vector3 es regresado.

Excepciones de colisiones
-------------------------

Es un caso muy común el intentar emitir un rayo desde un personaje u
otra escena del juego para tratar de inferir propiedades de el mundo
a su al rededor. El problema con esto es que el mismo personaje tiene
un colisionador, por lo que el rayo nunca deja el origen (se mantendrá
golpeando su propio colisionador), como queda en evidencia en la
siguiente imagen.

.. image:: /img/raycast_falsepositive.png

Para evitar la intersección propia, la función intersect_ray() puede
tomar un tercer parámetro opcional que es un arreglo de excepciones.
Este es un ejemplo de como usarlo desde un KinematicBody2D o cualquier
otro nodo basado en CollisionObject:

::

    extends KinematicBody2D

    func _fixed_process(delta):
        var space_state = get_world().get_direct_space_state()
        var result = space_state.intersect_ray( get_global_pos(), enemy_pos, [ self ] )

El argumento extra es una lista de excepciones, pueden ser objetos o
RIDs.

Ray casting 3D desde  pantalla.
-------------------------------

Emitir un rayo desde pantalla a un espacio físico 3D es útil para
tomar objetos. No hay mucha necesidad de hacer esto porque
:ref:`CollisionObject <class_CollisionObject>` tiene una señal
"input_event" que te permite saber cuando es pinchada, pero en caso
que haya deseo de hacerlo manualmente, aquí esta como.

Para emitir un rayo desde la pantalla, el nodo :ref:`Camera <class_Camera>`
es necesario. La cámara puede estar en dos modos de proyección,
perspectiva y ortogonal. Por este motivo, ambos el origen del rayo y la
dirección deben obtenerse. (el origen cambia en ortogonal, mientras
que la dirección cambia en perspectiva):

.. image:: /img/raycast_projection.png

Para obtenerlo usando una cámara, el siguiente código puede ser usado:

::

    const ray_length = 1000

    func _input(ev):
        if ev.type==InputEvent.MOUSE_BUTTON and ev.pressed and ev.button_index==1:

              var camera = get_node("camera")
              var from = camera.project_ray_origin(ev.pos)
              var to = from + camera.project_ray_normal(ev.pos) * ray_length

Por supuesto, recuerda que durante ``_input()``, el espacio puede estar
locked, así que guarda tu consulta para ``_fixed_process()``.
