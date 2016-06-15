.. _doc_splash_screen:

Pantalla de bienvenida (Splash Screen)
=============

Tutorial
--------

Este será un tutorial simple para cementar la idea básica de como el
subsistema GUI funciona. El objetivo será crear una pantalla de
bienvenida realmente simple y estática.

A continuación hay un archivo con los assets que serán usados. Estos
pueden ser agregados directamente a tu carpeta de proyecto, no hay
necesidad de importarlos.

:download:`robisplash_assets.zip </files/robisplash_assets.zip>`.

Configurando
------------

Fija la resolución de pantalla en 800x450 en la configuración de
proyecto, y prepara una nueva escena como esta:

.. image:: /img/robisplashscene.png

.. image:: /img/robisplashpreview.png

Los nodos "fondo" y "logo" son del tipo :ref:`TextureFrame <class_TextureFrame>`.
Estos tienen una propiedad especial para configurar la textura a
ser mostrada, solo carga el archivo correspondiente.

.. image:: /img/texframe.png

El nodo "start" es un :ref:`TextureButton <class_TextureButton>`,
que toma varias imágenes para diferentes estados, pero solo normal
y pressed (presionado) serán proporcionados en este ejemplo:

.. image:: /img/texbutton.png

Finalmente, el nodo "copyright" es una :ref:`Label <class_Label>`.
Las etiquetas (Labels) pueden ser configuradas con una fuente
personalizada editando la siguiente propiedad:

.. image:: /img/label.png

Como una nota aparte, la fuente fue importada de un TTF, ve :ref:`doc_importing_fonts`.
