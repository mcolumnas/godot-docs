.. _doc_instancing:

Instanciando
============

Fundamento
----------

Tener una escena y tirar nodos en ella puede funcionar para proyectos
pequeños, pero en la medida que el proyecto crece, mas y mas nodos son
usados y rápidamente se puede volver inmanejable. Para resolver esto,
Godot permite que un proyecto este separado en varias escenas. Esto,
sin embargo, no funciona de la misma forma que en otros motores de
juegos. De hecho, es bastante diferente, por lo que por favor no
saltees este tutorial!

Para resumir: Una escena es una colección de nodos organizados como
un árbol, donde solo pueden tener un nodo particular como nodo raíz.

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
-------------------------

Para aprender como instanciar, comencemos descargando un proyecto de
muestra: :download:`instancing.zip </files/instancing.zip>`.

Descomprime esta escena in el lugar de tu preferencia. Luego, agrega
esta escena al gestor de proyectos usando la opción 'Importar':

.. image:: /img/importproject.png

Simplemente navega hasta el lugar donde esta el proyecto y abre
"engine.cfg". El nuevo proyecto aparecerá en la lista de proyectos.
Edita el proyecto usando la opción 'Editar'.

Este proyecto contiene dos escenas "ball.scn"(pelota) y
"container.scn"(contenedor). La escena ball es solo una
pelota con fisica, mientras que la escena container tiene una
linda forma de colisión, de forma que las pelotas pueden tirarse
allí.

.. image:: /img/ballscene.png

.. image:: /img/contscene.png

Abre la escena container, luego selecciona el nodo raíz:

.. image:: /img/controot.png

Después, presiona el botón con forma de cadena, este es el botón de
instanciamiento!

.. image:: /img/continst.png

Selecciona la escena de la pelota (ball.scn), la pelota debería
aparecer en el origen (0,0), la mueves hasta el centro de la escena,
algo así:

.. image:: /img/continstanced.png

Press Play and Voila!

.. image:: /img/playinst.png

La pelota instanciada cayo hasta el fondo del pozo.

Un poco mas
-----------

Puede haber tantas instancias como se desee en una escena,
simplemente intenta instancias mas pelotas, o duplícalas (Ctrl-D
o botón derecho -> Duplicar):

.. image:: /img/instmany.png

Luego intenta correr la escena nuevamente:

.. image:: /img/instmanyrun.png

Esta bueno, eh? Asi es como funciona el instanciamiento.

Editando instancias
-------------------

Selecciona una de las muchas copias de las pelotas y ve al Inspector.
Hagamos que rebote mucho mas, por lo que busca el parámetro
bounce(rebote) y configuralo en 1.0:

.. image:: /img/instedit.png

Lo próximo que sucederá es que un botón de "revertir" con forma de
"flecha en circulo" aparecerá. Cuando este botón esta presente,
significa que hemos modificado una propiedad en la escena
instanciada, ignorando el valor original. Aun si esa propiedad es
modificada en la escena original, el valor personalizado siempre lo
sobreescribirá. Tocando el botón de revertir restaurara la propiedad
al valor original que vino de la escena.

Conclusión
----------

Instanciar parece útil, pero hay mas de lo que se ve a simple vista!
La próxima parte del tutorial de instanciamiento cubrirá el resto..
