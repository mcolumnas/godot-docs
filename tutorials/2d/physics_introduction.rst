.. _doc_physics_introduction:

Introducción a la física
========================

Nuestro mundo esta hecho de materia tangible. En nuestro mundo, un piano
no puede ir a través de la pared cuando es ingresado a una casa. Necesita
usar la puerta. Los videojuegos a menudo son como el mundo real por lo
que Pac-Man no puede ir a través de las paredes de su laberinto (aunque
se puede tele transportar desde el lado izquierdo al derecho de la
pantalla y viceversa).

Es decir, mover sprites por ahí es lindo pero un día tienen que
colisionar adecuadamente, asique vayamos al punto.

Shapes (Formas)
---------------

El objeto base que puede colisionar en el mundo 2D de Godot es un
:ref:`Shape2D <class_Shape2D>`.

-  :ref:`CircleShape2D <class_CircleShape2D>`
-  :ref:`RectangleShape2D <class_RectangleShape2D>`
-  :ref:`CapsuleShape2D <class_CapsuleShape2D>`
-  :ref:`ConvexPolygonShape2D <class_ConvexPolygonShape2D>`
-  :ref:`ConcavePolygonShape2D <class_ConcavePolygonShape2D>`
-  etc. (hay mas, chequea la lista de clases).

Shapes pueden ser del tipo :ref:`Resource <class_Resource>`,
pero pueden ser creados desde código fácilmente. Por ejemplo:

::

    # Crea un círculo
    var c = CircleShape2D.new()
    c.set_radius(20)

    # Crea una caja
    var b = RectangleShape2D.new()
    b.set_extents(Vector2(20,10))

El uso principal de los shapes es chequear colisiones/intersecciones y
obtener información de resolución. Son principalmente convexas, (excepto
concavepolygon, que no es mas que una lista de segmentos contra la cual
chequear colisiones). Este chequeo de colisión es hecho fácilmente con
funciones incorporadas como:

::

    # Chequea si hay una colisión entre dos formas, cada una tiene un transform
    if b.collide(b_xform, a, a_xform):
        print("OMG Colisión!")

Godot retornara informació correcta de colisión y de información de
colisión desde las diferentes llamadas a la API Shape2D. La colision
entre todas las formas y transformaciones pueden ser hechas de esta
manera, o aun obtener información de contacto, motion casting, etc.

Transformando shapes
~~~~~~~~~~~~~~~~~~~~

Como vimos antes en las funciones de colisión, los shapes 2D en Godot
pueden ser transformados usando una transformación regular
:ref:`Matrix32 <class_Matrix32>`, lo que significa que puede chequear
una colisión cuando es escalada, movida o rotada. La única limitación
para esto es que las formas con secciones curvas (como circulo y capsula)
pueden solo ser escaladas uniformemente. Esto implica que las formas de
circulo o capsula escaladas en forma de elipse **no funcionaran
adecuadamente**. Esta es una limitación en el algoritmo de colisión
usado (SAT), así que asegúrate que tus formas de circulo y capsula
siempre se escalen uniformemente!


.. image:: /img/shape_rules.png

Cuando comienzan los problemas
------------------------------

Aunque esto suena bien, la realidad es que usualmente la detección de
colisiones sola no es suficiente en la mayoría de los escenarios. Muchos
problemas aparecen en la medida que el desarrollo del juego progresa:

Demasiadas combinaciones!
~~~~~~~~~~~~~~~~~~~~~~

Los juegos tienen varias docenas, cientos, miles! de objetos que pueden
colisionar y ser colisionados. El enfoque típico es probar todo contra
todo en dos bucles for como este:

::

    for i in colliders:
        for j in colliders:
            if (i.collides(j)):
                do_collision_code()

Pero esto escala realmente mal. Imaginemos que hay solo 100 objetos en
el juego. Esto implica que 100*100=10000 colisiones necesitaran ser
probadas en cada frame. Esto es mucho!

Ayuda visual
~~~~~~~~~~~~

