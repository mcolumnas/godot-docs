.. _doc_matrices_and_transforms:

Matrices y transformaciones
===========================

Introducción
------------

Antes de leer este tutorial, es recomendable leer el anterior sobre
:ref:`doc_vector_math` ya que este es una continuación directa.

Este tutorial será sobre *transformaciones* y cubrirá algo sobre matrices
(pero no en profundidad).

Las transformaciones son aplicadas la mayor parte del tiempo como
traslación, rotación y escala por lo que serán consideradas como una
prioridad aquí.

Sistema orientado de coordenadas (OCS)
--------------------------------

Imagina que tenemos una nave en algún lugar del espacio. En Godot esto es
fácil, solo mueve la nave hacia algún lado y rótala:

.. image:: /img/tutomat1.png

Bien, entonces en 2D esto luce simple, una posición y un ángulo para una
rotación. Pero recuerda, somos adultos aquí y no usamos ángulos (además,
los ángulos ni siquiera son tan útiles cuando trabajamos en 3D).

Debemos darnos cuenta que en algún punto, alguien *diseño* esta nave.
Sea un dibujo 2D hecho con Paint.net, Gimp, Photoshop, etc. o en 3D a
través de herramientas DCC 3D como Blender, Max, Maya, etc.

Cuando fue diseñada, no estaba rotada. Fue diseñada en su propio
*sistema de coordenadas*.

.. image:: /img/tutomat2.png

Esto significa que el extremo de la nave tiene una coordenada, el
alerón tiene otra, etc. Sea en pixels (2D) o vértices (3D).

Así que, recordemos nuevamente que la nave esta en algún lugar del espacio:

.. image:: /img/tutomat3.png

Cómo llego allí? Que la movió y roto desde el lugar que fue diseñada a su
posición actual? La respuesta es... un **transform**, la nave fue
transformada desde su posición original a la nueva. Esto permite que la
nave sea mostrada donde está.

Pero transform es un término muy genérico para describir este proceso.
Para resolver este puzzle, vamos a superponer la posición del diseño
original a su posición actual:


.. image:: /img/tutomat4.png

Entonces, podemos ver que el "espacio de diseño" ha sido transformado
también. Como podemos representar mejor esta trasformación? Vamos a usar
3 vectores para esto (en 2D), un vector unitario apuntando hacia X
positivo, un vector unitario apuntando hacia Y positivo y una traslación.

.. image:: /img/tutomat5.png

Llamemos a estos 3 vectores "X", "Y" y "Origen", y vamos a superponerlos
sobre la nave para que tenga mas sentido:

.. image:: /img/tutomat6.png

Bien, esto es mas lindo, pero aún no tiene sentido. Que tienen que ver
X, Y y Origen con como la nave llego allí?

Bueno, tomemos el punto desde el extremo superior de la nave como
referencia:

.. image:: /img/tutomat7.png

Y le aplicamos la siguiente operación (y a todos los puntos en la nave
también, pero vamos a seguir el extremo superior como punto de
referencia):

::

    var new_pos = pos - origin

Haciendo esto al punto seleccionado lo movera de nuevo al centro:

.. image:: /img/tutomat8.png

Esto esa esperable, pero luego hagamos algo mas intereasnte. Usando
el producto escalar de X y el punto, y agregalo al producto escalar
de Y y el punto:

::

    var final_pos = x.dot(new_pos) + y.dot(new_pos)

Entonces lo que tenemos es.. espera un minuto, es la nave en su posición
de diseño!

.. image:: /img/tutomat9.png

Como sucedio esta magia negra? La nave estaba perdida en el espacio, y
ahora esta de nuevo en casa!

Puede parecer extrañio, pero tiene mucha logica. Recuerda, como vimos
en :ref:`doc_vector_math`, lo que sucedio es que la distancia al eje X,
y la distancia al eje Y fue computada. Calculanr distancia en una
dirección o plano era uno de los usos para el producto escalar. Esto fue
suficiente para obtener de vuelta las coordenadas de diseño para cada
punto en la nave.

Entonces, con lo que ha estado trabajando hasta ahora (con X, Y y Origen)
es un *Sistema ordenado de coordenadas\*. X e Y son la **Base**, y
\*Origen* es el offset (compensación).

Base
----

Sabemos lo que es el Origen. Es donde termino el 0,0 (origen) del sistema
de coordenadas luego de ser transformado a una nueva posición. Por esto
es llamado *Origen*, pero en la practica, es solo un offset hacia la
nueva posición.

La base es mas interesante. La base es la dirección de X e Y en el OCS
(sistema de coordenadas) de la nueva posición transformada. Nos dice que
ha cambiado, ya sea en 2D o 3D. El Origen (offset) y la Base (dirección)
comunican "Oye, el X e Y original de tu diseño estan *aqui*,
apuntando hacia *estas direcciones*."

