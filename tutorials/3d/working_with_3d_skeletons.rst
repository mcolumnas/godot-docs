.. _doc_working_with_3d_skeletons:

Trabajando con esqueletos 3D
============================

El soporte de esqueletos 3D actualmente es bastante rudimentario. El nodo
y clase :ref:`class_Skeleton` fueron diseñados principalmente para
soportar la importación de animaciones por esqueleto como un conjunto de
matrices de transformación.

Nodo Skeleton
-------------

Un nodo Skeleton puede ser directamente agregado en el lugar que quieras
en la escena. Usualmente Mesh es hijo de Skeleton, ya que es más fácil de
manipular de esta forma, porque las transformaciones en esqueletos son
relativas a donde se encuentra. Pero puedes especificar nodo Skeleton en
toda MeshInshatnce.

Siendo obvio, Skeleton está pensado para deformar mallas (meshes), y
consiste de estructuras llamadas "bones"(huesos). Cada "bone" se
representa como un Transform(transformación), la cual es aplicada a un
grupo de vértices dentro de la malla. Puedes controlar directamente un
grupo de vértices desde Godot. Para ello por favor ve la referencia en
:ref:`class_MeshDataTool` class,
method :ref:`set_vertex_bones <class_MeshDataTool_set_vertex_bones>`.
Esta clase es muy poderosa.

Los "bones" son organizados en jerarquía, cada hueso, excepto por el/los
hueso/huesos root(raíz) tienen padres. Cada hueso tiene un nombre asociado
que podes usar para referirlo (ej: "raiz" o "mano.I", etc.). Además los
huesos están numerados, esos números son IDs de huesos. La referencia de
los huesos padre es por sus números ID.

Para el resto del articulo consideramos la siguiente escena:

::

    main (Spatial) - el script siempre está aquí
    == skel (Skeleton)
    ==== mesh (MeshInstance)

Esta escena esta importada de Blender. Contiene la malla arm(brazo) con
2 huesos - upperarm y lowerarm, y upperarm es el padre de lowerarm.

Clase Skeleton
--------------

Puedes ver la ayuda interna de Godot para descripciones de todas las
funciones. Básicamente todas las operaciones en huesos son hechos usando
su ID numérica. Puedes convertir de nombre a numero ID y vice-versa.

**Para encontrar el número de huesos en un esqueleto usamos la función
get_bone_count()**

::

    extends Spatial
    var skel

    func _ready():
        skel = get_node("skel")
        var id = skel.find_bone("upperarm")
        print("bone id:", id)
        var parent = skel.get_bone_parent(id)
        print("bone parent id:", id)

**para encontrar el ID para el hueso, usa la función find_bone()**

::

    extends Spatial
    var skel

    func _ready():
        skel = get_node("skel")
        var id = skel.find_bone("upperarm")
        print("bone id:", id)

Ahora, queremos hacer algo interesante con ID excepto imprimirlo.
Además, podemos necesitar información adicional - para encontrar huesos
padres para completar la cadena, etc. Esto se hace completamente con las
funciones get/set_bone\_\*.

**Para encontrar padres de hueso usamos la función get_bone_parent(id)**

::

    extends Spatial
    var skel

    func _ready():
        skel = get_node("skel")
        var id = skel.find_bone("upperarm")
        print("bone id:", id)
        var parent = skel.get_bone_parent(id)
        print("bone parent id:", id)

Las transformaciones de huesos es el motivo por el cual estamos acá. Hay 3
tipos de transformaciones - local, global, custom (personalizada).

**Para encontrar el Transform local usamos la función get_bone_pose(id)**

::

    extends Spatial
    var skel

    func _ready():
        skel = get_node("skel")
        var id = skel.find_bone("upperarm")
        print("bone id:", id)
        var parent = skel.get_bone_parent(id)
        print("bone parent id:", id)
        var t = skel.get_bone_pose(id)
        print("bone transform: ", t)

