.. _doc_gui_skinning:

GUI skinning
============

Oh hermosa GUI!
---------------

Este tutorial es sobre skinning avanzado de una interfaz de usuario.
La mayoría de los juegos no necesitan esto, ya que terminan usando
:ref:`Label <class_Label>`, :ref:`TextureFrame <class_TextureFrame>`,
:ref:`TextureButton <class_TextureButton>` y
:ref:`TextureProgress <class_TextureProgress>`.

Sin embargo, muchos estilos de juegos a menudo necesitan interfaces de
usuario complejas, como MMOs, RPGs tradicionales, simuladores,
estrategia, etc. Este tipo de interfaces también son comunes en algunos
juegos que incluyen editores para crear contenido, o interfaces para
conectividad de red.

La interfaz de usuario de Godot usa este tipo de controles con el tema
por defecto, pero pueden ser skinned para lucir como cualquier otra
interfaz de usuario.

Tema
-----

La GUI esta skinned con el recurso :ref:`Theme <class_Theme>`.
Los temas contienen toda la información requerida para cambiar el estilo
visual de todos los controles. Las opciones de tema son por nombre, por
lo que no es obvio que nombre cambia que cosa (especialmente desde
código), pero varias herramientas se proveen. El lugar final para fijarse
que opción de tema es para que control, y que siempre estará mas
actualizado que cualquier documentación es el archivo
`scene/resources/default_theme/default_theme.cpp
<https://github.com/godotengine/godot/blob/master/scene/resources/default_theme/default_theme.cpp>`__.
El resto de este documento explicara las diferentes herramientas usadas
para personalizar el tema.

Un Tema puede ser aplicado a cualquier control en la escena. Como
resultado, todos sus controles hijos y nietos usaran el mismo tema
también (a no ser que otro tema sea especificado mas abajo en el árbol).
Si un valor no se encuentra en el tema, será buscado en los temas mas
arriba en la jerarquía hacia la raíz. Si no se encuentra nada, el tema
por defecto es usado. Este sistema permite una sobrescritura flexible de
los temas en interfaces de usuario complejas.

Opciones de tema
----------------

Cada tipo de opción en un tema puede ser:

-  **Un entero constante**: Una única constante numérica. Generalmente
   usada para definir el espaciado entre componentes o alineación.
-  **Un Color**: Un único color, con o sin transparencia. Los colores
   usualmente se aplican a fuentes e iconos.
-  **Una textura**: Una única imagen. Las texturas no se usan a menudo,
   pero cuando lo son representan manijas para recoger o iconos en un
   control complejo (como un dialogo de archivo).
-  **Una Fuente**: Cada control que usa texto puede tener asignada la
   fuente usada para dibujar cadenas.
-  **Un StyleBox**: StyleBox es un recurso que defino como dibujar un
   panel en diferentes tamaños (mas información sobre ellos luego).

Toda opción esta asociada a:

-  Un nombre (el nombre de la opción)
-  Un control (el nombre del control)

Un ejemplo de uso:

::

    var t = Theme.new()
    t.set_color("font_color","Label",Color(1.0,1.0,1.0))

    var l = Label.new()
    l.set_theme(t)

En el ejemplo de arriba, un nuevo tema es creado. La opcion "font_color"
es cambiada y luego aplicada a la etiqueta(label). Como resultado,
la etiqueta (y todas las etiquetas hijas y nietas) usaran ese color.

Es posible sobrescribir esas opciones sin usar el tema directamente y
solo para un control especifico al usar la API de sobrescritura en
:ref:`Control.add_color_override() <class_Control_add_color_override>`:

::

    var l = Label.new()
    l.add_color_override("font_color",Color(1.0,1.0,1.0))


En la ayuda integrada de Godot (en la pestaña script) tu puedes chequear
que opciones de tema que pueden ser sobrescritas, o chequea la referencia
de clase :ref:`Control <class_Control>`.

Personalizando un control
-------------------------

Si solo unos pocos controles necesitan ser skinned, suele no ser necesario
crear un nuevo tema. Los controles ofrecen sus propias opciones de tema
como propiedades especiales. Si son marcadas, serán sobrescritos:

.. image:: /img/themecheck.png

Como puede verse en la imagen de arriba, las opciones de tema tienen
pequeñas check-boxes. Si están marcadas, pueden ser usadas para
sobrescribir el valor del tema solo para ese control.

Creando un tema
---------------

La forma mas simple de crear un tema es editar un recurso theme.
Crea un nuevo recurso en el Inspector y selecciona Theme, el editor
aparecerá inmediatamente. Luego, guárdalo en el Inspector con el icono
de disquette (en, por ejemplo, mytheme.thm):

.. image:: /img/themecheck.png

Esto creara un tema vacío que luego puede ser cargado y asignado a
los controles.

Ejemplo: aplicando tema a un botón
----------------------------------

Toma algunos assets (:download:`skin_assets.zip </files/skin_assets.zip>`),
ve al menú "theme" y selecciona "Add Class Item":

.. image:: /img/themeci.png

Un menú aparecerá para elegir el tipo de control a crear. Selecciona
"Button":

.. image:: /img/themeci2.png

Inmediatamente, todas las opciones de tema para botones aparecerán en las
propiedades del editor, donde pueden ser editadas:

.. image:: /img/themeci3.png

Selecciona el stylebox "normal" y crea un nuevo "StyleBoxTexture", luego
edítalo. Una textura stylebox básicamente contiene una textura y el
tamaño de los márgenes que no se estiraran cuando la textura sea
estirada. Esto se llama stretching "3x3":

.. image:: /img/sb1.png

Repite los pasos y agrega otros assets. No hay imagen para los estados
hover o deshabilitado en los archivos de ejemplo, por lo que usa el mismo
stylebox que normal. Ajusta la fuente proporcionada como la fuente del
botón y cambia el color de la fuente a negro. Pronto, tu botón lucirá
diferente y retro:

.. image:: /img/sb2.png

Guarda este tema al archivo .thm. Ve al editor 2D y crea algunos botones:

.. image:: /img/skinbuttons1.png

Ahora, ve al nodo raíz de la escena y encuentra la propiedad "tema",
reemplázala por el tema que recién creamos. Debería lucir así:

.. image:: /img/skinbuttons2.png

Felicitaciones! Has creado un tema GUI reusable!