Entonces, cambiemos la respresentación de la base. En lugar de 2 vectores,
usemos una *matriz*.

.. image:: /img/tutomat10.png

Los vectores estan allí en la matriz, horizontalmente. El siguiente
problema ahora es que.. es es esto de una matriz? Bueno, vamos a asumir
que nunca escuchaste de una matriz.

Transforms en Godot
-------------------

Este tutorial no explicara matemática de matrices (y sus operaciones)
en profundidad, solo su uso practico. Hay mucho material sobre eso,
el cual deberia ser mucho mas simple de entender luego de completar este
tutorial. Vamos a explicar solo como usar los transforms.

Matrix32
--------

:ref:`Matrix32 <class_Matrix32>` es una matriz 2x3. Tiene 3 elementos
Vector2 y es usada para 2D. El eje "X" es el elemento 0, el eje "Y" es
el elemento 1 y "Origen" es el elemento 2. No esta dividido en
base/origen por conveniencia, debido a su simplicidad.

::

    var m = Matrix32()
    var x = m[0] # 'X'
    var y = m[1] # 'Y'
    var o = m[2] # 'Origin'

La mayoria de las operaciones seran explicadas con este tipo de datos
(Matrix32), pero la misma lógica aplica a 3D.

Identidad
---------

Por defecto, Matrix32 es creada como una matriz de "identidad". Esto
significa:

-  'X' Apunta a la derecha: Vector2(1,0)
-  'Y' Apunta arriba (o abajo en pixels): Vector2(0,1)
-  'Origen' es el origen Vector2(0,0)

.. image:: /img/tutomat11.png

Es fácil adivinar que una matriz *identidad* es solo una matriz que
alinea el transform a su sistema de coordenadas padre. Es un *OCS*
que no ha sido trasladado, rotado o escalado. Todos los tipos de
transforms en Godot son creados con *identidad*.

Operaciones
-----------

Rotación
--------

Rotar Matrix32 es hecho usando la función "rotated":

::

    var m = Matrix32()
    m = m.rotated(PI/2) # rotar 90°

.. image:: /img/tutomat12.png

Traslación
----------

Hay dos formas de trasladar una Matrix32, la primera es solo mover
el origen:

::

    # Mover 2 unidades a la derecha
    var m = Matrix32()
    m = m.rotated(PI/2) # rotar 90°
    m[2]+=Vector2(2,0)

.. image:: /img/tutomat13.png

Esto siempre funcionara en coordenadas globales.

Si en su lugar, la traslación es deseada en coordenadas *locales* de
la matriz (hacia donde se orienta la *base*), esta el método
:ref:`Matrix32.translated() <class_Matrix32_translated>` :

::

    # Mover 2 unidades hacia donde esta orientada la base
    var m = Matrix32()
    m = m.rotated(PI/2) # rotar 90°
    m=m.translated( Vector2(2,0) )

.. image:: /img/tutomat14.png

Escala
------

Una matriz puede ser escalada también. Escalar multiplicara los vectores
base por un vector (vector X por componente x de la escala, vector Y por
el componente y de la escala). Dejara igual el origen:

::

    # Llevar al doble el tamaño de la base.
    var m = Matrix32()
    m = m.scaled( Vector2(2,2) )

.. image:: /img/tutomat15.png

Este tipo de operaciones en matrices es acumulativa. Significa que cada
una empieza relativa a la anterior. Para aquellos que han estado viviendo
en el planeta lo suficiente, una buena referencia de como funciona
transform es esta:

.. image:: /img/tutomat16.png

Una matriz es usada en forma similar a una tortuga. La tortuga muy
probablemente tenia una matriz en su interior (y estas descubriendo esto
muchos años *despues* de descubrir que Santa no es real).

Transform
---------

Transform es el acto de conmutar entre sistemas de coordenadas. Para
convertir una posición (sea 2D o 3D) desde el sistema de coordenadas
de "diseño" al OCS, el método "xform" es usado:

::

    var new_pos = m.xform(pos)

Y solo para la base (sin traslación):

::

    var new_pos = m.basis_xform(pos)

Ademas - multiplicar también es valido:

::

    var new_pos = m * pos

Transform inversa
-----------------

Para hacer la operación opuesta (lo que hicimos arriba con el cohete),
se usa el método "xform_inv":

::

    var new_pos = m.xform_inv(pos)

Solo para la base:

::

    var new_pos = m.basis_xform_inv(pos)

O pre-multiplicación:

::

    var new_pos = pos * m

Matrices ortonormales
---------------------

Sin embargo, si la Matrix ha sido escalada (los vectores no tienen
largo de unidad), o los vectores base no son ortogonales (90°), el
transform inverso no funcionara.

En otras palabras, el transform inverso solo es valido en matrices
*ortonormales*. Por ello, en estos casos se debe computar un inverso
afín.