La mayoría del tiempo, crear un shape con código no es suficiente.
Necesitamos ubicarlo visualmente sobre nuestro sprite, dibujar un
polígono de colisión, etc. Es obvio que necesitamos nodos para crear
las formas de colisión apropiadas en una escena.

Resolución de colisión
~~~~~~~~~~~~~~~~~~~~~~

Imagina que resolvimos el problema de colisión, podemos saber de forma
fácil y rápida que formas se superponen. Si muchas de ellas son objetos
dinámicos que se mueven por ahí, o que se mueven de acuerdo a física
newtoniana, resolver la colisión de muchos objetos puede ser realmente
difícil desde código.

Introduciendo... Motor de física de Godot!
--------------------------------------

Para resolver todos esos problemas, Godot tiene un motor de física y
colisión que esta bien integrado en el sistema de escena, y de todas
formas permite diferentes niveles y capas de funcionalidad. El motor
de física incorporado puede ser usado para:

-  Detección simple de colisión: Ve la API :ref:`Shape2D <class_Shape2D>`
-  Cinemática de Escena: Maneja formas, colisiones, broadphase, etc
   como nodos. Ve :ref:`Area2D <class_Area2D>`.
-  Física de Escena: Cuerpos rígidos y restricciones como nodos. Ve
   :ref:`RigidBody2D <class_RigidBody2D>`, y los nodos joint.

Unidad de medida
~~~~~~~~~~~~~~~~

A menudo es un problema cuando se integra un motor de física 2D en un
juego, que dichos motores están optimizados para trabajar usando metros
como unidad de medida. Godot utiliza un motor de física 2D incorporado
y personalizado que esta diseñado para funcionar adecuadamente en pixels,
así que todas las unidades y valores por defecto usados para
estabilización están preparados para esto, haciendo el desarrollo mas
directo.

CollisionObject2D
-----------------

:ref:`CollisionObject2D <class_CollisionObject2D>` es el nodo (virtual)
base para todo lo que puede colisionar en 2D. Area2D, StaticBody2D,
KinematicBody2D y RigidBody2D todos heredan desde el. Este nodo contiene
una lista de formas (Shape2D) y una transformación relativa. Esto
significa que todos los objetos colisionables en Godot pueden usar
múltiples formas en diferentes transformaciones (offset/scale/rotation).
Solo recuerda que, como mencionamos antes, **escalado no uniforma no
funcionara para formas de circulo o capsulas**.

.. image:: /img/collision_inheritance.png

StaticBody2D
~~~~~~~~~~~~

El nodo mas simple en el motor de física es el StaticBody2D, el cual
provee una colisión estática. Esto implica que otros objetos pueden
colisionar contra el, pero StaticBody2D no se moverá por si mismo o
generara algún tipo de interacción cuando colisione otros cuerpos.
Esta allí solo para ser colisionado.

Crear uno de estos cuerpos no alcanza, porque carece de colisión:

.. image:: /img/collision_inheritance.png

Por el punto previo, sabemos que los nodos derivados de
ColiisionObject2D tienen una lista interna de formas y transformaciones
para colisiones, pero como lo editamos? Hay dos nodos especiales para
ello.

CollisionShape2D
~~~~~~~~~~~~~~~~

Este nodo es un nodo ayudante. Debe ser creado como un hijo directo de
un nodo derivado de CollisionObject2D: :ref:`Area2D <class_Area2D>`,
:ref:`StaticBody2D <class_StaticBody2D>`, :ref:`KinematicBody2D <class_KinematicBody2D>`,
:ref:`RigidBody2D <class_RigidBody2D>`.

Por si mismo no hace nada, pero cuando se crea como hijo de los nodos
mencionados arriba, les agrega forma de colisión. Cualquier cantidad
de hijos CollisionShape2D pueden ser creados, lo que significa que
el objeto padre simplemente tendrá mas formas de colisión. Cuando se
agrega/borra/mueve/edita, actualiza la lista de formas en el nodo padre.

