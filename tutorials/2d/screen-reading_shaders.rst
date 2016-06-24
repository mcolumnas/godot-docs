.. _doc_screen-reading_shaders:

Shaders que leen la pantalla
============================

Introducción
~~~~~~~~~~~~

A menudo es deseable hacer un shader que lee de la misma pantalla en
la que esta escribiendo. Las APIs 3D como OpenGL o DirectX hacen esto
bastante difícil por limitaciones internas de hardware. Las GPUs son
extremadamente paralelas, por lo que leer y escribir causa todo tipo
de problemas de orden y coherencia de cache. Como resultado, ni el
hardware mas moderno soporta esto adecuadamente.

La solución es hacer una copia de la pantalla, o de una parte de la
pantalla, a un back-buffer y luego leer desde el mientras se dibuja.
Godot provee algunas herramientas para hacer este proceso fácil!

Instrucción shader TexScreen
~~~~~~~~~~~~~~~~~~~~~~~~~~~~

El lenguaje :ref:`doc_shading_language` tiene una instrucción especial,
"texscreen", toma como parámetros el UV de la pantalla y retorna un
vec3 RGB con el color. Una variable especial incorporada: SCREEN_UV
puede ser usada para obtener el UV del fragmento actual. Como resultado,
este simple shader de fragmento 2D:

::

    COLOR=vec4( texscreen(SCREEN_UV), 1.0 );

termina en un objeto invisible, porque solo muestra lo que esta atrás.
El mismo shader usando el editor visual luce así:

.. image:: /img/texscreen_visual_shader.png

Ejemplo TexScreen
~~~~~~~~~~~~~~~~~

La instrucción TexScreen puede ser usada para muchas cosas. Hay una demo
especial para *Screen Space Shaders*, que puedes descargar para verla
y aprender. Un ejemplo es un shader simple para ajustar brillo, contraste
y saturación:

::

    uniform float brightness = 1.0;
    uniform float contrast = 1.0;
    uniform float saturation = 1.0;

    vec3 c = texscreen(SCREEN_UV);

    c.rgb = mix(vec3(0.0), c.rgb, brightness);
    c.rgb = mix(vec3(0.5), c.rgb, contrast);
    c.rgb = mix(vec3(dot(vec3(1.0), c.rgb)*0.33333), c.rgb, saturation);

    COLOR.rgb = c;

Detrás de escena
~~~~~~~~~~~~~~~~

Mientras esto puede parecer mágico, no lo es. La instrucción Texscreen,
cuando se encuentra por primera vez en un nodo que esta por ser dibujado,
hace una copia de pantalla completa al back-buffer. Los nodos siguientes
que usen texscreen() dentro de shaders no tendrán la pantalla copiada
para ellos, porque esto seria muy ineficiente.

Como resultado, si se superponen shaders que usan texscreen(), el segundo
no usara el resultado del primero, resultando en imágenes no esperadas:

.. image:: /img/texscreen_demo1.png

En la imagen de arriba, la segunda esfera (arriba derecha) esta usando
el mismo origen para texscreen() que la primera debajo, por lo que la
primera "desaparece", o no es visible.

Para corregir esto, un nodo :ref:`BackBufferCopy <class_BackBufferCopy>`
puede ser instanciado entre ambas esferas. BackBufferCopy puede funcionar
tanto especificando una región de pantalla o la pantalla entera:

.. image:: /img/texscreen_bbc.png

Copiando adecuadamente el back-buffer, las dos esferas se mezclan
correctamente:

.. image:: /img/texscreen_demo2.png

Lógica de Back-buffer
~~~~~~~~~~~~~~~~~~~~~

Entonces, para dejarlo claro, aquí esta como la Lógica de copia de
backbuffer funciona en Godot:

-  Si un nodo usa texscreen(), la pantalla entera es copiada al back
   buffer antes de dibujar ese nodo. Esto solo sucede la primera vez,
   los siguientes nodos no lo dispararan.
-  Si un nodo BackBufferCopy fue procesado antes de la situación en el
   punto de arriba (aun si texscreen() no ha sido usado), el
   comportamiento descrito en el punto de arriba no sucede. En otras
   palabras, la copia automática de la pantalla entera solo sucede si
   texscreen() es usado en un nodo por primera vez y ningún nodo
   BackBufferCopy (no deshabilitado) fue encontrado en el orden del
   árbol.
-  BackBufferCopy puede copiar tanto la pantalla entera o una región.
   Si se ajusta solo a una región (no la pantalla entera) y tu shader
   usa pixels que no están en la región copiada, el resultado de esa
   lectura no esta definido (lo mas probable que sea basura de frames
   previos). En otras palabras, es posible usar BackBufferCopy para
   copiar de regreso una región de la pantalla y luego usar texscreen()
   en una región diferente. Evita este comportamiento!