El transform, o el transform inverso de una matriz de identidad
retornara la posición sin cambio:

::

    # No hace nada, pos no cambia
    pos = Matrix32().xform(pos)

Inverso afín
------------

El inverso afín es la matriz que hace la operacion inversa de otra
matriz, no importa si la matriz tiene escala o los ejes de vectores
no son ortogonales. El inverso afín es calculado con el método
affine_inverse():

::

    var mi = m.affine_inverse()
    var pos = m.xform(pos)
    pos = mi.xform(pos)
    # pos no cambia

Si la matriz es ortonormal, entonces:

::

    # si m es ortonormal, entonces
    pos = mi.xform(pos)
    # es lo mismo que
    pos = m.xform_inv(pos)

Multiplicacion de matrices
--------------------------

Las matrices pueden ser multiplicadas. La multiplicación de dos
matrices "encadena" (concatena) sus transforms.

Sin embargo, por convención, la multiplicación toma lugar en
orden reverso.

Ejemplo:

::

    var m = more_transforms * some_transforms

Para hacerlo un poco mas claro, esto:

::

    pos = transform1.xform(pos)
    pos = transform2.xform(pos)

Es lo mismo que:

::

    # nota el orden inverso
    pos = (transform2 * transform1).xform(pos)

Sin embargo, esto no es lo mismo:

::

    # devuelve resultados diferentes
    pos = (transform1 * transform2).xform(pos)

Porque en matemática de matrices, A + B no es lo mismo que B + A.

Multiplicación por inverso
--------------------------

Multiplicar una matriz por su inverso, resulta en identidad

::

    # No importa lo que A sea, B sera identidad
    B = A.affine_inverse() * A


Multiplicación por identidad
----------------------------

Multiplicar una matriz por identidad, resultara en una matriz sin cambios:
::

    # B sera igual que A
    B = A * Matrix32()

Consejos de Matrices
--------------------

Cuando usamos una jerarquía de transform, recuerda que la multiplicación
de matrices es reversa! Para obtener el transform global para una
jerarquía, haz:

::

    var global_xform = parent_matrix * child_matrix

Para 3 niveles:

::

    # debido al orden reverso, se necesitan parentesis
    var global_xform = gradparent_matrix + (parent_matrix + child_matrix)

Para hacer una matriz relativa al padre, usa el inverso afín (o el inverso
regular para matrices ortonormales).

::

    # transformar B desde una matriz global a una local a A
    var B_local_to_A = A.affine_inverse() * B

Revertirlo es justo como el ejemplo de arriba:

::

    # transformar de vuelta B local a B global
    var B = A * B_local_to_A

Bien, esto deberia ser suficiente! Completemos el tutorial moviendonos
a matrices 3D.

Matrices & transforms en 3D
---------------------------

Como mencionamos antes, para 3D, nos manejamos con 3 vectores
:ref:`Vector3 <class_Vector3>` para la matriz de rotación, y uno extra
para el origen.

Matrix3
-------

Godot tiene un tipo especial para una matriz 3x3, llamada
:ref:`Matrix3 <class_Matrix3>`. Puede ser usada para representar una
rotación y escala 3D. Los sub vectores pueden ser accedidos asi:

::

    var m = Matrix3()
    var x = m[0] # Vector3
    var y = m[1] # Vector3
    var z = m[2] # Vector3

O, alternativamente como:

::

    var m = Matrix3()
    var x = m.x # Vector3
    var y = m.y # Vector3
    var z = m.z # Vector3

Matrix3 tambien es inicializado a Identidad por defecto:

.. image:: /img/tutomat17.png

Rotación in 3D
--------------

Rotación en 3D es mas complejo que en 2D (traslación y escala son
iguales), porque rotación es una operación 2D implicita . Para rotar en
3D, un *eje*, debe ser seleccionado. La rotación, entonces, sucede
al rededor de dicho eje.

El eje para la rotación debe ser un *vector normal*. Es decir, un
vector que puede apuntar en cualquier dirección, pero cuyo largo debe
ser uno (1.0).

::

    #rotar en el eje Y
    var m3 = Matrix3()
    m3 = m3.rotated( Vector3(0,1,0), PI/2 )

Transform
---------

Para agregar el componente final a la mezcla, Godot provee el tipo
:ref:`Transform <class_Transform>` . Transform tiene dos miembros:

-  *basis* (base, de tipo :ref:`Matrix3 <class_Matrix3>`)
-  *origin* (origen, de tipo :ref:`Vector3 <class_Vector3>`)

Cualquier transformación 3D puede ser representada con Transform, y
la separación de base y origen hace mas sencillo trabajar con
traslación y rotación por separada.

Un ejemplo:

::

    var t = Transform()
    pos = t.xform(pos) # transformar posición 3D
    pos = t.basis.xform(pos) # (solo rotar)
    pos = t.origin + pos  (solo trasladar)
