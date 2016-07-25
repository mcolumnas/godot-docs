.. _doc_vector_math:

Matemática vectorial
====================

Introducción
~~~~~~~~~~~~

Este pequeño tutorial tiene como objetivo ser una introducción corta y
práctica a la matemática de vectores, útil para juegos 3D pero también
3D. De nuevo, la matemática de vectores es útil para juegos 3D pero
*también* 3D. Es una herramienta asombrosa cuando la entiendes y hace a
la programación de comportamientos complejos mucho más simple.

A menudo sucede que programadores jóvenes confían demasiado en la
matemática *incorrecta* para resolver un amplio abanico de problemas,
por ejemplo usando solo trigonometría en lugar de matemática de vectores
para juegos 2D.

Este tutorial se enfocara en el uso práctico, con aplicación inmediata al
arte de la programación de juegos.

Sistema de coordenadas (2D)
~~~~~~~~~~~~~~~~~~~~~~~~~~~

Típicamente, definimos coordenadas como un par (x,y), x representa el
valor horizontal e y el vertical. Esto tiene sentido dado que la pantalla
es solo un rectángulo en dos dimensiones. Como ejemplo, aquí hay una
posición en espacio 2D:

.. image:: /img/tutovec1.png

Una posición puede estar en cualquier lugar del espacio. La posición (0,0)
tiene un nombre, es llamada el **origen**. Recuerda este término bien
porque tiene más usos implícitos luego. El (0,0) de un sistema de
n-coordenadas es el **origen**.

En matemática vectorial, las coordenadas tienen dos usos diferentes, ambos
igualmente importantes. Son usadas para representar una *posición* pero
también un *vector*. La misma posición que antes, cuando se imagina como
un vector, tiene diferente significado.

.. image:: /img/tutovec2.png

Cuando se imagina como vector, dos propiedades pueden inferirse, la
**dirección** y la **magnitud**. Cada posición en el espacio puede ser
un vector, con la excepción del **origen**. Esto es porque las coordenadas
(0,0) no pueden representar dirección (magnitud 0).

.. image:: /img/tutovec2b.png

Dirección
---------

Dirección es simplemente hacia donde el vector apunta. Imagina una flecha
que comienza en el **origen** y va hacia la [STRIKEOUT:posición]. La
punta de la flecha está en la posición, por lo que siempre apunta hacia
afuera, alejándose del origen. Imaginarse vectores como flechas ayuda
mucho.

.. image:: /img/tutovec3b.png

Magnitud
--------

Finalmente, el largo del vector es la distancia desde el origen hasta la
posición. Obtener el largo del vector es fácil, solo usa el te teorema
Pitagórico <http://en.wikipedia.org/wiki/Pythagorean_theorem>`__.

::

    var len = sqrt( x*x + y*y )

Pero... ángulos?
----------------

Pero porque no usar un *ángulo*? Después de todo, podríamos pensar de
un vector como un ángulo y una magnitud, en lugar de una dirección y
una magnitud. Los ángulos también son un concepto más familiar.

Para decir la verdad, los ángulos no son tan útiles en matemática de
vectores, y la mayoría del tiempo no se los trata directamente. Tal vez
funcionen en 2D, pero en 3D un montón de lo que usualmente puede ser
hecho con ángulos no funciona.

Igual, usar ángulos no es una excusa, aun en 2D. La mayoría de lo que
toma un montón de trabajo con ángulos en 2D, es de todas formas mas
natural y fácil de lograr con matemática de vectores. En matemática
vectorial, los ángulos son útiles solo como medida, pero tienen poca
participación en la matemática. Entonces, deja de lado la trigonometría
de una vez, y prepárate para adoptar vectores!

En cualquier caso, obtener el ángulo desde el vector es fácil y puede
ser logrado con trig... er, que fue eso? Digo, la función
:ref:`atan2() <class_@GDScript_atan2>`.

Vectores es Godot
~~~~~~~~~~~~~~~~

