.. _doc_vector_math:

Matemática vectorial
====================

Introducción
~~~~~~~~~~~~

Este pequeño tutorial tiene como objetivo ser una introducción corta y
práctica a la matemática de vectores, útil para juegos 3D pero también
3D. De nuevo, la matemática de vectores es útil para juegos 3D pero
*también* 3D. Es una herramienta asombrosa cuando la entiendes y hace a
la programación de comportamientos complejos mucho mas simple.

A menudo sucede que programadores jovenes confian demasiado en la
matemática *incorrecta* para resolver un amplio abanico de problemas,
por ejemplo usando solo trigonometria en lugar de matemática de vectores
para jugos 2D.

Este tutorial se encofara en el uso practico, con aplicación inmediata al
arte de la programación de juegos.

Sistema de coordenadas (2D)
~~~~~~~~~~~~~~~~~~~~~~~~~~~

Tipicamente, definimos coordenadas como un par (x,y), x representa el
valor horizontal e y el vertical. Esto tiene sentido dado que la pantalla
es solo un rectangulo en dos dimensiones. Como ejemplo, aquí hay una
posición en espacio 2D:

.. image:: /img/tutovec1.png

Una posición puede estar en cualquier lugar del espacio. La posicion (0,0)
tiene un nombre, es llamada el **origen**. Recuerda este termino bien
porque tiene mas usos implicitos luego. El (0,0) de un sistema de
n-coordenadas es el **origen**.

En matemática vectorial, las coordenadas tienen dos usos diferentes, ambos
igualmente importantes. Son usadas para representar una *posición* pero
tambien un *vector*. La misma posición que antes, cuando se imagina como
un vector, tiene diferente significado.

.. image:: /img/tutovec2.png

Cuando se imagina como vector, dos propiedades pueden inferirse, la
**dirección** y la **magnitud**. Cada posición en el espacio puede ser
un vector, con la excepcion del **origen**. Esto es porque las coordenadas
(0,0) no pueden representar dirección (magnitud 0).

.. image:: /img/tutovec2b.png

Dirección
---------

Dirección es simplemente hacia donde el vector apunta. Imagina una flecha
que comienza en el **origen** y va hacia la [STRIKEOUT:posición]. La
punta de la flecha esta en la posición, por lo que siempre apunta hacia
afuera, alejandose del origen. Imaginarse vectores como flechas ayuda
mucho.

.. image:: /img/tutovec3b.png

Magnitud
--------

Finalmente, el largo del vector es la distancia desde el origen hasta la
posición. Obtener el largo del vector es fácil, solo usa el te teorema
Pitagorico <http://en.wikipedia.org/wiki/Pythagorean_theorem>`__.

::

    var len = sqrt( x*x + y*y )

Pero... ángulos?
----------------

Pero porque no usar un *ángulo*? Despues de todo, podriamos pensar de
un vector como un ángulo y una magnitud, en lugar de una dirección y
una magnitud. Los ángulos también son un concepto mas familiar.

Para decir la verdad, los ángulos no son tan útiles en matemática de
vectores, y la mayoría del tiempo no se los trata directamente. Tal vez
funcionen en 2D, pero en 3D un montón de lo que usualmente puede ser
hecho con ángulos no funciona.

Igual, usar ángulos no es una excusa, aun en 2D. La mayoría de lo que
toma un monton de trabajo con ángulos en 2D, es de todas formas mas
natural y facil de lograr con matemática de vectores. En matemática
vectorial, los ángulos son útiles solo como medida, pero tienen poca
participacion en la matemática. Entonces, deja de lado la trigonometria
de una vez, y preparate para adoptar vectores!

En cualquier caso, obtener el angulo desde el vector es fácil y puede
ser logrado con trig... er, que fue eso? Digo, la función
:ref:`atan2() <class_@GDScript_atan2>`.

Vectores es Godot
~~~~~~~~~~~~~~~~

Para hacer los ejemplos mas fáciles, vale la pena explicar como los
vectores son implementados en GDScript. GDscript tiene ambos
:ref:`Vector2 <class_Vector2>` y :ref:`Vector3 <class_Vector3>`,
para matemática 2D y 3D respectivamente. Godot usa clases de vectores
tanto para posición como dirección. Tambien contienen variables miembro
x e y (para 2D) y x, y, z (para 3D).


