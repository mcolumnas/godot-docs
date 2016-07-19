.. _doc_inverse_kinematics:

Inverse kinematics
==================

Este tutorial es una continuación de :ref:`doc_working_with_3d_skeletons`.

Antes de continuar, recomendaría leer algo de teoría, el artículo mas
simple que encontré es este:

http://freespace.virgin.net/hugo.elias/models/m_ik2.htm

Problema inicial
~~~~~~~~~~~~~~~~

Hablando en terminología Godot, la tarea que queremos resolver aquí es
posicionar los 2 ángulos que hablamos antes, entonces, el extremo
del hueso lowerarm esta tan cerca del target point, el cual está ajustado
por el target Vector3() como posible usando solo rotaciones. Esta tarea
es muy intensiva en calculo y nunca resuelta por una ecuación analítica.
Entonces, es un problema sin restricciones, lo cual significa que hay
un número ilimitado de soluciones a la ecuación.

.. image:: /img/inverse_kinematics.png

Para simplificar los cálculos, en este capítulo consideramos que target
también es hijo de Skeleton. Si este no es el caso para tu configuración,
puedes siempre re-apadrinarlo en tu script, ya que ahorraras en cálculos
si lo haces.

El eje de rotación es fácilmente calculado usando el producto cruzado
del vector de hueso y el vector target (objetivo). La rotación en este
caso va a ser siempre en dirección positiva. Si t es la Transform que
obtenemos desde la función get_bone_global_pose(), el vector hueso es

::

    t.basis[2]

Así que tenemos toda la información aquí para ejecutar nuestro algoritmo.

En el desarrollo de juegos es común resolver este problema acercándose de
forma iterativa a la ubicación deseada, sumando/restando pequeños ángulos
hasta que el cambio de distancia logrado es menos que algún pequeño valor
de error. Suena bastante fácil, pero hay problemas en Godot que debemos
resolver para lograr nuestro objetivo.

-  **Como encontrar las coordenadas del extremo del hueso?**
-  **Como encontrar el vector desde la base del hueso hasta el objetivo?**

Para nuestro propósito (extremo del hueso movido dentro del área del
objetivo), necesitamos saber dónde está el extremo de nuestro hueso IK.
Ya que no usamos un leaf bone como IK bone, sabemos que las coordenadas
de la base del hueso es el extremo del hueso padre. Todos estos cálculos
son bastante dependientes en la estructura del esqueleto. Pueden usar
constantes pre-calculadas también. Puedes agregar un hueso extra para el
extremo de IK y calcularlo usando eso.

Implementación
~~~~~~~~~~~~~~

Vamos a usar la variable exportada para el largo del hueso para
simplificarlo.

::

    export var IK_bone="lowerarm"
    export var IK_bone_length=1.0
    export var IK_error = 0.1

Ahora, necesitamos aplicar nuestras transformaciones desde el hueso IK
a la base de la cadena. Así que aplicamos rotación al hueso IK y luego
mover desde nuestro hueso IK hasta su padre, y aplicar rotación
nuevamente, luego mover hasta el padre del hueso actual nuevamente, etc.
Así que necesitamos limitar nuestra cadena un poco.

::

    export var IK_limit = 2

For ``_ready()`` function:

::

    var skel
    func _ready():
        skel = get_node("arm/Armature/Skeleton")
        set_process(true)

Ahora podemos escribir nuestra función de pasaje de cadena:

::

    func pass_chain():
        var b = skel.find_bone(IK_bone)
        var l = IK_limit
        while b >= 0 and l > 0:
            print( "name":", skel.get_bone_name(b))
            print( "local transform":", skel.get_bone_pose(b))
            print( "global transform":", skel.get_bone_global_pose(b))
            b = skel.get_bone_parent(b)
            l = l - 1

Y para la función ``_process()``:

::

    func _process(dt):
        pass_chain(dt)

Ejecutando este script solo pasara a través de la cadena de huesos
imprimiendo las transformaciones de huesos.

::

    extends Spatial

    export var IK_bone="lowerarm"
    export var IK_bone_length=1.0
    export var IK_error = 0.1
    export var IK_limit = 2
    var skel
    func _ready():
        skel = get_node("arm/Armature/Skeleton")
        set_process(true)
    func pass_chain(dt):
        var b = skel.find_bone(IK_bone)
        var l = IK_limit
        while b >= 0 and l > 0:
            print("name: ", skel.get_bone_name(b))
            print("local transform: ", skel.get_bone_pose(b))
            print( "global transform:", skel.get_bone_global_pose(b))
            b = skel.get_bone_parent(b)
            l = l - 1
    func _process(dt):
        pass_chain(dt)

Ahora necesitamos trabajar con el objetivo. El objetivo debe estar
ubicado en algún lugar accesible. Ya que "arm" es una escena importada,
lo mejor es ubicar el nodo target dentro de nuestro nivel superior de
escena. Pero para que podamos trabajar fácilmente con el objetivo su
Transform debe estar en el mismo nivel que el Skeleton.

Para hacer frente a este problema creamos un nodo "target" bajo nuestra
raíz de nodos de escena y cuando corra el script vamos a re apadrinarlo
copiando la transformación global, lo cual logra el efecto deseado.

Crea un nuevo nodo Spatial bajo la raíz y renómbralo a "target".
Luego modifica la función ``_ready()`` para que luzca así:

::

    var skel
    var target
    func _ready():
        skel = get_node("arm/Armature/Skeleton")
        target = get_node("target")
        var ttrans = target.get_global_transform()
        remove_child(target)
        skel.add_child(target)
        target.set_global_transform(ttrans)
        set_process(true)