Para hacer los ejemplos más fáciles, vale la pena explicar cómo los
vectores son implementados en GDScript. GDscript tiene ambos
:ref:`Vector2 <class_Vector2>` y :ref:`Vector3 <class_Vector3>`,
para matemática 2D y 3D respectivamente. Godot usa clases de vectores
tanto para posición como dirección. También contienen variables miembro
x e y (para 2D) y x, y, z (para 3D).


::

    # crea un vector con coordenadas (2,5)
    var a = Vector2(2,5)
    # crea un vector y asigna x e y manualmente
    var b = Vector2()
    b.x = 7
    b.y = 8

Cuando se opera con vectores, no es necesario operar directamente en
los miembros (en realidad esto es mucho más lento). Los vectores
soportan operaciones aritméticas regulares:

::

    # add a and b
    var c = a + b
    # resultara en un vector c, con valores (9,13)

Es lo mismo que hacer:

::

    var c = Vector2()
    c.x = a.x + b.x
    c.y = a.y + b.y

Excepto que lo primero es mucho más eficiente y legible.

Las operaciones aritméticas regulares como adición, sustracción,
multiplicación y división son soportadas.

La multiplicación y división también puede ser mezclada con números
de digito único, también llamados **escalares**.

::

    # multiplicación de vector por escalar
    var c = a*2.0
    # resultara en el vector c, con valor (4,10)

Es lo mismo que hacer

::

    var c = Vector2()
    c.x = a.x*2.0
    c.y = a.y*2.0

Excepto que, nuevamente, lo primero es mucho más eficiente y legible.

Vectores perpendiculares
~~~~~~~~~~~~~~~~~~~~~~~~

Rotar un vector 2D 90º para algún lado, izquierda o derecha, es realmente
fácil, solo intercambia x e y, luego niega x o y (la dirección de la
rotación depende en cual es negado).

.. image:: /img/tutovec15.png

Ejemplo:

::

    var v = Vector2(0,1)
    # rotar a la derecha (horario)
    var v_right = Vector2(-v.y, v.x)
    # rotar a la izquierda (antihorario)
    var v_right = Vector2(v.y, -v.x)

Este es un truco práctico que se usa a menudo. Es imposible de hacer con
vectores 3D, porque hay un número infinito de vectores perpendiculares.

Vectores unitarios
~~~~~~~~~~~~~~~~~~

OK, entonces sabemos lo que es un vector. Tiene una **dirección** y una
**magnitud**. También sabemos cómo usarlos en Godot. El siguiente paso
es aprender sobre **vectores unitarios**. Cualquier vector con una
magnitud de largo 1 es considerado un **vector unitario**. En 2D,
imagina dibujar un circulo de radio uno. Ese círculo contiene todos
los vectores unitarios en existencia para dos dimensiones.

.. image:: /img/tutovec3.png

Entonces, que es tan especial sobre los vectores unitarios? Los vectores
unitarios son asombrosos. En otras palabras, los vectores unitarios
tienen **varias propiedades muy útiles**.

No puedes esperar a saber más sobre las fantásticas propiedades de los
vectores unitarios, pero un paso a la vez. Así que, como se crea un
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

Como habrás adivinado, si el vector tiene magnitud 0 (lo que significa
que no es un vector pero el **origen** también llamado *vector nulo*),
ocurre una división por 0 y el universo atraviesa un segundo big bang,
excepto que en polaridad reversa y luego de vuelta. Como resultado,
la humanidad está a salvo pero Godot imprimira un error. Recuerda!
El vector (0,0) no puede ser normalizado!.

Por supuesto, Vector2 y Vector3 ya tienen un método para esto:

::

    a = a.normalized()

Producto escalar
~~~~~~~~~~~~~~~~

OK, el **producto escalar** es la parte más importante de matemática
de vectores. Sin el producto escalar, Quake nunca hubiera sido hecho.
Esta es la sección más importante del tutorial, así que asegúrate que
lo entiendes. La mayoria de la gente que intenta aprender matemática
de vectores abandona acá, a pesar de lo simple que es, no le encuetran
pie ni cabeza. Porque? Aquí está el porqué, es porque...

El producto escalar toma dos vectores y regresa un **escalar**:

::

    var s = a.x*b.x + a.y*b.y

Si, básicamente eso. Multiplica **x** del vector **a** por **x** del
vector **b**. Haz lo mismo con y, luego súmalo. En 3D es muy parecido:

