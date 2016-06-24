.. _doc_particle_systems_2d:

Sistema de Partículas (2D)
==========================

Intro
-----

Un sistema simple (aunque suficientemente flexible para la mayoría de
los usos) de partículas se provee. Los sistemas de partícula son
usados para simular efectos fisicos complejos como chispas, fuego,
partículas de magia, humo, niebla, magia, etc.

La idea es que la "partícula" es emitida en un intervalo fijo y con un
tiempo de vida fijo. Durante su vida, cada partícula tendrá el mismo
comportamiento base. Lo que hace diferente a cada partícula y provee
un aspecto mas orgánico es la "aleatoriedad" asociada a cada parámetro.
En esencia, crear un sistema de partículas significa ajustar parámetros
de física base y luego agregarles aleatoriedad.

Particles2D
~~~~~~~~~~~

Los sistema de partículas son agregados a la escena con el nodo
:ref:`Particles2D <class_Particles2D>`. Están habilitadas por defecto
y empiezan a emitir puntos blancos hacia abajo (como si estuviesen
afectados por la gravedad). Esto provee un punto de arranque
razonable para empezar a adaptarse a nuestras necesidades.

.. image:: /img/particles1.png

Textura
~~~~~~~

Un sistema de partícula usa una sola textura (en el futuro esto puede
ser ampliado a texturas animadas por spritesheet). La textura se ajusta
con la propiedad de textura:

.. image:: /img/particles2.png

Variables de Física
-------------------

Antes de dar un vistazo a los parámetros globales del sistema de
partículas, vamos primero a ver que pasa cuando las variables de
física son modificadas.

Direction (dirección)
---------------------

Este es el ángulo base en el que las partículas emiten. Por defecto es
0 (abajo):

.. image:: /img/paranim1.gif

Modificarlo cambiara la dirección del emisor, pero la gravedad seguirá
afectándolas:

.. image:: /img/paranim2.gif

Este parámetro es útil porque, al rotar el nodo, la gravedad también será
rotada. Cambiar dirección los mantiene separados.

Spread (propagación)
--------------------

Spread es el ángulo en el que las partículas aleatorias serán emitidas.
Aumentar el spread aumentara el ángulo. Un spread de 180 emitirá en todas
direcciones.

.. image:: /img/paranim3.gif

Linear velocity (velocidad lineal)
---------------

Linear velocity es la velocidad a la que las partículas serán emitidas
(en pixels por segundo). La velocidad puede ser luego modificada por la
gravedad u otras aceleraciones (como se describe mas abajo).

 .. image:: /img/paranim4.gif

Spin velocity (velocidad de giro)
---------------------------------

Spin velocity es la velocidad a la cual las partículas giran al rededor
de su centro (en grados por segundo).

 .. image:: /img/paranim5.gif

Orbit velocity (velocidad orbital)
----------------------------------

Velocidad orbital es usado para hacer que las partículas giren al
rededor del centro.

.. image:: /img/paranim6.gif

Gravity direction & strength (dirección de gravedad y fuerza)
-------------------------------------------------------------

Gravity puede ser modificado como dirección y fuerza. La gravedad
afecta capa partícula que esta viva.

.. image:: /img/paranim7.gif

Radial acceleration (aceleración radial)
----------------------------------------

Si esta aceleración es positiva, las partículas son aceleradas hacia
fuera del centro. Si son negativas, son absorbidas hacia el centro.

.. image:: /img/paranim8.gif

Tangential acceleration (Aceleración tangencial)
-----------------------

Esta aceleración usara el vector tangente al centro. Combinándola
con aceleración radial se pueden hacer lindos efectos.

.. image:: /img/paranim9.gif

Damping
-------

Damping aplica fricción a las partículas, forzándolas a parar. Es
especialmente útil para chispas o explosiones, las cuales usualmente
empiezan con una alta velocidad linear y luego se detienen en la medida
que se apagan.

.. image:: /img/paranim10.gif

Initial angle (Angulo inicial)
------------------------------

