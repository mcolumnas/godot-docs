.. _doc_fixed_materials:

Fixed Materials (Materiales Fijos)
==================================

Introducción
------------

Fixed materials (originalmente Fixed Pipeline Materials) son el tipo
mas común de materiales, usando las opciones mas comunes de materiales
encontradas en DCCs 3D (Como Maya, 3DS Max, o Blender). La gran ventaja
de usarlos es que los artistas 3D están muy familiarizados con este
diseño. También permiten probar diferentes cosas rápidamente sin la
necesidad de escribir shaders. Fixed Materials heredan desde
:ref:`Material <class_Material>`, que también tiene varias opciones.
Si no lo has leido antes, es recomendable leer el tutorial
:ref:`doc_materials`.

Opciones
--------

Aquí hay una lista de todas las opciones disponibles para fixed materials:

.. image:: /img/fixed_materials.png

A partir de este punto, toda opción se explicara en detalle:

Flags fijas
-----------

Este es un conjunto de flags (banderas) que controlan aspectos generales
del material.

Use alpha
~~~~~~~~~

Este flag necesita estar activo para que materiales transparentes se
mezclen con lo que esta detrás, de otra forma siempre se desplegara
de forma opaca. No habilites este flag a no ser que el material
realmente lo necesite, porque puede afectar severamente la performance
y calidad. Los materiales con transparencia no proyectaran sombras
(a no ser que contengan áreas opacas y que el hint "opaque pre-pass"
este habilitado, ve el tutorial :ref:`doc_materials` para mas
información)

.. image:: /img/fixed_material_alpha.png

Use vertex colors
~~~~~~~~~~~~~~~~~

Pintar por vertex color es una técnica muy común de agregar detalle a
la geometría. Todos los DDCs 3D soportan esto, y puede que hasta soporten
baking occlusion. Godot permite que esta información sea usada en el
fixed material al modular el color diffuse cuando se habilita.

.. image:: /img/fixed_material_vcols.png

Point size
~~~~~~~~~~

Point size es usado para ajustar el tamaño del punto (en pixels) para
cuando se renderizan puntos. Esta característica es mas que nada usada
para herramientas y HUDs.

Discard alpha
~~~~~~~~~~~~~

Cuando alpha esta habilitado (ve arriba) los pixels invisibles son
mezclados con lo que hay detrás de ellos. En algunas combinaciones (de
usar alpha para renderizar profundidad) puede suceder que pixels
invisibles cubran otros objetos.

Si este es el caso, habilita esta opción para el material. Esta opción
es a menudo usada en combinación con el hint "opaque pre-pass" (ve el
tutorial :ref:`doc_materials` para mas información).

Parameteros
-----------

Diffuse, specular, emission y specular exponent
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

Esto son los colores base para un material.

-  Diffuse color es responsable por la iluminación que llega al material,
   y luego se difumina al rededor. Este color varia por el ángulo a la
   luz y la distancia (en el caso de luces spot y omni). Es el color que
   mejor representa al material. También puede tener alpha (transparencia)
-  Specular color es el color de la luz reflejada y es responsable de los
   brillos. Es afectada por el valor de specular exponent.
-  Emission es el color de la luz generada dentro del material (aunque
   no iluminara nada al rededor a no ser que se haga baking). Este color
   es constante.
-  Specular Exponent (o "Shininess"/"Intensity" en algunos DCCs 3D) es
   la forma en que la luz es reflejada. Si el valor es alto, la luz es
   completamente reflejada, de otra forma es difuminada mas y mas.

Abajo un ejemplo de como interactúan:

.. image:: /img/fixed_material_colors.png

Shader & shader param
~~~~~~~~~~~~~~~~~~~~~

Los materiales shader regulares permiten código personalizado de
iluminación. Los materiales fijos vienen con cuatro tipos predefinidos
de shader:

-  **Lambert**: La luz difusa estandard, donde la cantidad de luz es
   proporcional al ángulo del emisor de luz.
-  **Wrap**: Una variante de Lambert, donde el "coverage" de la luz
   puede ser cambiado. Esto es útil para muchos tipos de materiales
   como son madera, arcilla, pelo, etc.
-  **Velvet**: Este es similar a Lambert, pero agrega light scattering
   en los bordes. Es útil para cueros y algunos tipos de metales.
-  **Toon**: Sombreado estilo toon standard con un parámetro de cobertura.
   El componente especular también se vuelve toon-isado.

.. image:: /img/fixed_material_shader.png

Detail & detail mix
~~~~~~~~~~~~~~~~~~~

Detail es una segunda textura difusa la cual puede ser mezclada con la
primera (mas sobre texturas luego!). Detail blend y mix controlan como
estas son sumadas, aquí hay un ejemplo de lo que hacen las texturas
detail:

.. image:: /img/fixed_material_detail.png

Normal depth
~~~~~~~~~~~~

Normal depth controla la intensidad del normal-mapping así como la
dirección. En 1 (por defecto) normalmapping aplica normalmente, en
-1 el map es invertido y en 0 deshabilitado. Valores intermedios o
mayores son aceptados. Aquí esta como debería verse:

 .. image:: /img/fixed_material_normal_depth.png

Glow
~~~~

Este valor controla que cantidad de color es enviado al buffer glow.
Puede ser mayor a 1 para un efecto mas fuerte. Para que glow funcione,
debe existir un WorldEnvironment con Glow activado.

.. image:: /img/fixed_material_glow.png

Blend mode
~~~~~~~~~~

Los objetos son usualmente mezclados en modo Mix. Otros modos de mezcla
(Add y Sub) existen para casos especiales (usualmente efectos de partículas,
rayos de luz, etc.) pero los materiales pueden ser ajustados a ellos:

.. image:: /img/fixed_material_blend.png

Point size, line width
~~~~~~~~~~~~~~~~~~~~~~

Cuando dibujas puntos o líneas, el tamaño de ellos puede ser ajustado
aquí por material.

Texturas
--------

Casi todos los parámetros de arriba pueden tener una textura asignada
a ellos. Hay cuatro opciones de donde pueden obtener sus coordenadas
UV:

-  **UV Coordinates (UV Array)**: Este es el arreglo de coordenada
   regular UV que fue importado con el modelo.
-  **UV x UV XForm**: Coordenadas UV multiplicadas por la matriz
   UV Xform.
-  **UV2 Coordinates**: Algunos modelos importados pueden venir con
   un segundo grupo de coordenadas UV. Son comunes en texturas detail
  o para texturas baked light.
-  **Sphere**: Coordenadas esféricas (diferencia de la normal en el pixel
   por la normal de la cámara).

El valor de cada pixel de la textura es multiplicado por el parámetro
original. Esto implica que si una textura es cargada para diffuse, será
multiplicada por el color del parametro diffuse color. Lo mismo aplica
a todos los demás excepto por specular exponent, que es remplazada.