::

    var s = a.x*b.x + a.y*b.y + a.z*b.z

Lo se, no tienen ningún sentido! Hasta puedes hacerlo con una función
incorporada:

::

    var s = a.dot(b)

El orden de los dos vectores *no* importa, ``a.dot(b)`` retorna el
mismo valor que ``b.dot(b)``.

Aquí es donde empieza la desesperación y los libros y tutoriales te
muestran esta fórmula:

.. image:: /img/tutovec4.png

Y te das cuenta que es tiempo de abandonar el desarrollo de juegos 3D
o 2D complejos. Como puede ser que algo tan simple sea tan complejo?
Alguien más tendrá que hacer el próximo Zelda o Call of Duty. Los
RPGs vistos de arriba no lucen tan mal después de todo. Sip, escuche
que alguien lo ha hecho bastante bien con uno de esos en steam...

Así que este es tu momento, tu tiempo de brillar. **NO TE RINDAS**!
En este punto, el tutorial tomara un giro rápido y se enfocara en
lo que hace útil al producto escalar. Esto es, **porque** es útil.
Nos enfocaremos uno por uno en los casos de uso del producto escalar,
con aplicación en la vida real. No más fórmulas que no tienen ningún
sentido. Las formulas tendrán un montón de sentido *una vez que aprendes*
para que son útiles.

Lado
------

La primer utilidad y mas importante propiedad del producto escalar es
para chequear para que lado miran las cosas. Imaginemos que tenemos
dos vectores cualquiera, **a** y **b**. Cualquier **dirección** o
**magnitud** (menos el **origen**). No importa lo que sean, solo
imaginemos que computamos el producto escalar entre ellos.

::

    var s = a.dot(b)

La operación retornara un único número de punto flotante (pero como
estamos en el mundo de vectores, lo llamamos **scalar**, seguiremos
usando ese término de ahora en más). El número nos dirá lo siguiente:

-  Si el número es más grande que cero, ambos están mirando hacia la
   misma dirección (el ángulo entre ellos es < 90° grados).
-  Si el número es menor que cero, ambos están mirando en direcciones
   opuestas (el ángulo entre ellos es > 90° grados).
-  Si el número es cero, los vectores tienen forma de L (el ángulo
   entre ellos *es* 90° grados).

.. image:: /img/tutovec5.png

Así que pensemos de un escenario de caso de uso real. Imaginemos que
una serpiente está yendo por un bosque, y esta el enemigo cerca. Como
podemos decir rápidamente si el enemigo descubrió la serpiente? Para
poder descubrirla, el enemigo debe poder *ver* la serpiente. Digamos,
entonces que:

-  La serpiente está en la posición **A**.
-  El enemigo está en la posición **B**.
-  El enemigo está *mirando* por el vector de dirección **F**.

.. image:: /img/tutovec6.png

Así que, vamos a crear un nuevo vector **BA** que va desde el guardia
(**B**) hasta la serpiente (**A**), al restarlos:

::

    var BA = A - B

.. image:: /img/tutovec7.png

Idealmente, si el guardia está mirando hacia la serpiente, para hacer
contacto de ojo, necesitaría hacerlo en la misma dirección que el vector
BA.

Si el producto escalar entre **F** y **BA** es mayor que 0, entonces la
serpiente será descubierta. Esto sucede porque podremos saber si el
guardia está mirando hacia ese lado:

::

    if (BA.dot(F) > 0):
        print("!")

Parece que la serpiente está a salvo por ahora.

Lado con vectores unitarios
~~~~~~~~~~~~~~~~~~~~~~~~~~~

Bien, entonces ahora sabemos que el producto escalar entre dos vectores
nos dejara saber si miran hacia el mismo lado, lados contrarios o son
perpendiculares entre sí.

Esto funciona igual con todos los vectores, no importa la magnitud por
lo que los **vectores unitarios** no son la excepción. Sin embargo,
usando las mismas propiedades con los vectores unitarios brinda un
resultado aun más interesante, ya que se agrega una propiedad extra:

