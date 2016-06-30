.. _doc_size_and_anchors:

Tamaño y anclas
---------------

Si un juego siempre se corre en el mismo dispositivo y a la misma
resolución, posicionar controles sería tan simple como ajustar la
posición y tamaño de cada uno de estos. Desafortunadamente, rara vez
es el caso.

Solo las TVs hoy en día tienen resolución y relación de aspecto
standard. Todo lo demás, desde monitores de computadores hasta tablets,
consolas portables y teléfonos móviles tienen diferente resolución y
relacion de aspecto.

Hay varias formas de manejar esto, pero por ahora vamos a imaginar que
la resolución de pantalla cambio y los controles necesitan ser
re-posicionados. Algunos necesitan seguir la parte inferior de la
pantalla, otros la superior, o tal vez los márgenes derecho o izquierdo.

.. image:: /img/anchors.png

Esto es hecho al editar la propiedad *margin* de los controles. Cada
control tiene cuatro márgenes: left(izq), right(der), bottom(inferior) y
top(superior). Por defecto todos representan una distancia en pixeles
relativa a la esquina superior-izquierda del control padre o (en caso
que no haya control padre) el viewport.

.. image:: /img/margin.png

Cuando las anclas horizontales (left, right) y/o vertical (top, bottom)
se cambian a END, los valores de márgenes se vuelven relativos a la
esquina inferior-derecha del control padre o viewport.

.. image:: /img/marginend.png

Aquí el control esta ajustado para expandir su esquina inferior-derecha
con la del padre, por lo que cuando se cambia de tamaño al padre, el
control siempre lo cubrirá, dejando un margen de 20 pixeles:

.. image:: /img/marginaround.png

Finalmente, también esta la opción de ratio (relación), donde 0 implica
izquierda, 1 derecha y cualquier valor entre medio es interpolado.
