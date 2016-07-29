.. _doc_managing_image_files:

Administrando archivos de imagen
================================

Si haz leído los tutoriales previos en :ref:`doc_resources`y
:ref:`doc_filesystem`, en este punto tu sabes que las imágenes regulares
(.png, .jpg, etc.) son tratadas como recursos regulares en Godot.

A diferencia de los recursos de texturas (.tex files), los archivos de
imagen no contienen información extra de mosaico (repetición de
texturas), mipmaps o filtrado. Editar esta información y guardar la
textura de nuevo no tendrá ningún efecto, ya que dichos formatos no
pueden contener esa información.

Cargador de imágenes
--------------------

La carga de imágenes es hecha por el "image loader". El comportamiento
del cargador de imágenes para todos los archivos de imagenes puede ser
cambiado en el dialogo de Configuración de Proyecto (Escena ->
Configuración de proyecto), Hay una sección con valores que son usados
para todos los recursos de imágenes:

.. image:: /img/imgloader.png

Opciones del cargador de imágenes
---------------------------------

Filter (Filtro)
~~~~~~~~~~~~~~~

Filter es usado cuando la imagen es estirada más que tu tamaño original,
por lo que un texel en la imagen es más grande que el pixel en
la pantalla. Apagarlo genera un efecto estilo retro:

.. image:: /img/imagefilter.png

Repeat (Repetir)
~~~~~~

Repeat es usado principalmente para texturas 3D, por lo que está apagado
por defecto (las texturas son importadas con la escena y usualmente no
están en el proyecto como archivos). Cuando se usan coordenadas UV (algo
que no es común en 2D), y el valor UV va más allá de 0,0,1,1 rect, la
textura se repite en lugar de restringirse al borde.

Mipmaps
~~~~~~~

Cuando la opción mipmaps es habilitada, Godot va a generar mipmaps.
Mipmaps son versiones de una imagen encogidas a la mitad en ambos ejes,
recursivamente, hasta que la imagen tiene un tamaño de 1 pixel. Cuando
el hardware 3D necesita encoger la imagen, encuentra el mipmap más grande
desde el cual puede escalar, y escala desde allí. Esto mejora el
rendimiento y la calidad de imagen.

.. image:: /img/mipmaps.png

Cuando los mipmaps son deshabilitados, las imágenes empiezan a
distorsionarse mucho cuando se encojen excesivamente:

.. image:: /img/imagemipmap.png

Alpha blending (Mezcla Alpha)
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

La `ecuación de mezcla <http://en.wikipedia.org/wiki/Alpha_compositing>`__
usada por aplicaciones como Photoshop es demasiado compleja para tiempo
real. Hay aproximaciones mejores como `alpha pre-multiplicado
<http://blogs.msdn.com/b/shawnhar/archive/2009/11/06/premultiplied-alpha.aspx?Redirected=true>`__,
pero imponen más estrés en el conducto de assets. Al final, terminamos
con texturas que tienen artefactos en los bordes, porque las aplicaciones
como Photoshop guardan los pixels blancos en áreas completamente
transparentes. Esos pixels blancos se terminan mostrando gracias al
filtro de textura (cuando está activo).

Godot tiene una opción para arreglar los bordes de una imagen (al pintar
pixels invisibles con el mismo color de sus vecinos visibles).

.. image:: /img/fixedborder.png

Para hacer esto, abre la imagen desde la pestaña recursos, o edítala desde
las propiedades de editor de otro nodo o recurso, luego ve a las opciones
del objeto y selecciona "Fix Alpha Edges", y guardalo.

.. image:: /img/imagefixalpha.png

Debido a que arreglar esto en tantas imágenes puede ser algo molesto,
tanto Texture Import y Image Export pueden también hacer esta operación.

Importando texturas
~~~~~~~~~~~~~~~~~~~

A veces, puede desearse cambiar los ajustes de arriba por imagen.
Desafortunadamente, los ajustes de image loader son globales. Los flags
de textura no pueden ser guardados en un archivo regular .png o .jpg.

Para esos casos, la imagen puede ser importada como una textura (.tex),
donde los falgs individuales pueden ser cambiados. Godot también mantiene
el seguimiento del archivo original y lo re-importara si cambia.

Importar también permite la conversión a otros formatos (WebP, o
compresión RAM) la cual puede ser usada en algunos casos. Más información
en la pagina :ref:`doc_importing_textures`.

Exportando texturas
~~~~~~~~~~~~~~~~~~~

También es posible convertir imágenes a otros formatos (WebP o compresión
RAM) al exportar, así como instruir al exportador para crear un Atlas
para un conjunto de imágenes. También es posible pedir al exportador
que escale todas las imágenes (o grupos seleccionados).

Mas información en la paginae :ref:`doc_exporting_images`.