-  Si ambos vectores están mirando exactamente hacia la misma dirección
   (paralelos, ángulo entre ellos de 0°), el escalar resultante es **1**.
-  Si ambos vectores están mirando en dirección exactamente opuesta
   (paralelos, ángulo entre ellos es 180°), el escalar resultante es
   **-1**.

Esto significa que el producto escalar entre dos vectores unitarios
siempre esta en el rango de 1 a -1. Asique de nuevo...

-  Si su ángulo es **0°** el producto escalar es **1**.
-  Si su ángulo es **90°** el producto escalar es **0**.
-  Si su ángulo es **180°** el producto escalar es **-1**.

Mm... esto es extrañamente familiar... lo he visto antes... dónde?

Tomemos dos vectores unitarios. El primero apunta hacia arriba, el
segundo también pero lo rotaremos desde arriba (0°) hasta abajo
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
unitarios. Imagina que perpendicular al vector (y a través del origen)
pasa un plano. Los planos dividen el espacio entero entre positivo
(sobre el plano) y negativo (bajo el plano), y (contrario a la creencia
popular) también puedes usar su matemática en 2D:

.. image:: /img/tutovec10.png

Los vectores unitarios que son perpendiculares a la superficie (por lo
que, ellos describen la orientación de la superficie) son llamados
**vectores unitarios normales**. Aunque, usualmente se abrevian solo
como \*normals. Las normales aparecen en planos, geometría 3D (para
determinar hacia donde mira cada cara o vector), etc. Una **normal**
*es* un **vector unitario**, pero se llama *normal* por su uso.
(De la misma forma que llamamos Origen a (0.0)!).

Es tan simple como luce. El plano pasa por el origen y la superficie
de el es perpendicular al vector unitario (o *normal*). El lado hacia
el cual apunta el vector es el medio-espacio positivo, mientras que
el otro lado es el medio-espacio negativo. En 3D esto es exactamente
lo mismo, excepto que el plano es una superficie infinita (imagina
una pila de papel plana e infinita que puedes orientar y está sujeta
al origen) en lugar de una línea.

Distancia a plano
-----------------

Ahora que está claro lo que es un plano, vamos nuevamente al producto
escalar. El producto escalar entre dos **vectores unitarios* y cualquier
**punto en el espacio** (si, esta vez hacemos el producto escalar entre
vector y posición), regresa la **distancia desde el punto al plano**:
::

    var distance = normal.dot(point)

Pero no solo la distancia absoluta, si el punto está en en la mitad
negativa del espacio la distancia será negativa, también:

.. image:: /img/tutovec11.png

Esto nos permite saber de qué lado de un plano esta un punto.

Fuera del origen
----------------

Se lo que estás pensando! Hasta ahora viene bien, pero los planos
*verdaderos* están por todos lados en el espacio, no solo pasando por
el origen. Quieres acción con *planos* reales y lo quieres *ahora*.

Recuerda que los planos no solo dividen el espacio en dos, pero también
tienen *polaridad*. Esto significa que es posible tener planos
perfectamente superpuestos, pero sus espacios negativos y positivos
están intercambiados.

Con esto en mente, vamos a describir un plano completo como una **normal**
*N* y una **distancia desde origen** escalar *D*. Entonces, nuestro plano
es representado por N y D. Por ejemplo:

.. image:: /img/tutovec12.png

Para matemática 3D, Godot provee un tipo incorporado :ref:`Plane <class_Plane>`
que maneja esto.

Básicamente, N y D pueden representar cualquier plano en el espacio, sea
2D o 3D (dependiendo de la cantidad de las dimensiones de N) y la
matemática es la misma para ambos. Es lo mismo que antes, pero D es la
distancia desde el origen al plano, viajando en dirección N. Como un
ejemplo, imagina que quieres llegar a un punto en el plano, solo harías:

::

    var point_in_plane = N*D

Esto va a estirar (cambiar el tamaño) el vector normal y lo hará tocar el
plano. Esta matemática puede parecer confusa, pero es en realidad mucho
más simple de lo que parece. Si queremos saber, nuevamente, la distancia de
un punto al plano, hacemos lo mismo pero ajustándolo para la distancia:

::

    var distance = N.dot(point) - D

