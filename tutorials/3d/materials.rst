.. _doc_materials:

Materiales
=========

Introducción
------------

Los materiales pueden ser aplicados a la mayoría de los objetos 3D, son
básicamente una descripción de como la luz reacciona con ese objeto. Hay
muchos tipos de materiales, pero los principales son
:ref:`FixedMaterial <class_FixedMaterial>` y :ref:`ShaderMaterial <class_ShaderMaterial>`.
Existen tutoriales para cada uno de ellos:
:ref:`doc_fixed_materials` and :ref:`doc_shader_materials`.

Este tutorial es sobre las propiedades básicas compartidas entre ellos.

.. image:: /img/material_flags.png

Flags (banderas)
----------------

Los materiales, no importa del tipo que sean, tienen un grupo de flags
asociados. Cada una tiene un uso diferente y será explicado a
continuacián.

Visible
~~~~~~~

Alterna si el material es visible. Si no esta marcado, el objeto no
será mostrado.

Double sided & invert faces
~~~~~~~~~~~~~~~~~~~~~~~~~~~

Godot por defecto solo muestra las caras de la geometría (triángulos)
cuando están frente a la cámara. Para hacer esto necesita que estén en
un orden de agujas de reloj. Esto ahorra un montón de trabajo a la
GPU al asegurarse que los triángulos no visibles no sean dibujados.

Algunos objetos planos pueden necesitar ser dibujados todo el tiempo
sin embargo, para esto el flag "double sided" se asegurara que no importa
hacia donde este mirando, el triángulo siempre será dibujado. También
es posible invertir este chequeo y dibujar caras en un orden contrario
a las agujas de reloj, aunque no es muy útil excepto para algunos casos
(como dibujar outlines).

Unshaded
~~~~~~~~

Los objetos siempre son negros a no ser que la luz los afecta, y el
shading (sombreado) cambia de acuerdo al tipo y dirección de las luces.
Cuando este flag es prendido, el color diffuse (difuso) es mostrado
tal cual aparece en la textura o parámetro.

.. image:: /img/material_unshaded.png

On top
~~~~~~

Cuando este flag esta encendido, el objeto será dibujado luego que todo
lo demás ha sido dibujado y sin una prueba de profundidad. Esto en general
solo es útil para efectos de HUD o gizmos.

Ligthmap on UV2
~~~~~~~~~~~~~~~

Cuando usas lightmapping (ve el tutorial :ref:`doc_light_baking`), esta
opción determina que el lightmap debería ser accedido en el arreglo UV2
en lugar del UV regular.

Parámetros
----------

Algunos párametros también existen para controlar el dibujado y mezclado:

Blend mode
~~~~~~~~~~

Los objetos son usualmente mezclados en modo Mix. Otros modos de blend
(Add and Sub) existen para casos especiales (usualmente efectos de
partículas, rayos de luz, etc.) pero se pueden ajustar los materiales a
ellos:

.. image:: /img/fixed_material_blend.png

Line width
~~~~~~~~~~

Cuando dibujas líneas, el tamaño de ellas puede ser ajustado aquí para
cada material.

Depth draw mode
~~~~~~~~~~~~~~~

Este es un ajuste difícil pero muy útil. Por defecto, los objetos opacos
son dibujados usando el depth buffer y los objetos translucientes no
(pero son ordenados por profundidad). Este comportamiento puede ser
cambiado aquí. Las opciones son:

-  **Always**: Dibuja objetos con profundidad siempre, aun aquellos con
   alpha. Esto a menudo resulta en glitches como el de la primer
   imagen (motivo por el cual no es el valor por defecto)

-  **Opaque Only**: Dibuja objetos con profundidad solo cuando son opacos,
   y no ajusta la profundidad para alpha. Este es el valor por defecto
   porque es rápido, pero no es el ajuste mas correcto. Los objetos con
   transparencia que se auto-intersectan siempre lucirán mal,
   especialmente aquellos que mezclan áreas opacas y transparentes, como
   pasto, hojas de árboles, etc. Los objetos con transparencia tampoco
   pueden emitir sombras, esto es evidente en la segunda imagen.

-  **Alpha Pre-Pass**: Lo mismo que el anterior, pero una pasada de
   profundidad es realizada para las áreas opacas de los objetos con
   transparencia. Esto hace que los objetos con transparencia luzcan
   mucho mejor. En la tercer imagen es evidente como las hojas emiten
   sombras entre ellas y hacia el piso. Este ajuste es apagado por
   defecto ya que, mientras en PC no es muy costoso, los dispositivos
   móviles sufren un montón cuando es habilitado, así que úsalo con
   cuidado.

-  **Never**: Nunca usar el buffer de profundidad para este material.
   Esto es mas útil en combinación con la bandera "On Top" explicada
   mas arriba.

 .. image:: /img/material_depth_draw.png