Así que vemos una matriz 3x4 allí, la primer columna de 1s. Que podemos
hacer sobre eso? Es un Transform, así que podemos hacer todo lo que se
hace con Transform, básicamente traslación, rotación y escala. También
podemos multiplicar transformaciones para obtener transformaciones
complejas. Recuerda, los "bones" en Godot son solo Transforms sobre un
grupo de vétrices. También podemos copiar Transforms de otros objetos
aquí. Así que vamos a rotar nuestro hueso "upperarm":

::

    extends Spatial
    var skel
    var id

    func _ready():
        skel = get_node("skel")
        id = skel.find_bone("upperarm")
        print("bone id:", id)
        var parent = skel.get_bone_parent(id)
        print("bone parent id:", id)
        var t = skel.get_bone_pose(id)
        print("bone transform: ", t)
        set_process(true)

    func _process(dt):
        var t = skel.get_bone_pose(id)
        t = t.rotated(Vector3(0.0, 1.0, 0.0), 0.1 * dt)
        skel.set_bone_pose(id, t)

Ahora podemos rotar huesos individuales. Lo mismo sucede para escala y
traslación - inténtalos por tu cuenta y ve los resultados.

Lo que usamos ahora fue local pose. Por defecto todos los huesos no
están modificados. Pero este Transform no nos dice nada sobre la relación
entre huesos. Esta información es necesaria para un buen número de cosas.
Como lo podemos obtener? Aquí viene global transform:

**Para encontrar el Transform global del hueso usamos la función
get_bone_global_pose(id)**

Vamos a encontrar el Transform global para el hueso lowerarm:

::

    extends Spatial
    var skel

    func _ready():
        skel = get_node("skel")
        var id = skel.find_bone("lowerarm")
        print("bone id:", id)
        var parent = skel.get_bone_parent(id)
        print("bone parent id:", id)
        var t = skel.get_bone_global_pose(id)
        print("bone transform: ", t)

Como puedes ver, este transform no está en ceros. Aunque se llama global,
en realidad es relativo al origén del Skeleton. Para el hueso raíz, el
origén siempre esta en 0 si no fue modificado. Vamos a imprimir el origén
de nuestro hueso lowerarm:

::

    extends Spatial
    var skel

    func _ready():
        skel = get_node("skel")
        var id = skel.find_bone("lowerarm")
        print("bone id:", id)
        var parent = skel.get_bone_parent(id)
        print("bone parent id:", id)
        var t = skel.get_bone_global_pose(id)
        print("bone origin: ", t.origin)

Vas a ver un número. Que significa este número? Es un punto de rotación
de Transform. Así que es la parte base del hueso. En Blender, puedes ir
a modo Pose e intentar allí rotar los huesos - van a rotar al rededor
de su origen. Pero que hay sobre el extremo del hueso? No podemos saber
cosas como el largo del hueso, el cual es necesario para muchas cosas,
sin saber la ubicación del extremo. Para todos los huesos en cadena
excepto el ultimo podemos calcular la ubicación del extremo - es
simplemente el origen de un hueso hijo. Sí, hay situaciones donde esto
no es cierto, para huesos no conectados. Pero eso está OK por ahora,
ya que no es importante respecto a los Transforms. Pero el extremo de
leaf bone no se puede encontrar. Leaf bone es un hueso sin hijo. Por lo
que no tienes ninguna información sobre su extremo. Pero esto no es tan
grave. Puedes superarlo ya sea agregando un hueso extra a la cadena o
simplemente calculando el largo del leaf bone en Blender y guardando
su valor en tu script.

Usando "bones" 3D para control de malla
---------------------------------------

Ahora que ya sabes lo básico podemos aplicar esto para hacer FK-control
completo de nuestro brazo (FK es forward-kinematics)

Para controlar completamente nuestro brazo necesitamos los siguientes
parámetros:

-  Upperarm angle x, y, z
-  Lowerarm angle x, y, z

Todos estos parámetros pueden ser ajustados, incrementados y
reducidos.

Crea el siguiente árbol de nodos:

::

    main (Spatial) <- el script esta aquí
    +-arm (arm scene)
    + DirectionLight (DirectionLight)
    + Camera

Ajusta una Camera de tal forma que el brazo este adecuadamente visible.
Rota DirectionLight así ese brazo es apropiadamente iluminado cuando
esta en modo de reproducción de escena.

Ahora necesitamos crear un nuevo script debajo de main:

