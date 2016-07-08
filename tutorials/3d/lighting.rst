.. _doc_lighting:

Iluminación
===========

Introducción
------------

Las luces emiten luz que se mezcla con los materiales y produce un
resultado visible. La luz puede venir de diferentes tipos de orígenes
en una escena:

-  Desde el propio Material, en la forma de emission color (aunque no
   afecta objetos cercanos, a no ser que sea baked).
-  Nodos de luces: Directional, Omni y Spot.
-  Luz de ambiente en la clase :ref:`Environment <class_Environment>`.
-  Baked Light (lee :ref:`doc_light_baking`).

La emisión de color es una propiedad del material, como se vio en los
tutoriales previos sobre materiales (ve a leeros si aún no lo has hecho!).

Nodos de Luz
------------

Como mencionamos antes, hay tres tipos de nodos de luz: Direccional,
Ambiente y Spot. Cada una tiene diferentes usos y será descrita en
detalle más abajo, pero primero tomemos un vistazo en los parámetros
comunes para luces:

.. image:: /img/light_params.png

Cada una tiene una funcion especifica:

-  **Enabled**: Las luces pueden deshabilitarse en cualquier momento.
-  **Bake Mode**: Cuando se usa el light baker, el rol de esta luz puede
   ser definido en este enumerador. El rol sera respetado aun si la luz
   esta deshabilitada, lo que permite configurar una luz y luego
   deshabilitarla para baking.
-  **Energy**: Este valor es un múltiplo para la luz, es especialmente
   útil para :ref:`doc_high_dynamic_range` y para luces Spot y Omni,
   porque puede crear spots muy brillantes cerca del emisor.
-  **Diffuse and Specular**: Estos valores de luz son multiplicados por
   la luz del material y los colores difusos, por lo que un valor blanco
   no significa que la luz será toda blanca, pero que el color original
   será mantenido.
-  **Operator**: Es posible hacer algunas luces negativas para un efecto
   de oscurecimiento.
-  **Projector**: Las luces pueden proyectar una textura para la luz
   difusa (actualmente solo soportado para luces Spot).

Directional light
~~~~~~~~~~~~~~~~~

Este es el tipo más común de luz y representa al sol. También es la luz
más barata de computar y debe ser usada siempre que sea posible (aunque
no es el shadow-map mas barato de computar, más sobre ello luego).
Los nodos de luces direccionales están representados por una flecha
grande, que representa la dirección de la luz, sin embargo la posición
del nodo no afecta la luz para nada, y puede estar en cualquier lugar.

.. image:: /img/light_directional.png

Básicamente lo que mira hacia la luz es iluminado, lo que no es oscuro.
La mayoría de las luces tienen parámetros específicos pero las luces
direccionales son bastante simples por naturaleza por lo que no tienen.

Omni light
~~~~~~~~~~

Las luces Omni son un punto que tira luz a su alrededor hasta un cierto
radio (distancia) que puede ser controlado por el usuario. La luz se
atenúa con la distancia y llega a 0 en el borde. Representa lámparas o
cualquier otra fuente de luz que viene de un punto.

.. image:: /img/light_omni.png

La curva de atenuación para este tipo de luces por naturaleza es
computada con una función cuadrática inversa que nunca llega a cero
y tiene valores infinitamente grandes cerca del emisor.

Esto las vuelve considerablemente inconveniente para que los artistas
las ajusten, así que Godot las simula con una curva exponencial
controlada por el artista.

.. image:: /img/light_attenuation.png

Spot Light
~~~~~~~~~~

Las luces Spot son similares a las luces Omni, excepto que solo operan
entre un ángulo dado (o "cutoff"). Son útiles para simular linternas,
luces de autos, etc. Este tipo de luz también es atenuada hacia la
dirección opuesta de donde apunta.

.. image:: /img/light_spot.png

Ambient light
-------------

La luz de ambiente puede ser encontrada en las propiedades de un
WorldEnvironment (recuerda solo una de ellas puede ser instanciada
por escena). Ambient light consiste de luz y energía uniformes.
Esta luz es aplicada por igual a cada pixel de la escena renderizada,
excepto los objetos que usan baked light.

Baked light
-----------

Baked light significa luz de ambiente pre-computada. Puede servir
muchos propósitos, como hacer baking de emisores de luz que no serán
usados en tiempo real, y backing de rebotes de luces de tiempo real
para agregar más realismo a la escena (ve el tutorial
:ref:`doc_light_baking` para mas información).