::

    # crea un vector con coordenadas (2,5)
    var a = Vector2(2,5)
    # crea un vector y asigna x e y manualmente
    var b = Vector2()
    b.x = 7
    b.y = 8

Cuando se opera con vectores, no es necesario operar directamente en
los miembros (en realidad esto es mucho mas lento). Los vectores
soportan operaciones aritmeticas regulares:

::

    # add a and b
    var c = a + b
    # resultara en un vector c, con valores (9,13)

Es lo mismo que hacer:

::

    var c = Vector2()
    c.x = a.x + b.x
    c.y = a.y + b.y

Excepto que lo primero es mucho mas eficiente y legible.

Las operaciones aritemticas regulares como adición, sustracción,
multiplicación y division son soportadas.

La multiplicación y división tambien puede ser mezclada con numeros
de digito unico, también llamados **escalares**.

::

    # multiplicación de vector por escalar
    var c = a*2.0
    # resultara en el vector c, con valor (4,10)

Es lo mismo que hacer

::

    var c = Vector2()
    c.x = a.x*2.0
    c.y = a.y*2.0

Excepto que, nuevamente, lo primero es mucho mas eficiente y legible.

Vectores perpendiculares
~~~~~~~~~~~~~~~~~~~~~~~~

Rotar un vector 2D 90º para algun lado, izquierda o derecha, es realmente
fácil, solo intercambia x e y, luego niega x o y (la dirección de la
rotación depende en cual es negado).

.. image:: /img/tutovec15.png

Ejemplo:

::

    var v = Vector2(0,1)
    # rotar a la derecha (como las agujas del reloj)
    var v_right = Vector2(-v.y, v.x)
    # rotar a la izquierda (al reves de las agujas del reloj)
    var v_right = Vector2(v.y, -v.x)

Este es un truco práctico que se usa a menudo. Es imposible de hacer con
vectores 3D, porque hay un número infinito de vectores perpendiculares.

Vectores unitarios
~~~~~~~~~~~~~~~~~~

OK, entonces sabemos lo que es un vector. Tiene una **dirección** y una
**magnitud**. Tambien sabemos como usarlos en Godot. El siguiente paso
es aprender sobre **vectores unitarios**. Cualquier vector con una
magnitud de largo 1 es considerado un **vector unitario**. En 2D,
imagina dibujar un circulo de radio uno. Ese círculo contiene todos
los vectores unitarios en existencia para dos dimensiones.

.. image:: /img/tutovec3.png

Entonces, que es tan especial sobre los vectores unitarios? Los vectores
unitarios son asombrosos. En otras palabras, los vectores unitarios
tienen **varias propiedades muy útiles**.

No puedes esperar a saber mas sobre las fantasticas propiedades de los
vectores unitarios, pero un paso a la vez. Asi que, como se crea un
vector unitario a partir de un vector regular?

Normalización
-------------

Tomar cualquier vector y reducir su **magnitud** a 1 mientras mantienes
su **dirección** se llama **normalización**. La normalización es hecha
al dividir los componentes x e y (y z en 3D) de un vector por su
magnitud:

::

    var a = Vector2(2,4)
    var m = sqrt(a.x*a.x + a.y*a.y)
    a.x /= m
    a.y /= m

Como habras adivinado, si el vector tiene magnitud 0 (lo que significa
que no es un vector pero el **origen** tambien llamado *vector nulo*),
ocurre una division por 0 y el universo atraviesa un segundo big bang,
excepto que en polaridad reversa y luego de vuelta. Como resultado,
la humanidad esta a salvo pero Godot imprimira un error. Recuerda!
El vector (0,0) no puede ser normalizado!.

Por supuesto, Vector2 y Vector3 ya tienen un método para esto:

::

    a = a.normalized()

Producto escalar
~~~~~~~~~~~~~~~~

