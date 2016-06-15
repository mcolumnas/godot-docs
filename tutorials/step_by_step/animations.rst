.. _doc_animations:

Animaciones
===========

Introducción
------------

Este tutorial explicara como todo es animado en Godot. El sistema de
animación de Godot es extremadamente poderoso y flexible.

Para empezar, vamos a usar la escena del tutorial previo (:ref:`doc_splash_screen`).
El objetivo es agregarle una animación simple. Aquí hay una copia
del tutorial por las dudas: :download:`robisplash.zip </files/robisplash.zip>`.

Creando la animación
--------------------

Primero que nada, agrega un nodo :ref:`AnimationPlayer <class_AnimationPlayer>`
a la escena como hijo del fondo (en nodo raiz):

.. image:: /img/animplayer.png

Cuando un nodo de este tipo es seleccionado, el panel de edición de
animaciones aparecerá:

.. image:: /img/animpanel.png

Asique, es tiempo de crear una nueva animación! Presiona el botón Nueva
Animación y nómbrala "intro".

.. image:: /img/animnew.png

Luego de que la animación es creada, es tiempo de editarla.

Editando la animación
---------------------

Ahora es cuando la magia sucede! Varias cosas suceden cuando se
edita una animación, la primera es la aparicion del panel de edición
de animación.

.. image:: /img/animeditor.png

Pero la segunda, y mas importante, es que el Inspector entra en modo
"animación". En este modo, un icono de llave aparece al lado de cada
propiedad en el Inspector. Esto significa que, en Godot, *cualquier
propiedad de cualquier objeto* puede ser animada.

.. image:: /img/propertykeys.png

Haciendo que el logo aparezca
-----------------------------

A continuación, haremos aparecer el logo desde la parte superior de
la pantalla. Luego de seleccionar el reproductor de animaciones, el
panel de edición se mantendrá visible hasta que sea manualmente
escondido. Para tomar ventaja de esto, seleccióna el nodo "logo" y
ve a la propiedad "pos" en el Inspector, muévela arriba, a la
posición: 114, -400.

Una vez en esta posición, presiona el botón de llave al lado de la
propiedad:

.. image:: /img/keypress.png

Como es un nuevo track (pista), un dialogo aparecerá preguntando
para crearla. Confirma!

.. image:: /img/addtrack.png

Asi el keyframe(fotograma clave) sera agregado en el editor
de animación:

.. image:: /img/keyadded.png

En segundo lugar, mueve el cursor del editor hasta el final, haciendo
click aquí:

.. image:: /img/move_cursor.png

Cambia la posición del logo a 114,0 en el Inspector y agrega otro
keyframe (haciendo click en la llave). Con dos keyframes, la animación
sucede.

.. image:: /img/animation.png

Pulsando Play en el panel de animación hará que el logo descienda.
Para probarlo al correr la escena, el botón autoplay puede marcar
la animación para que empiece automáticamente cuando la escena
comienza:

.. image:: /img/autoplay.png

Y finalmente, cuando corras la escena, la animación debería verse
de esta forma:

.. image:: /img/out.gif