Lo mismo, usando una función incorporada:

::

    var distance = plane.distance_to(point)

Esto, nuevamente, regresara una distancia positiva o negativa.

Dar vuelta la polaridad del plano también es muy simple, solo niega
ambos N y D. Esto resultara en un plano en la misma posición, pero
invirtiendo las mitades positivas y negativas del espacio:

::

    N = -N
    D = -D

Por supuesto, Godot también implementa el operador en :ref:`Plane <class_Plane>`,
por lo que haciendo:

::

    var inverted_plane = -plane

Funcionará como se espera.

Así que, recuerda, un plano es solo eso y su uso práctico más importante
es calcular la distancia a él. Entonces, por qué es útil calcular la
distancia desde un punto al plano? Es extremadamente útil! Vamos a ver
algunos ejemplos simples..

Construyendo un plano en 2D
---------------------------

Los planos claramente no vienen de la nada, así que deben ser construidos.
Construirlos en 2D es fácil, esto puede ser hecho ya sea de una normal
(vector unitario) y un punto, o de dos puntos en el espacio.

En el caso de la normal y el punto, la mayor parte del trabajo está hecho,
porque la normal ya está computada, por lo q es solo calcular D desde el
producto escalar de la normal y el punto.

::

    var N = normal
    var D = normal.dot(point)

Para dos puntos en el espacio, hay en realidad dos planos que pasan por
ellos, compartiendo el mismo espacio pero con la normal apuntando en
direcciones opuestas. Para computar la normal desde los dos puntos, el
vector de dirección debe ser obtenido en primer lugar, y luego necesita
ser rotado 90° para cualquiera de los dos lados:

::

    # calcular vector desde a to b
    var dvec = (point_b - point_a).normalized()
    # rotatr 90 grados
    var normal = Vector2(dvec.y,-dev.x)
    # o alternativamente
    # var normal = Vector2(-dvec.y,dev.x)
    # dependiendo del lado deseado de la normal

El resto es lo mismo que en el ejemplo previo, tanto point_a o point_b
funcionará ya que están en el mismo plano:

::

    var N = normal
    var D = normal.dot(point_a)
    # esto funciona igual
    # var D = normal.dot(point_b)


Hacer lo mismo en 3D es un poco más complejo y será explicado mas abajo.

Algunos ejemplos de planos
--------------------------

Aquí hay un ejemplo simple sobre para que son útiles los planos. Imagina
que tienes un polígono `convexo <http://www.mathsisfun.com/definitions/convex.html>`__
Por ejemplo, un rectángulo, un trapezoide, un triángulo, o el polígono que
sea donde las caras no se doblan hacia adentro.

Para cada segmento del polígono, computamos el plano que pasa por dicho
segmento. Una vez tenemos la lista de planos, podemos hacer cosas
interesantes, por ejemplo chequear si un punto está dentro de un polígono.

Vamos a través de todos los planos, si podemos encontrar un plano donde la
distancia al punto es positiva, entonces el punto está fuera del polígono.
Si no podemos, está dentro

.. image:: /img/tutovec13.png

El código sería algo como esto:

::

    var inside = true
    for p in planes:
        # chequear si la distancia al plano es positiva
        if (N.dot(point) - D > 0):
            inside = false
            break # si falla uno, es suficiente

Bastante copado, eh? Pero se pone mucho mejor! Con un poco más de
esfuerzo, lógica similar nos permitirá saber cuándo dos polígonos
convexos están superpuestos también. Esto se llama "Teorema de separación
de ejes" (Separating Axis Theorem SAT) y la mayoría de los motores de
física lo usan para detectar colisiones.

La idea es realmente simple! Con un punto, solo chequear si un plano
retorna una distancia positiva es suficiente para saber si el punto
esta afuera. Con otro polígono, debemos encontrar un plano donde *todos
los **demás** puntos del polígono* retornan una distancia positiva a él.
Este chequeo es hecho con los planos A contra los puntos de B, y luego con
los planos B contra los puntos de A:

.. image:: /img/tutovec14.png

El código debería ser parecido a esto:

::

    var overlapping = true

    for p in planes_of_A:
        var all_out = true
        for v in points_of_B:
            if (p.distance_to(v) < 0):
                all_out = false
                break

        if (all_out):
            # se encontró un plano separador
            # no continuar probando
            overlapping = false
            break

    if (overlapping):
        # solo haz este chequeo si no se encontraron planos
        # de separación en los planos de A
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
        print("Los polígonos colisionaron!")

Como puedes ver, los planos son bastante útiles, y esto es la punta del
iceberg. Puedes estarte preguntando que sucede con los polígonos no
convexos. Esto en general se hace dividiendo el polígono concavo en
polígonos convexos mas pequeños, o usando una técnica como BSP (la cual
ya no se usa mucho hoy en día).



Producto Vectorial
------------------

Un montón puede ser hecho con el producto escalar! Pero la fiesta no
seria completa sin el producto vectorial. Recuerdas al comienzo de este
tutorial? Específicamente como obtener un vector perpendicular (rotado
90 grados) al intercambiar x e y, luego negando uno para rotación derecha
(horario) o izquierda (anti-horario)? Eso termino siendo útil para
calcular un plano 2D normal desde dos puntos.

Como se mencionó antes, no existe tal cosa en 3D porque un vector 3D
tiene infinitos vectores perpendiculares. Tampoco tendría sentido obtener
un plano 3D con 2 puntos, ya que en su lugar se necesitan 3 puntos.

Para ayudarnos con este tipo de cosas, las mentes más brillantes de los
principales matemáticos nos trajo el **producto vectorial**.

El producto vectorial toma dos vectores y retorna otro vector. El tercer
vector retornado siempre es perpendicular a los dos primeros. Los vectores
fuente, por supuesto, no deben ser iguales, y no deben ser paralelos u
opuestos, de lo contrario el vector resultante será (0,0,0):

.. image:: /img/tutovec16.png

La formula para el producto vectorial es:

::

    var c = Vector3()
    c.x = (a.y + b.z) - (a.z + b.y)
    c.y = (a.z + b.x) - (a.x + b.z)
    c.z = (a.x + b.y) - (a.y + b.x)

Esto puede ser simplificado, en Godot, a:

::

    var c = a.cross(b)

Sin embargo, a diferencia del producto escalar, hacer ``a.cross(b)`` y
``b.cross(a)`` producirá resultados diferentes. Específicamente, el
vector retornado será negado para el segundo caso. Como te habrás dado
cuenta, esto coincide con crear planos perpendiculares en 2D. En 3D,
también hay dos posibles vectores perpendiculares a un par de vectores
2D.

Además, el resultado del producto vectorial de dos vectores unitarios
*no* es un vector unitario. El resultado deberá ser re normalizado.

área de un triángulo
~~~~~~~~~~~~~~~~~~~~

El producto vectorial puede ser usado para obtener la superficie de un
triángulo en 3D. Dado que un triángulo consiste de 3 puntos, **A**, **B**
y **C**:

.. image:: /img/tutovec17.png

Toma cualquiera de ellos como un pívot y computa los vectores adyacentes
a los otros dos puntos. A modo de ejemplo: usaremos B como pívot.

::

    var BA = A - B
    var BC = C - B

.. image:: /img/tutovec18.png

Computa el producto vectorial entre **BA** y **BC** para obtener el
vector perpendicular **P**:

::

    var P = BA.cross(BC)

.. image:: /img/tutovec19.png

El largo (magnitud) de **P** es la superficie del área del paralelogramo
construido por los dos vectores **BA** y **BC**, por lo cual el área de
superficie del triángulo es la mitad de él.

::

    var area = P.length()/2

Plano de un triángulo
~~~~~~~~~~~~~~~~~~~~~

Con **P** computado desde el paso previo, normalizalo para obtener la
normal del plano.

::

    var N = P.normalized()

Y obtiene la distancia al hacer el producto escalar de P con cualquiera
de los 3 puntos del triángulo **ABC**:

::

    var D = P.dot(A)

Fantástico! Computaste el plano desde un triángulo!