OK, el **producto escalar** es la parte mas importante de matemática
de vectores. Sin el producto escalar, Quake nunca hubiera sido hecho.
Esta es la sección mas importante del tutorial, así que asegurate que
lo entiendes. La mayoria de la gente que intenta aprender matemática
de vectores abandona aca, a pesar de lo simple que es, no le encunetran
pie ni cabeza. Porque? Aquí esta el porque, es porque...

El producto escalar toma dos vectores y regresa un **escalar**:

::

    var s = a.x*b.x + a.y*b.y

Si, básicamente eso. Multiplica **x** del vector **a** por **x** del
vector **b**. Haz lo mismo con y, luego sumalo. En 3D es muy parecido:

::

    var s = a.x*b.x + a.y*b.y + a.z*b.z

Lo se, no tienen ningún sentido! Hasta puedes hacerlo con una función
incorporada:

::

    var s = a.dot(b)

El órden de los dos vectores *no* importa, ``a.dot(b)`` retorna el
mismo valor que ``b.dot(b)``.

Aquí es donde empieza la desesperación y los libros y tutoriales te
muestran esta formula:

.. image:: /img/tutovec4.png

Y te das cuenta que es tiempo de abandonar el desarrollo de juegos 3D
o 2D complejos. Como puede ser que algo tan simple sea tan complejo?
Alguien mas tendra que hacer el proximo Zelda o Call of Duty. Los
RPGs vistos de arriba no lucen tan mal despues de todo. Sip, escuche
que alguien lo ha hecho bastante bien con uno de esos en steam...

Así que este es tu momento, tu tiempo de brillar. **NO TE RINDAS**!
En este punto, el tutorial tomara un giro rapido y se enfocara en
lo que hace útil al producto escalar. Esto es, **porque** es util.
Nos enfocaremos uno por uno en los casos de uso del producto escalar,
con aplicacion en la vida real. No mas forumlas que no hacen ningun
sentido. Las formulas tendran un montón de sentido *una vez que aprendes*
para que son útiles.

Lado
------

La primer útilidad y mas imporante propiedad del producto escalar es
para chequear para que lado miran las cosas. Imaginemos que tenemos
dos vectores cualquiera, **a** y **b**. Cualquier **dirección** o
**magnitud** (menos el **origen**). No importa lo que sean, solo
imaginemos que computamos el producto escalar entre ellos.

::

    var s = a.dot(b)

La operación retornara un único numero de punto flotante (pero como
estamos en el mundo de vectores, lo llamamos **scalar**, seguiremos
usando ese termino de ahora en mas). El número nos dira lo siguiente:

-  Si el número es mas grande que zero, ambos estan mirando hacia la
   misma dirección (el ángulo entre ellos es < 90° grados).
-  Si el numero es menor que zero, ambos estan mirando en direcciones
   opuestas (el ángulo entre ellos es > 90° grados).
-  Si el número es cero, los vectores tienen forma de L (el ángulo
   entre ellos *es* 90° grados).

.. image:: /img/tutovec5.png

Así que pensemos de un escenario de caso de uso real. Imaginemos que
una serpiente esta yendo por un bosque, y esta el enemigo cerca. Como
podemos decir rapidamente si el enemigo descubrio la serpiente? Para
poder descubrirla, el enemigo debe poder *ver* la serpiente. Digamos,
entonces que:

-  La serpiente esta en la posicion **A**.
-  El enemigo esta en la posicion **B**.
-  El enemigo esta *mirando* por el vector de dirección **F**.

.. image:: /img/tutovec6.png

Así que, vamos a crear un nuevo vector **BA** que va desde el guardia
(**B**) hasta la serpiente (**A**), al restarlos:

::

    var BA = A - B

.. image:: /img/tutovec7.png

Idealmente, si el guardia esta mirando hacia la serpiente, para hacer
contacto de ojo, necesitaria hacerlo en la misma dirección que el vector
BA.

Si el producto escalar entre **F** y **BA** es mayor que 0, entonces la
serpiente sera descubierta. Esto sucede porque podremos saber si el
guardia esta mirando hacia ese lado:

::

    if (BA.dot(F) > 0):
        print("!")

Parece que la serpiente esta a salvo por ahora.

Lado con vectores unitarios
~~~~~~~~~~~~~~~~~~~~~~~~~~~

