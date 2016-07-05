.. _doc_kinematic_character_2d:

Kinematic Character (2D) (Personaje Cinemático)
===============================================

Introducción
~~~~~~~~~~~~

Si, el nombre suena extraño. "Kinematic  Character". Que es? La razón
es que cuando salieron los motores de física, se llamaban motores
"Dynamics"(Dinámicos) (porque se encargaban principalmente de las
respuestas de las colisiones). Muchos intentos tuvieron lugar para crear
un character controller usando los motores dinámicos pero no era tan
simple como parecía. Godot tiene una de las mejores implementaciones de
dynamic character controller que puedes encontrar (basta chequear
el demo 2d/platformer), pero usarlo requiere un nivel considerable de
habilidad y entendimiento de los motores de física (o un montón de
paciencia con prueba y error).

Algunos motores de física como Havok parecen jurar que los dynamic
character controllers son la mejor alternativa, mientras otros (PhysX)
prefiere promover los de tipo Kinematic.

Entonces, cual es realmente la diferencia?:

-  Un **dynamic character controller** usa un rigid body con tensor
   inercial infinito. Básicamente, es un rigid body que no puede rotar.
   Los motores de fisica siempre permiten que los objetos colisionen,
   luego resuelven las colisiones todas juntas. Esto hace que los dynamic
   character controllers sean capaces de interactuar con otros objetos
   físicos sin problemas (como se ve en el demo platformer), sin embargo
   estas interacciones no siempre son predecibles. Las colisiones también
   pueden tomar mas de un frame para ser resueltas, por lo que algunas
   colisiones puede parecer que se desplazan un poquito. Esos problemas
   pueden ser arreglados, pero requieren una cierta cantidad de
   habilidad.
-  Un **kinematic character controller** se asume que siempre empieza en
   un estado de no-colisionar, y siempre se moverá a un estado de
   no-colisionar. Si empieza en un estado de colisión, tratara de
   liberarse (como hacen los rigid bodies) pero esta es la excepción, no
   la regla. Esto vuelve su control y movimiento mucho mas predecible y
   fácil de programar. Sin embargo, como contrapartida, no pueden
   interactuar directamente con otros objetos de física (a no ser que se
   haga a mano en el codigo).

Este corto tutorial se enfocara en los kinematic character controllers.
Básicamente, la forma antigua de manejar colisiones (que no es
necesariamente mas simple bajo el capó, pero bien escondida y presentada
como una linda y simple API).

Fixed process
~~~~~~~~~~~~~

Para manejar la lógica de cuerpos o personajes kinematic, siempre es
recomendable usar fixed process, el cual es llamado la misma cantidad
de veces por segundo, siempre. Esto hace que los cálculos
de física y movimiento funcionen de una forma mas predecible que usando
el process regular, el cual puede tener picos o perder precisión si el
frame rate (cuadros por segundo) es demasiado alto o demasiado bajo.

::

    extends KinematicBody2D

    func _fixed_process(delta):
        pass

    func _ready():
        set_fixed_process(true)

Configuración de la escena
~~~~~~~~~~~~~~~~~~~~~~~~~~

Para tener algo que probar, aquí esta la escena (del tutorial de tilemap):
:download:`kbscene.zip </files/kbscene.zip>`. Vamos a estar creando una
nueva escena para el personaje. Usa los sprites del robot y crea una
escena como esta:

.. image:: /img/kbscene.png

Agreguemos una forma de colisión circular al collision body, crea una
nueva CircleShape2D en la propiedad shape de CollisionShape2D. Ajusta
el radio a 30:

.. image:: /img/kbradius.png

**Nota: Como se menciono antes en el tutorial de física, el motor de
física no puede manejar escala en la mayoría de las formas (solo los
polígonos de colisión, planos y segmentos parecen funcionar), así que
siempre cambia los parámetros (como el radio) en la forma en lugar de
escalarlo. Lo mismo es cierto para los cuerpos kinematic/rigid/static,
porque su escala afecta la escala de la forma.**

Ahora crea un script para el personaje, el que se uso como un ejemplo
arriba debería funcionar como base.

Finalmente, instancia esa escena de personaje en el tilemap, y haz
que la escena del mapa sea la principal, asi corre cuando tocamos play.

.. image:: /img/kbinstance.png

Moviendo el Kinematic character
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

Ve nuevamente a la escena del personaje, y abre el script, la magia
empieza ahora! Kinematic body no hará nada por defecto, pero tiene una
función realmente util llamada :ref:`KinematicBody2D.move() <class_KinematicBody2D_move>`.
Esta función toma un :ref:`Vector2 <class_Vector2>` como argumento,
e intenta aplicar ese movimiento al kinematic body. Si una colisión
sucede, se detiene justo en el momento de la colisión.

