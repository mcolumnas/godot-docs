.. _doc_instancing:

Instanciando
============

Fundamento
----------

Tener una escena y tirar nodos en ella puede funcionar para proyectos
pequeños, pero en la medida que el proyecto crece, mas y mas nodos son
usados y rapidamente se puede volver inmanejable. Para resolver esto,
Godot permite que un proyecto este separado en varias escenas. Esto,
sin embargo, no funciona de la misma forma que en otros motores de
juegos. De hecho, es bastante diferente, por lo que por favor no
saltees este tutorial!

Para resumir: Una escena es una coleccion de nodos organizados como
un arbol, donde solo pueden tener un nodo particular como nodo raiz.

.. image:: /img/tree.png

En Godot, una escena puede ser creada y salvada a disco. Se pueden
crear y guardar tantas escenas como se desee.

.. image:: /img/instancingpre.png

Luego, mientras editas una escena existente o creas una nueva, otras
escenas pueden ser instanciadas como parte de esta:

.. image:: /img/instancing.png

En la imagen anterior, la escena B fue agregada a la escena A como
una instancia. Puede parecer extraño al principio, pero al final de
este tutorial va a tener completo sentido!

Instanciando, paso a paso
------------------------

Para aprender como instanciar, comenzemos descargando un proyecto de
muestra: :download:`instancing.zip </files/instancing.zip>`.

Descomprime esta escena in el lugar de tu preferencia. Luego, agrega
esta escena al gestro de proyectos usando la opcion 'Importar':
Unzip this scene in any place of your preference. Then, add this scene to
the project manager using the 'Import' option:

.. image:: /img/importproject.png

Simply browse to inside the project location and open the "engine.cfg"
file. The new project will appear on the list of projects. Edit the
project by using the 'Edit' option.

This project contains two scenes "ball.scn" and "container.scn". The
ball scene is just a ball with physics, while container scene has a
nicely shaped collision, so balls can be thrown in there.

.. image:: /img/ballscene.png

.. image:: /img/contscene.png

Open the container scene, then select the root node:

.. image:: /img/controot.png

Afterwards, push the '+' shaped button, this is the instancing button!

.. image:: /img/continst.png

Select the ball scene (ball.scn), the ball should appear in the origin
(0,0), move it to around the center

of the scene, like this:

.. image:: /img/continstanced.png

Press Play and Voila!

.. image:: /img/playinst.png

The instanced ball fell to the bottom of the pit.

A little more
-------------

There can be as many instances as desired in a scene, just try
instancing more balls, or duplicating them (ctrl-D or duplicate button):

.. image:: /img/instmany.png

Then try running the scene again:

.. image:: /img/instmanyrun.png

Cool, huh? This is how instancing works.

Editing instances
-----------------

Select one of the many copies of the balls and go to the property
editor. Let's make it bounce a lot more, so look for the bounce
parameter and set it to 1.0:

.. image:: /img/instedit.png

The next it will happen is that a green "revert" button appears. When
this button is present, it means we modified a property from the
instanced scene to override for a specific value in this instance. Even
if that property is modified in the original scene, the custom value
will always overwrite it. Pressing the revert button will restore the
property to the original value that came from the scene.

Conclusion
----------

Instancing seems handy, but there is more to it than it meets the eye!
The next part of the instancing tutorial should cover the rest..