Bien, entonces ahora sabemos que el producto escalar entre dos vectores
nos dejara saber si miran hacia el mismo lado, lados contrarios o son
perpendiculares entre si.

Esto funciona igual con todos los vectores, no importa la magnitud por
lo que los **vectores unitarios** no son la excepción. Sin embargo,
usando las mismas propiedades con los vectores unitarios brinda un
resultado aun mas interesante, ya que se agrega una propiedad extra:

-  Si ambos vectores estan mirando exactamente hacia la misma dirección
   (paralelos, ángulo entre ellos de 0°), el escalar resultante es **1**.
-  Si ambos vectores estan mirando en dirección exactamente opuesta
   (paralelos, ángulo entre ellos es 180°), el escalar resultante es
   **-1**.

Esto significa que el producto escalar entre dos vectores unitarios
siempre esta en el rango de 1 a -1. Asique de nuevo...

-  Si su angulo es **0°** el producto escalar es **1**.
-  Si su angulo es **90°** el producto escalar es **0**.
-  Si su angulo es **180°** el producto escalar es **-1**.

Mm... esto es extrañamente familiar... lo he visto antes... donde?

Tomemos dos vectores unitarios. El primero apunta hacia arriba, el
segundo tambien pero lo rotaremos desde arriba (0°) hasta abajo
(180°)...

.. image:: /img/tutovec8.png

Mientras trazamos el escalar resultante!

.. image:: /img/tutovec9.png

Ahhh! Tiene sentido ahora, esto es la función
`Coseno <http://mathworld.wolfram.com/Cosine.html>`__

Podemos decir que, entonces, como regla...

El **producto escalar** entre dos **vectores unitarios** es el **coseno**
del **ángulo** entre esos dos vectores. Entonces, para obtener el ángulo
entre los dos vectores, debemos hacer:

::

    var angulo_en_radianes = acos( a.dot(b) )

Para que sirve esto? Bueno obtener el ángulo directamente no es tan
útil, pero solo poder saber el ángulo es útil como referencia. Un ejemplo
es el demo `Kinematic
Character <https://github.com/godotengine/godot/blob/master/demos/2d/kinematic_char/player.gd#L879>`__
cuando el personaje se mueve en una cierta dirección y luego golpeamos
un objeto. Como saber si lo que se golpeo es el suelo?

Comparando la normal del punto de colisión con un ángulo previamente
computado.

La belleza de esto es que el mismo código funciona exactamente igual y
sin modificación en
`3D <https://github.com/godotengine/godot/blob/master/demos/3d/kinematic_char/cubio.gd#L57>`__.
La matemática vectorial es, en gran medida,
independiente-de-la-cantidad-de-dimensiones, por lo que agregar o quitar
ejes solo agrega muy poca complejidad.

Planos
~~~~~~

El producto escalar tiene otra propiedad interesante con los vectores
unitarios. Imagina que perpendicular al vector (y a traves del origen)
pasa un plano. Los planos dividen el espacio entreo entre positivo
(sobre el plano) y negativo (bajo el plano), y (contrario a la creencia
popular) tambien puedes usar su matemática en 2D:

.. image:: /img/tutovec10.png

Los vectores unitarios que son perpendiculares a la superficie (por lo
que, ellos describen la orientacion de la superficie) son llamados
**vectores unitarios normales**. Aunque, usualmente se abrebian solo
como \*normals. Las normales aparecen en planos, geometría 3D (para
determinar hacia donde mira cada cara o vector), etc. Una **normal**
*es* un **vector unitario**, pero se llama *normal* por su uso.
(De la misma forma que llamamos Origen a (0.0)!).

Es tan simple como luce. El plano pasa por el origen y la superficie
de el es perpendicular al vector unitario (o *normal*). El lado hacia
el cual apunta el vector es el medio-espacio positivo, mientras que
el otro lado es el medio-espacio negativo. En 3D esto es exactamente
lo mismo, excepto que el plano es una superficie infinita (imagina
una pila de papel plana e infinita que puedes orientar y esta sujeta
al origen) en lugar de una línea.

Distance to plane
-----------------