Entonces, vamos a mover nuestro sprite hacia abajo hasta que toque el piso:

::

    extends KinematicBody2D

    func _fixed_process(delta):
        move( Vector2(0,1) ) #mover abajo 1 pixel por frame de física

    func _ready():
        set_fixed_process(true)

El resultado es que el personaje se va a mover, pero se detendrá justo
cuando toque el piso. Copado, eh?

El siguiente paso será agregar gravedad a la mezcla, de esta forma se
comporta un poco mas como un personaje de juego:

::

    extends KinematicBody2D

    const GRAVITY = 200.0
    var velocity = Vector2()

    func _fixed_process(delta):

        velocity.y += delta * GRAVITY

        var motion = velocity * delta
        move( motion )

    func _ready():
        set_fixed_process(true)

Ahora el personaje cae suavemente. Hagamos que camine hacia los costados,
izquierda y derecha cuando se tocan las teclas direccionales. Recuerda que
los valores siendo usados (al menos para la velocidad) son pixels/segundo.

Esto agrega soporte simple para caminar cuando se presiona izquierda y
derecha:

::

    extends KinematicBody2D

    const GRAVITY = 200.0
    const WALK_SPEED = 200

    var velocity = Vector2()

    func _fixed_process(delta):

        velocity.y += delta * GRAVITY

        if (Input.is_action_pressed("ui_left")):
            velocity.x = -WALK_SPEED
        elif (Input.is_action_pressed("ui_right")):
            velocity.x =  WALK_SPEED
        else:
            velocity.x = 0

        var motion = velocity * delta
        move(motion)

    func _ready():
        set_fixed_process(true)

Y dale una prueba.

Problema?
~~~~~~~~

Y... no funciona muy bien. Si vas hacia la izquierda contra una pared,
se queda trancado a no ser que sueltes la flecha. Una vez en el piso,
también se queda trancado y no camina. Que sucede??

La respuesta es, lo que parece que debería ser simple, no es tan simple
en realidad. Si el movimiento no puede ser completado, el personaje
dejara de moverse. Es así de sencillo. Este diagrama debería ilustrar
mejor que esta sucediendo:

.. image:: /img/motion_diagram.png

Básicamente, el movimiento vectorial deseado nunca se completara porque
golpea el piso y la pared demasiado temprano en la trayectoria de
movimiento y eso hace que se detenga allí. Recuerda que aunque el
personaje esta en el piso, la gravedad siempre esta empujando el
vector movimiento hacia abajo.

Solución!
~~~~~~~~~

La solución? Esta situación se resuelve al "deslizar" por la normal de
la colisión. KinematicBody2D provee dos funciones útiles:

-  :ref:`KinematicBody2D.is_colliding() <class_KinematicBody2D_is_colliding>`
-  :ref:`KinematicBody2D.get_collision_normal() <class_KinematicBody2D_get_collision_normal>`

Así que lo que queremos hacer es esto:

.. image:: /img/motion_reflect.png

Cuando colisiona, la función ``move()`` retorna el "remanente" del
vector movimiento. Esto significa, que si el vector de movimiento es
40 pixels, pero la colisión sucedió a los 10 pixeles, el mismo vector
pero con 30 pixels de largo es retornado.

La forma correcta de resolver el movimiento es, entonces, deslizarse
por la normal de esta forma:

::

    func _fixed_process(delta):

        velocity.y += delta * GRAVITY
        if (Input.is_action_pressed("ui_left")):
            velocity.x = - WALK_SPEED
        elif (Input.is_action_pressed("ui_right")):
            velocity.x =   WALK_SPEED
        else:
            velocity.x = 0

        var motion = velocity * delta
        motion = move(motion)

        if (is_colliding()):
            var n = get_collision_normal()
            motion = n.slide(motion)
            velocity = n.slide(velocity)
            move(motion)


    func _ready():
        set_fixed_process(true)

Observa que no solo el movimiento ha sido modificado pero también la
velocidad. Esto tiene sentido ya que ayuda a mantener la nueva dirección
también.

La normal también puede ser usada para detectar que el personaje esta en
el piso, al chequear el ángulo. Si la normal apunta hacia arriba (o al
menos, con cierto margen), se puede determinar que el personaje esta allí.

Una demo mas completa puede encontrarse en el zip de demos distribuidos
con el motor, o en
https://github.com/godotengine/godot/tree/master/demos/2d/kinematic_char.