En tiempo de ejecución, sin embargo, esto nodo no existe (no puede ser
accedido con ``get_node()``), ya que solo esta destinado para ser un
ayudante en el editor. Para acceder a las formas en tiempo de ejecución,
usa la API CollisionObject2D directamente.

Como un ejemplo, aquí esta la escena del juego plataformer (se puede
descargar junto con los demás demos desde el sitio oficial de Godot),
conteniendo un Area2D con hijo CollisionObject2D como icono de moneda:

.. image:: /img/area2dcoin.png

Triggers (disparadores)
~~~~~~~~~~~~~~~~~~~~~~~

Un CollisionShape2D o CollisionPolygon2D puede ser ajustado como un
trigger. Cuando se usa en un RigidBody2D o KinematicBody2D, las formas
"trigger" se vuelven no colisionables (los objetos no pueden colisionar
contra el). Solo se mueven al rededor de los objetos como fantasmas.
Esto los vuelve útiles en dos situaciones:

-  Deshabilitar la colisión en una forma especifica.
-  Hacer que Area2D dispare una señal body_enter / body_exit con
   objetos no colisionables (útil en varias situaciones).

CollisionPolygon2D
~~~~~~~~~~~~~~~~~~

Este es similar a CollisionShape2D, excepto que en lugar de asignarse
una forma, un polígono puede ser editado (dibujado por el usuario) para
determinar la forma. El polígono puede ser convexo o cóncavo, no importa.

Yendo atrás, aquí esta la escena con el StaticBody2D, el static body es
hijo de un sprite (lo que significa que si el sprite se mueve, la
colisión también lo hace). CollisionPolygon es un hijo de staticbody,
lo que implica que le agrega formas de colisión.

.. image:: /img/spritewithcollision.png

De hecho, lo que hace CollisionPolygon es descomponer el polígono en
formas convexas (los shapes solo pueden ser convexos, recuerdan?) y
agregarla a CollisionObject2D:

.. image:: /img/decomposed.png

KinematicBody2D
~~~~~~~~~~~~~~~

:ref:`KinematicBody2D <class_KinematicBody2D>` son tipos especiales de
cuerpos que están pensados para ser controlados por el usuario. No son
afectados por la física en lo absoluto (para otros tipos de cuerpos,
como un character o rigidbody, estos son los mismos que un staticbody).
Tienen sin embargo, dos usos principales:

-  **Movimiento Simulado**: Cuando estos cuerpos son movidos manualmente,
   ya sea desde código o desde :ref:`AnimationPlayer <class_AnimationPlayer>`
   (con proccess mode ajustado a fixed!), la física va a computar
   automáticamente un estimado de su velocidad linear y angular. Esto
   los vuelve muy útiles para mover plataformas u otros objetos
   controlados desde AnimationPlayer (como una puerta, un puente que se
   abre, etc). Como un ejemplo, el demo 2d/platformer los usa para mover
   plataformas.
-  **Personajes Cinemáticos**: KinematicBody2D también tiene una API para
   mover objetos (la función move()) mientras realiza pruebas de colisión.
   Esto los vuelve realmente útiles para implementar personajes que
   colisionan contra un mundo, pero que no requieren física avanzada. Hay
   un tutorial sobre esto :ref:`doc_kinematic_character_2d`.

RigidBody2D
~~~~~~~~~~~

Este tipo de cuerpo simula física newtoniana. Tiene masa, fricción,
rebote, y las coordenadas 0,0 simulan el centro de masa. Cuando se
necesita física real, :ref:`RigidBody2D <class_RigidBody2D>` es el
nodo ha usar. El movimiento de este cuerpo es afectado por la gravedad
y/u otros cuerpos.

Los Rigid bodies suelen estar activos todo el tiempo, pero cuando
terminan en una posición de descanso y no se mueven por un rato, son
puestos a dormir hasta que algo los despierte. Esto ahorra una cantidad
enorme de CPU.