Now that it's clear what a plane is, let's go back to the dot product.
The dot product between a **unit vector** and any **point in space**
(yes, this time we do dot product between vector and position), returns
the **distance from the point to the plane**:

::

    var distance = normal.dot(point)

But not just the absolute distance, if the point is in the negative half
space the distance will be negative, too:

.. image:: /img/tutovec11.png

This allows us to tell which side of the plane a point is.

Away from the origin
--------------------

I know what you are thinking! So far this is nice, but *real* planes are
everywhere in space, not only passing through the origin. You want real
*plane* action and you want it *now*.

Remember that planes not only split space in two, but they also have
*polarity*. This means that it is possible to have perfectly overlapping
planes, but their negative and positive half-spaces are swapped.

With this in mind, let's describe a full plane as a **normal** *N* and a
**distance from the origin** scalar *D*. Thus, our plane is represented
by N and D. For example:

.. image:: /img/tutovec12.png

For 3D math, Godot provides a :ref:`Plane <class_Plane>`
built-in type that handles this.

Basically, N and D can represent any plane in space, be it for 2D or 3D
(depending on the amount of dimensions of N) and the math is the same
for both. It's the same as before, but D is the distance from the origin
to the plane, travelling in N direction. As an example, imagine you want
to reach a point in the plane, you will just do:

::

    var point_in_plane = N*D

This will stretch (resize) the normal vector and make it touch the
plane. This math might seem confusing, but it's actually much simpler
than it seems. If we want to tell, again, the distance from the point to
the plane, we do the same but adjusting for distance:

::

    var distance = N.dot(point) - D

The same thing, using a built-in function:

::

    var distance = plane.distance_to(point)

This will, again, return either a positive or negative distance.

Flipping the polarity of the plane is also very simple, just negate both
N and D. This will result in a plane in the same position, but with
inverted negative and positive half spaces:

::

    N = -N
    D = -D

Of course, Godot also implements this operator in :ref:`Plane <class_Plane>`,
so doing:

::

    var inverted_plane = -plane

Will work as expected.

So, remember, a plane is just that and it's main practical use is
calculating the distance to it. So, why is it useful to calculate the
distance from a point to a plane? It's extremely useful! Let's see some
simple examples..

Constructing a plane in 2D
--------------------------

Planes clearly don't come out of nowhere, so they must be built.
Constructing them in 2D is easy, this can be done from either a normal
(unit vector) and a point, or from two points in space.

In the case of a normal and a point, most of the work is done, as the
normal is already computed, so just calculate D from the dot product of
the normal and the point.

::

    var N = normal
    var D = normal.dot(point)

For two points in space, there are actually two planes that pass through
them, sharing the same space but with normal pointing to the opposite
directions. To compute the normal from the two points, the direction
vector must be obtained first, and then it needs to be rotated 90°
degrees to either side:

::

    # calculate vector from a to b
    var dvec = (point_b - point_a).normalized()
    # rotate 90 degrees
    var normal = Vector2(dvec.y,-dev.x)
    # or alternatively
    # var normal = Vector2(-dvec.y,dev.x)
    # depending the desired side of the normal

The rest is the same as the previous example, either point_a or
point_b will work since they are in the same plane:

::

    var N = normal
    var D = normal.dot(point_a)
    # this works the same
    # var D = normal.dot(point_b)

Doing the same in 3D is a little more complex and will be explained
further down.

Some examples of planes
-----------------------

Here is a simple example of what planes are useful for. Imagine you have
a `convex <http://www.mathsisfun.com/definitions/convex.html>`__
polygon. For example, a rectangle, a trapezoid, a triangle, or just any
polygon where faces that don't bend inwards.

For every segment of the polygon, we compute the plane that passes by
that segment. Once we have the list of planes, we can do neat things,
for example checking if a point is inside the polygon.

We go through all planes, if we can find a plane where the distance to
the point is positive, then the point is outside the polygon. If we
can't, then the point is inside.

.. image:: /img/tutovec13.png

Code should be something like this:

::

    var inside = true
    for p in planes:
        # check if distance to plane is positive
        if (N.dot(point) - D > 0):
            inside = false
            break # with one that fails, it's enough

