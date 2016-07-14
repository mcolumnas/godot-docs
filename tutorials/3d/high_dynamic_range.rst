.. _doc_high_dynamic_range:

High dynamic range
==================

Introducción
------------

Normalmente, un artista hace todo el modelado 3d, luego las texturas,
mira su modelo que luce increíblemente bien en el DCC 3D (Maya, Blender,
etc) y dice "luce fantástico, listo para la integración!" luego va al
juego, se ajustan las luces y el juego corre.

Así que de donde viene todo este tema de HDR? La idea es que en lugar
de tratar con colores que van del negro al blanco (0 a 1), usamos
colores más blancos que el blanco (por ejemplo, 0 a 8 veces blanco).

Para ser más practico, imagina que en una escena regular, la intensidad
de una luz (generalmente 1.0) se ajusta a 5.0. La escena completa se
volverá muy brillosa (hacia el blanco) y lucirá horrible.

Luego de esto el luminance de la escena es computado al promediar el
luminance de cada pixel que la conforma, y este valor es usado para traer
la escena de vuelta a rangos normales. Esta última operación es llamada
tone-mapping. Al final, estamos en un lugar similar a donde empezamos:

.. image:: /img/hdr_tonemap.png

Excepto que la escena tiene más contraste, porque hay un mayor rango de
iluminación en juego. Para que sirve esto? La idea es que el luminance
de la escena va a cambiar mientras te mueves a través del mundo,
permitiendo que sucedan situaciones como esta:

.. image:: /img/hdr_cave.png

Además, es posible ajustar un valor threshold para enviar al glow buffer
dependiendo en el luminance del pixel. Esto permite efectos de light
bleeding más realistas en la escena.

Espacio de color lineal
-----------------------

El problema con esta técnica es que los monitores de computadora aplican
una curva gamma para adaptarse mejor a la forma que ve el ojo humano.
Los artistas crean su arte en la pantalla también, así que su arte tiene
una curva gamma implícita aplicada.

El espacio de color donde las imágenes creadas en computadora existen es
llamada "sRGB". Todo el contenido visual que la gente tiene en sus
computadoras o descarga de Internet (como imagenes, peliculas, etc.)
esta en este colorspace.

.. image:: /img/hdr_gamma.png

La matemática de HDR requiere que multipliquemos la escena por diferentes
valores para ajustar el luminance y exposure a diferentes rangos de luz,
y esta curva molesta ya que necesitamos colores en espacio linear para
esto.

Espacio de color lineal & asset pipeling
----------------------------------------

Trabajar en HDR no es solo presionar un interruptor. Primero, los assets
de imágenes importados deben convertirse a espacio linear al importarse.
Hay dos formas de hacer esto:

SRGB -> linear conversion al importar imagen
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

Esta es la forma más compatible de usar assets de espacio-linear y
funcionará en todos lados incluyendo todos los dispositivos moviles.
El principal tema con esto es la pérdida de calidad, ya que sRGB existe
para evitar este mismo problema. Usando 8 bits por canal para representar
colores lineales es ineficiente desde el punto de vista del ojo humano.
Estas texturas pueden comprimirse más tarde también, lo que vuelve al
problema peor.

Pero en cualquier caso, esta es la solución fácil que funciona en todos
lados.

sRGB por Hardware -> linear conversion
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

Esta es la forma mas correcta de usar assets en espacio-linear, ya que
el sampler de texturas en la GPU hará la conversión luego de leer el
texel usando punto flotante. Esto funciona bien en PC y consolas, pero
la mayoría de los dispositivos móviles no lo soporta, o no lo soportan
en formato de textura comprimida (iOS por ejemplo).

Linear -> sRGB al final
~~~~~~~~~~~~~~~~~~~~~~~

Luego de que todo el renderizado está hecho, la imagen de espacio-linear
renderizada debe ser convertida de nuevo a sRGB. Para hacer esto, solo
habilita sRGB conversión en el :ref:`Environment <class_Environment>` actual
(mas sobre esto abajo).

Maten en mente que las conversiones sRGB [STRIKEOUT:> Linear and Linear]> sRGB
deben siempre estar **ambas** habilitadas. Fallar al habilitar una de
ellas resultara en visuales horribles adecuadas solo para juegos indie
avant garde experimentales.

Parámetros de HDR
-----------------

HDR se encuentra en el recurso :ref:`Environment <class_Environment>`.
Estos se encuentran la mayoría de las veces dentro de un nodo
:ref:`WorldEnvironment <class_WorldEnvironment>`, o ajustado en una
cámara. Hay muchos parámetros para HDR:

.. image:: /img/hdr_parameters.png

ToneMapper
~~~~~~~~~~

El ToneMapper es el corazón del algoritmo. Muchas opciones para
tonemappers son proveídas:

-  Linear: El tonemapper más simple. Hace su trabajo para ajustar el
   brillo de la escena, pero si la diferencia en luz es muy grande,
   causara que los colores estén muy saturados.
-  Log: Similar a linear, pero no tan extremo.
-  Reinhardt: Tonemapper clásico (modificado para que no desature
   demasiado)
-  ReinhardtAutoWhite: Igual que el anterior, peor usa el luminance
   de escena máximo para ajustar el valor blanco.

Exposure
~~~~~~~~

El mismo parámetro de exposure que en cámaras reales. Controla cuanta
luz entra a la cámara. Valores altos resultara en una escena mas
brillante mientras valores bajos resultara en una escena más oscura.

White
~~~~~

Valor máximo de blanco.

Glow threshold
~~~~~~~~~~~~~~

Determina luego de que valor (desde 0 a 1 luego que a la escena se le
hace tonemapped), la luz empezara a hacer bleeding.

Glow scale
~~~~~~~~~~

Determina cuanta luz hará bleeding.

Min luminance
~~~~~~~~~~~~~

Valor más bajo de luz para la escena en la cual el tonemapper dejara
de trabajar. Esto permite que escenas oscuras permanezcan oscuras.

Max luminance
~~~~~~~~~~~~~

Valor más alto de luz para la escena en la cual el tonemapper dejara
de trabajar. Esto permite que las escenas brillantes se mantengan
saturadas.

Exposure adjustment speed
~~~~~~~~~~~~~~~~~~~~~~~~~

Auto-exposure cambiara lentamente y llevara un rato ajustarlo (como en
las cámaras reales). Valores más altos significan ajustes más rápidos.