Los nodos RigidBody2D actualizan sus trasformaciones constantemente, ya
que es generada por la simulación desde una posición, velocidad linear y
velocidad angular. Como resultado, [STRIKEOUT:this node can't be scaled].
Escalar los nodos hijos debería funcionar sin embargo.

Como un plus, ya que es muy común en los juegos, es posible cambiar un
nodo RigidBody2D para que se comporte como un Character (sin rotación),
StaticBody o KinematicBody de acuerdo a diferentes situaciones (ejemplo,
un enemigo congelado por hielo se vuelve un StaticBody).

La mejor forma de interactuar con un RigidBody2D es durante la llamada
de retorno force integration. En este momento exacto, el motor físico
sincroniza estado con la escena y permite modificación completa de los
parámetros internos (de otra forma, ya que puede estar corriendo en un
thread, los cambios no tendrán lugar hasta el siguiente frame). Para
hacer esto, la siguiente función debe ser sobrescrita:

::

    func _integrate_forces(state):
        [use state to change the object]

El parámetro "state" es del tipo :ref:`Physics2DDirectBodyState <class_Physics2DDirectBodyState>`.
Por favor no uses este objeto (estado) fuera de la llamada de retorno
ya que resultara en un error.

Reporte de contacto
-------------------

En general, RigidBody2D no se mantendrá al corriente de los contactos,
ya que esto puede requerir una cantidad enorme de memoria si miles de
rigid bodies están en la escena. Para tener los contactos reportados,
simplemente aumenta la cantidad de la propiedad "contacts reported" desde
cero hasta un valor que tenga sentido (dependiendo en cuantos esperas
obtener). Los contactos pueden ser las tarde obtenidos con
:ref:`Physics2DDirectBodyState.get_contact_count() <class_Physics2DDirectBodyState_get_contact_count>`
y las funciones relacionadas.

El monitoreo de contactos a través de señales también esta disponible
( las señales son similares a las de Area2D, descritas mas abajo) con
propiedades booleanas.

Area2D
~~~~~~

Las áreas en la física de Godot tienen tres roles principales:

1. Sobrescribir los parámetros de espacio para objetos que entran a
ellas (ejemplo: gravedad, dirección de gravedad, tipo de gravedad,
densidad, etc).

2. Monitorear cuando rigid o kinematic bodies entrar o salen del área.

3. Monitorear otras áreas (esta es la forma mas simple de probar la
superposición)

La segunda función es la mas común. Para que funcione, la propiedad
"monitoring" debe estar habilitada (lo esta por defecto). Hay dos tipos
de señales emitidas por este nodo:

::

    # Notificación simple, de alto nivel
    body_enter(body:PhysicsBody2D)
    body_exit(body:PhysicsBody2D)
    area_enter(area:Area2D)
    area_exit(body:Area2D)

    # Notificación de bajo nivel basada en shape
    # Notifica específicamente cual shape en ambos, cuerpo y área, están en contacto
    body_enter_shape(body_id:int,body:PhysicsBody2D,body_shape_index:int,area_shape_index:idx)
    body_exit_shape(body_id:int,body:PhysicsBody2D,body_shape_index:int,area_shape_index:idx)
    area_enter_shape(area_id:int,area:Area2D,area_shape_index:int,self_shape_index:idx)
    area_exit_shape(area_id:int,area:Area2D,area_shape_index:int,self_shape_index:idx)

Por defecto, las áreas también reciben entrada de mouse/touchscreen,
proveyendo una forma de mas bajo nivel que controla la implementación
de este tipo de entrada en un juego.

En caso de overlap, quien recibe la información de colisión?
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

Recuerda que no toda combinación de dos cuerpos puede hacer "report"
de contactos. Los cuerpos Static son pasivos y no reportaran contacto
cuando son tocados. Los cuerpos Kinematic reportaran contactos pero solo
contra cuerpos Rigid/Character. Area2D reportara overlap (no el contacto
detallado) con cuerpos y otras áreas. La siguiente tabla debería hacerlo
mas visual:

+-------------------+-------------+-----------------+-----------------+---------------+--------+
| **Tipo**          | *RigidBody* | *CharacterBody* | *KinematicBody* | *StaticBody*  | *Area* |
+===================+=============+=================+=================+===============+========+
| **RigidBody**     | Both        | Both            | Both            | Rigidbody     | Area   |
+-------------------+-------------+-----------------+-----------------+---------------+--------+
| **CharacterBody** | Both        | Both            | Both            | CharacterBody | Area   |
+-------------------+-------------+-----------------+-----------------+---------------+--------+
| **KinematicBody** | Both        | Both            | None            | None          | Area   |
+-------------------+-------------+-----------------+-----------------+---------------+--------+
| **StaticBody**    | RigidBody   | CharacterBody   | None            | None          | None   |
+-------------------+-------------+-----------------+-----------------+---------------+--------+
| **Area**          | Area        | Area            | Area            | None          | Both   |
+-------------------+-------------+-----------------+-----------------+---------------+--------+

Variables globales de física
----------------------------

Algunas variables globales pueden ser modificadas en la configuración
de proyecto para ajustar como funciona la física 2D:

.. image:: /img/physics2d_options.png

Dejarlos sin tocar es lo mejor (excepto para la gravedad, que necesita
ser ajustada en la mayoría de los juegos), pero hay un parámetro
especifico que puede necesitar ser ajustado "cell_size". El motor físico
de Godot usa por defecto un algoritmo de space hashing que divide el
espacio en celdas para computar pares de colisiones cercanas mas
eficientemente.

Si un juego utiliza varios colisionadores que son realmente pequeños y
ocupan una pequeña porción de la pantalla, puede ser necesario encoger
ese valor (siempre a una potencia de 2) para mejorar la eficiencia. De
la misma manera si un juego usa pocos colisionadores grandes que cubren
un mapa gigante (del tamaño de varias pantallas), aumentar ese valor
un poco puede ayudar a salvar recursos.

Llamada de retorneo fixed process
---------------------------------

El motor físico puede iniciar múltiples threads para mejorar el
rendimiento, para que puede utilizar hasta un frame completo para
procesar física. Por este motivo, cuando se acceden variables de física
como la posición, velocidad linear, etc. pueden no ser representativas
de lo que esta sucediendo en el frame actual.

Para resolver esto, Godot tiene una llamada de retorno fixed process,
que es como process pero es llamada una vez por frame de física (por
defecto 60 veces por segundo). Durante este tiempo, el motor de física
esta en estado de *sincronización* y puede ser accedido directamente
sin demoras.

Para habilitar una llamada de retorno fixed process, usa la función
``set_fixed_process()``, ejemplo:

::

    extends KinematicBody2D

    func _fixed_process(delta):
        move(direction * delta)

    func _ready():
        set_fixed_process(true)


Casting rays y consultas de movimiento
--------------------------------------

Es muy a menudo necesario "explorar" el mundo al rededor desde nuestro
código. Emitir rays (rayos) es la forma mas común de hacerlo. La forma
mas simple de hacer esto es usando el nodo RayCast2D, el cual va a
emitir un rayo en cada frame y grabar la intersección.

Por el momento no hay una API de alto nivel para esto, así que el
servidor de física debe ser usado directamente. Para esto, la clase
:ref:`Physics2DDirectspaceState <class_Physics2DDirectspaceState>`
debe ser usada. Para obtenerla, los siguiente pasos deben ser tomados:

1. Debe ser usada dentro de la llamada de retorno ``_fixed_process()``,
   o en ``_integrate_forces()``

2. Los RIDs 2D para el servidor de espacio y física deben ser obtenidos.

El siguiente código debería funcionar:

::

    func _fixed_process(delta):
        var space = get_world_2d().get_space()
        var space_state = Physics2DServer.space_get_direct_state(space)

Disfruta haciendo consultas de espacio!