Pretty cool, huh? But this gets much better! With a little more effort,
similar logic will let us know when two convex polygons are overlapping
too. This is called the Separating Axis Theorem (or SAT) and most
physics engines use this to detect collision.

The idea is really simple! With a point, just checking if a plane
returns a positive distance is enough to tell if the point is outside.
With another polygon, we must find a plane where *all the **other**
polygon points* return a positive distance to it. This check is
performed with the planes of A against the points of B, and then with
the planes of B against the points of A:

.. image:: /img/tutovec14.png

Code should be something like this:

::

    var overlapping = true

    for p in planes_of_A:
        var all_out = true
        for v in points_of_B:
            if (p.distance_to(v) < 0):
                all_out = false
                break

        if (all_out):
            # a separating plane was found
            # do not continue testing
            overlapping = false
            break

    if (overlapping):
        # only do this check if no separating plane
        # was found in planes of A
        for p in planes_of_B:
            var all_out = true
            for v in points_of_A:
                if (p.distance_to(v) < 0):
                    all_out = false
                    break

            if (all_out):
                overlapping = false
                break

    if (overlapping):
        print("Polygons Collided!")

As you can see, planes are quite useful, and this is the tip of the
iceberg. You might be wondering what happens with non convex polygons.
This is usually just handled by splitting the concave polygon into
smaller convex polygons, or using a technique such as BSP (which is not
used much nowadays).

Cross product
-------------

Quite a lot can be done with the dot product! But the party would not be
complete without the cross product. Remember back at the beginning of
this tutorial? Specifically how to obtain a perpendicular (rotated 90
degrees) vector by swapping x and y, then negating either of them for
right (clockwise) or left (counter-clockwise) rotation? That ended up
being useful for calculating a 2D plane normal from two points.

As mentioned before, no such thing exists in 3D because a 3D vector has
infinite perpendicular vectors. It would also not make sense to obtain a
3D plane from 2 points, as 3 points are needed instead.

To aid in this kind stuff, the brightest minds of humanity's top
mathematicians brought us the **cross product**.

The cross product takes two vectors and returns another vector. The
returned third vector is always perpendicular to the first two. The
source vectors, of course, must not be the same, and must not be
parallel or opposite, else the resulting vector will be (0,0,0):

.. image:: /img/tutovec16.png

The formula for the cross product is:

::

    var c = Vector3()
    c.x = (a.y + b.z) - (a.z + b.y)
    c.y = (a.z + b.x) - (a.x + b.z)
    c.z = (a.x + b.y) - (a.y + b.x)

This can be simplified, in Godot, to:

::

    var c = a.cross(b)

However, unlike the dot product, doing ``a.cross(b)`` and ``b.cross(a)``
will yield different results. Specifically, the returned vector will be
negated in the second case. As you might have realized, this coincides
with creating perpendicular vectors in 2D. In 3D, there are also two
possible perpendicular vectors to a pair of 2D vectors.

Also, the resulting cross product of two unit vectors is *not* a unit
vector. Result will need to be renormalized.

Area of a triangle
~~~~~~~~~~~~~~~~~~

Cross product can be used to obtain the surface area of a triangle in
3D. Given a triangle consisting of 3 points, **A**, **B** and **C**:

.. image:: /img/tutovec17.png

Take any of them as a pivot and compute the adjacent vectors to the
other two points. As example, we will use B as a pivot:

::

    var BA = A - B
    var BC = C - B

.. image:: /img/tutovec18.png

Compute the cross product between **BA** and **BC** to obtain the
perpendicular vector **P**:

::

    var P = BA.cross(BC)

.. image:: /img/tutovec19.png

The length (magnitude) of **P** is the surface area of the parallelogram
built by the two vectors **BA** and **BC**, therefore the surface area
of the triangle is half of it.

::

    var area = P.length()/2

Plane of the triangle
~~~~~~~~~~~~~~~~~~~~~

With **P** computed from the previous step, normalize it to get the
normal of the plane.

::

    var N = P.normalized()

And obtain the distance by doing the dot product of P with any of the 3
points of the **ABC** triangle:

::

    var D = P.dot(A)

Fantastic! You computed the plane from a triangle!

Here's some useful info (that you can find in Godot source code anyway).
Computing a plane from a triangle can result in 2 planes, so a sort of
convention needs to be set. This usually depends (in video games and 3D
visualization) to use the front-facing side of the triangle.

In Godot, front-facing triangles are those that, when looking at the
camera, are in clockwise order. Triangles that look Counter-clockwise
when looking at the camera are not drawn (this helps to draw less, so
the back-part of the objects is not drawn).

To make it a little clearer, in the image below, the triangle **ABC**
appears clock-wise when looked at from the *Front Camera*, but to the
*Rear Camera* it appears counter-clockwise so it will not be drawn.

.. image:: /img/tutovec20.png

Normals of triangles often are sided towards the direction they can be
viewed from, so in this case, the normal of triangle ABC would point
towards the front camera:

.. image:: /img/tutovec21.png

So, to obtain N, the correct formula is:

::

    # clockwise normal from triangle formula
    var N = (A-C).cross(A-B).normalized()
    # for counter-clockwise:
    # var N = (A-B).cross(A-C).normalized()
    var D = N.dot(A)

Collision detection in 3D
~~~~~~~~~~~~~~~~~~~~~~~~~

This is another bonus bit, a reward for being patient and keeping up
with this long tutorial. Here is another piece of wisdom. This might
not be something with a direct use case (Godot already does collision
detection pretty well) but It's a really cool algorithm to understand
anyway, because it's used by almost all physics engines and collision
detection libraries :)

Remember that converting a convex shape in 2D to an array of 2D planes
was useful for collision detection? You could detect if a point was
inside any convex shape, or if two 2D convex shapes were overlapping.

Well, this works in 3D too, if two 3D polyhedral shapes are colliding,
you won't be able to find a separating plane. If a separating plane is
found, then the shapes are definitely not colliding.

To refresh a bit a separating plane means that all vertices of polygon A
are in one side of the plane, and all vertices of polygon B are in the
other side. This plane is always one of the face-planes of either
polygon A or polygon B.

In 3D though, there is a problem to this approach, because it is
possible that, in some cases a separating plane can't be found. This is
an example of such situation:

.. image:: /img/tutovec22.png

To avoid it, some extra planes need to be tested as separators, these
planes are the cross product between the edges of polygon A and the
edges of polygon B

.. image:: /img/tutovec23.png

So the final algorithm is something like:

::

    var overlapping = true

    for p in planes_of_A:
        var all_out = true
        for v in points_of_B:
            if (p.distance_to(v) < 0):
                all_out = false
                break

        if (all_out):
            # a separating plane was found
            # do not continue testing
            overlapping = false
            break

    if (overlapping):
        # only do this check if no separating plane
        # was found in planes of A
        for p in planes_of_B:
            var all_out = true
            for v in points_of_A:
                if (p.distance_to(v) < 0):
                    all_out = false
                    break

            if (all_out):
                overlapping = false
                break

    if (overlapping):
        for ea in edges_of_A:
            for eb in edges_of_B:
                var n = ea.cross(eb)
                if (n.length() == 0):
                    continue

                var max_A = -1e20 # tiny number
                var min_A = 1e20 # huge number

                # we are using the dot product directly
                # so we can map a maximum and minimum range
                # for each polygon, then check if they
                # overlap.

                for v in points_of_A:
                    var d = n.dot(v)
                    if (d > max_A):
                        max_A = d
                    if (d < min_A):
                        min_A = d

                var max_B = -1e20 # tiny number
                var min_B = 1e20 # huge number

                for v in points_of_B:
                    var d = n.dot(v)
                    if (d > max_B):
                        max_B = d
                    if (d < min_B):
                        min_B = d

                if (min_A > max_B or min_B > max_A):
                    # not overlapping!
                    overlapping = false
                    break

            if (not overlapping):
                break

    if (overlapping):
       print("Polygons collided!")

This was all! Hope it was helpful, and please give feedback and let know
if something in this tutorial is not clear! You should be now ready for
the next challenge... :ref:`doc_matrices_and_transforms`!