Aquí un poco de información útil (que puedes encontrar en el código
fuente de Godot de todas formas). Computar un plano desde un triángulo
puede resultar en 2 planos, por lo que alguna convención debe ser
ajustada. Esto usualmente depende (en juegos de video y para visualización
3D) en usar el lado del triángulo que mira al frente.

En Godot, los triángulos que miran al frente son aquellos que, cuando
cuando están mirando a la cámara, están en orden horario. Los triángulos
que miran de forma anti horaria a la cámara no son dibujados (esto ayuda
a dibujar menos, así la parte trasera de los objetos no es dibujada).

Para hacerlo un poco más claro, en la imagen de abajo, el triángulo
**ABC** aparece de forma horaria cuando se lo mira desde la *Cámara
Frontal*, pero a la *Cámara trasera* aparece como anti horario por lo
que no será dibujado.

.. image:: /img/tutovec20.png

Las normales de los triángulos a menudo están hacia el lado de
dirección que se pueden ver, por lo que en este caso, la normal del
triángulo ABC apuntaría hacia la cámara frontal:

.. image:: /img/tutovec21.png

Así que, para obtener N, la formula correcta es:

::

    #  normal horaria de la fórmula del triángulo
    var N = (A-C).cross(A-B).normalized()
    # para anti horario:
    # var N = (A-B).cross(A-C).normalized()
    var D = N.dot(A)

Detección de colisión en 3D
~~~~~~~~~~~~~~~~~~~~~~~~~~~

Este es otro pequeño bono, una recompensa por ser paciente y mantenerse
en este largo tutorial. Aquí hay otra pieza de sabiduría. Esto puede no
ser algo con un caso de uso directo (Godot ya hace la detección de
colisión bastante bien) pero es un algoritmo realmente copado para
entender de todas formas, porque es usado por casi todos los motores
físicos y librerías de detección de colisión :)

Recuerdas que convertir una figura 2D convexa a un arreglo de planos
2D fue útil para la detección de colisión? Puedes detectar si un punto
estaba dentro de una figura convexa, o si dos figuras 2D convexas están
superpuestas.

Bueno, esto funciona en 3D también, si dos figuras 3D poliedros están
colisionando, no podrás encontrar un plano separador. Si un plano
separador se encuentra, entonces las formas definitivamente no están
colisionando.

Para refrescar un poco un plano separador significa que todos los
vértices del polígono A están en un lado del plano, y todos los
vértices del polígono B están en el otro lado. Este plano es siempre
uno de los planos de las caras del polígono A o B.

.. image:: /img/tutovec22.png

Para evitarlo, algunos planos extra deben ser probados como separadores,
estos planos con el producto vectorial entre los lados del polígono A y
los lados del polígono B:


.. image:: /img/tutovec23.png

Por lo que el algoritmo final es algo así:

::

    var overlapping = true

    for p in planes_of_A:
        var all_out = true
        for v in points_of_B:
            if (p.distance_to(v) < 0):
                all_out = false
                break

        if (all_out):
            # un plano separador fue encontrado
            # no continuar probando
            overlapping = false
            break

    if (overlapping):
        # solo haz este chequeo si no fue encontrado
        # un plano separador en los planos de A
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

                var max_A = -1e20 # numero diminuto
                var min_A = 1e20 # numero enorme

                # estamos usando el producto escalar directamente
                # por lo que podemos mapear un rango máximo y mínimo
                # para cada polígono, luego chequear si se superponen

                for v in points_of_A:
                    var d = n.dot(v)
                    if (d > max_A):
                        max_A = d
                    if (d < min_A):
                        min_A = d

                var max_B = -1e20 # numero diminuto
                var min_B = 1e20 # numero enorme

                for v in points_of_B:
                    var d = n.dot(v)
                    if (d > max_B):
                        max_B = d
                    if (d < min_B):
                        min_B = d

                if (min_A > max_B or min_B > max_A):
                    # no se superponen!
                    overlapping = false
                    break

            if (not overlapping):
                break

    if (overlapping):
       print("Los polígonos colisionaron!")

Esto fue todo! Espero que haya sido de ayuda, y por favor danos tu
feedback y déjanos saber si algo en este tutorial no es claro! Deberías
estar pronto para el siguiente desafío... :ref:`doc_matrices_and_transforms`!