Determina el ángulo inicial de la partícula (en grados). Este parámetro
es mas que nada útil cuando se usa de forma aleatoria.

.. image:: /img/paranim11.gif

Initial & final size (Fase inicial y final)
-------------------------------------------

Determina las escalas inicial y final de la partícula.

.. image:: /img/paranim12.gif

Color phases (Fases de color)
-----------------------------

Las partículas pueden usar hasta 4 fases de color. Cada fase de color
puede incluir transparencia.

Las fases deben proveer un valor offset del 0 a 1, y siempre en orden
ascendente. Por ejemplo, un color va a empezar con offset 0 y terminar
con offset 1, pero 4 colores pueden usar diferentes offsets, como
0, 0.2, 0.8 y 1.0 para las diferentes fases:

.. image:: /img/particlecolorphases.png

Resultara en:

.. image:: /img/paranim13.gif

Global parameters (Parámetros globales)
---------------------------------------

Estos parámetros afectan el comportamiento del sistema entero.

Lifetime (Tiempo de vida)
-------------------------

El tiempo en segundos que cada partícula estará viva. Cuando lifetime
llega a su fin, una nueva partícula es creada para reemplazarla.

Lifetime: 0.5

.. image:: /img/paranim14.gif

Lifetime: 4.0

.. image:: /img/paranim15.gif

Timescale (Escala de tiempo)
---------

Sucede a menudo que el efecto que se alcanza es perfecto, excepto que
es muy rápido o muy lento. Timescale ayuda ajustando la velocidad en
su conjunto.

Timescale everything 2x:

.. image:: /img/paranim16.gif

Preprocess (Pre procesamiento)
------------------------------

Los sistemas de partícula empiezan con 0 partículas emitidas, luego
empiezan a emitir. Esto puede ser un inconveniente cuando recién
cargas una escena y sistemas como antorchas, nieble, etc empiezan
a emitir en el momento que entras. Preprocess es usado para dejar
al sistema procesar una cantidad dada de segundos antes de que se
muestre por primera vez.

Emit timeout (Tiempo limite de emisión)
------------

Esta variable va a apagar la emision luego estar encendida una cantidad
dada de segundos. Si es cero, estará deshabilitada.

Offset
------

Permite mover el centro del emisor fuera del centro.

Half extents
------------

Hace el centro (por defecto 1 pixel) mas ancho, hasta el valor en
pixels deseado. Las partículas serán emitidas de forma aleatoria dentro
de esta area.

.. image:: /img/paranim17.gif

También es posible ajustar una mascara de emisión usando este valor.
Chequea el menu "Particles" en el viewport del editor de escena 2D y
selecciona tu textura favorita. Los pixels opacos serán usados como
potenciales lugares de emisión, mientras que los transparentes serán
ignorados.

.. image:: /img/paranim19.gif

Local space
-----------

Por defecto esta opción esta habilitada, y significa que el espacio
hacia el cual son emitidas las partículas esta contenido en el nodo.
Si el nodo es movido, todas las partículas se mueven con el:

.. image:: /img/paranim20.gif

Si se deshabilita, las partículas se emitirán a espacio global, lo que
implica que si el nodo es movido, el emisor se mueve también:

.. image:: /img/paranim21.gif

Explosiveness (Explosividad)
-------------

Si lifetime es 1 y hay 10 partículas, significa que cada partícula
será emitida cada .1 segundos. El parámetro explosiveness cambia esto,
y fuerza que las partículas sean emitidas todas juntas. Los rangos son:

-  0: Emite todas las partículas juntas.
-  1: Emite las partículas en intervalos iguales.

Los valores entre medio también son permitidos. Esta característica es
útil para crear explosiones o ráfagas repentinas de partículas:

.. image:: /img/paranim18.gif

Randomness (Aleatoriedad)
----------

Todos los parámetros físicos pueden ser aleatorizados. Las variables
aleatorias van de 0 a 1. La formula para volver aleatorio un parámetro
es:

::

    initial_value = param_value + param_value*randomness