Primero ajustamos los parámetros:

::

    var lowerarm_angle = Vector3()
    var upperarm_angle = Vector3()

Ahora necesitamos configurar una forma de cambiarlos. Usemos las teclas
para eso.

Por favor crea 7 acciones en la configuración de proyecto:

-  **selext_x** - bind to X key
-  **selext_y** - bind to Y key
-  **selext_z** - bind to Z key
-  **select_upperarm** - bind to key 1
-  **select_lowerarm** - bind to key 2
-  **increment** - bind to key numpad +
-  **decrement** - bind to key numpad -

Así que ahora queremos ajustar los parámetros de arriba. Entonces vamos a
crear código para que lo haga:

::

    func _ready():
        set_process(true)
    var bone = "upperarm"
    var coordinate = 0
    func _process(dt):
        if Input.is_action_pressed("select_x"):
            coordinate = 0
        elif Input.is_action_pressed("select_y"):
            coordinate = 1
        elif Input.is_action_pressed("select_z"):
            coordinate = 2
        elif Input.is_action_pressed("select_upperarm"):
            bone = "upperarm"
        elif Input.is_action_pressed("select_lowerarm"):
            bone = "lowerarm"
        elif Input.is_action_pressed("increment"):
            if bone == "lowerarm":
                lowerarm_angle[coordinate] += 1
            elif bone == "upperarm":
                upperarm_angle[coordinate] += 1

El código completo para control de brazo es este:

::

    extends Spatial

    # member variables here, example:
    # var a=2
    # var b="textvar"
    var upperarm_angle = Vector3()
    var lowerarm_angle = Vector3()
    var skel

    func _ready():
        skel = get_node("arm/Armature/Skeleton")
        set_process(true)
    var bone = "upperarm"
    var coordinate = 0
    func set_bone_rot(bone, ang):
        var b = skel.find_bone(bone)
        var rest = skel.get_bone_rest(b)
        var newpose = rest.rotated(Vector3(1.0, 0.0, 0.0), ang.x)
        var newpose = newpose.rotated(Vector3(0.0, 1.0, 0.0), ang.y)
        var newpose = newpose.rotated(Vector3(0.0, 0.0, 1.0), ang.z)
        skel.set_bone_pose(b, newpose)

    func _process(dt):
        if Input.is_action_pressed("select_x"):
            coordinate = 0
        elif Input.is_action_pressed("select_y"):
            coordinate = 1
        elif Input.is_action_pressed("select_z"):
            coordinate = 2
        elif Input.is_action_pressed("select_upperarm"):
            bone = "upperarm"
        elif Input.is_action_pressed("select_lowerarm"):
            bone = "lowerarm"
        elif Input.is_action_pressed("increment"):
            if bone == "lowerarm":
                lowerarm_angle[coordinate] += 1
            elif bone == "upperarm":
                upperarm_angle[coordinate] += 1
        elif Input.is_action_pressed("decrement"):
            if bone == "lowerarm":
                lowerarm_angle[coordinate] -= 1
            elif bone == "upperarm":
                upperarm_angle[coordinate] -= 1
        set_bone_rot("lowerarm", lowerarm_angle)
        set_bone_rot("upperarm", upperarm_angle)

Presionando las teclas 1/2 seleccionas upperarm/lowerarm, selecciona el
eje al presionar x, y, z, rota usando "+"/"-" en el teclado numérico.

De esta forma tu puedes controlar por completo el brazo en modo FK usando
2 huesos. Puedes agregar huesos adicionales y/o mejorar el "feel" de la
interface usando coeficientes para el cambio. Recomiendo que juegues con
este ejemplo un montón antes de ir a la siguiente parte.

Puedes clonar el código del demo para este capítulo usando

::

    git clone git@github.com:slapin/godot-skel3d.git
    cd demo1

O puedes navegarlo usando la interfaz web:

https://github.com/slapin/godot-skel3d

Usando "bones" 3D para implementar Inverse Kinematics
-----------------------------------------------------

Ve :ref:`doc_inverse_kinematics`.

Usando "bones" 3D para implementar física ragdoll
-------------------------------------------------

Para hacer.
