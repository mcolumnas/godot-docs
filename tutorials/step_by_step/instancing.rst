.. _doc_instancing:

Instanciar
============

Fundamento
----------

Tener una escena y tirar nodos en ella puede funcionar para proyectos
pequeños, pero en la medida que el proyecto crece, más y más nodos son
usados y rápidamente se puede volver inmanejable. Para resolver esto,
Godot permite que un proyecto este separado en varias escenas. Esto,
sin embargo, no funciona de la misma forma que en otros motores de
juegos. De hecho, es bastante diferente, por lo que por favor no
saltes este tutorial!

Para resumir: Una escena es una colección de nodos organizados como
un árbol, donde soló pueden tener un nodo particular como nodo raíz.

.. image:: /img/tree.png

En Godot, una escena puede ser creada y salvada a disco. Se pueden
crear y guardar tantas escenas como se desee.

.. image:: /img/instancingpre.png

Luego, mientras editas una escena existente o creas una nueva, otras
escenas pueden ser instanciadas como parte de está:

.. image:: /img/instancing.png

En la imagen anterior, la escena B fue agregada a la escena A como
una instancia. Puede parecer extraño al principio, pero al final de
este tutorial va a tener completo sentido!

Instanciar, paso a paso
-------------------------

Para aprender como instanciar, comencemos descargando un proyecto de
muestra: :download:`instancing.zip </files/instancing.zip>`.

Descomprime esta escena en el lugar de tu preferencia. Luego, agrega
esta escena al gestor de proyectos usando la opción 'Importar':

.. image:: /img/importproject.png

Simplemente navega hasta el lugar donde está el proyecto y abre
"engine.cfg". El nuevo proyecto aparecerá en la lista de proyectos.
Edita el proyecto usando la opción 'Editar'.

Este proyecto contiene dos escenas "ball.scn"(pelota) y
"container.scn"(contenedor). La escena ball es solo una
pelota con física, mientras que la escena container tiene una
linda forma de colisión, de forma que las pelotas pueden tirarse
allí.

.. image:: /img/ballscene.png

.. image:: /img/contscene.png

Abre la escena container, luego selecciona el nodo raíz:

.. image:: /img/controot.png

Después, presiona el botón con forma de cadena, este es el botón de
instanciar!

.. image:: /img/continst.png

Selecciona la escena de la pelota (ball.scn), la pelota debería
aparecer en el origen (0,0), la mueves hasta el centro de la escena,
algo así:

.. image:: /img/continstanced.png

Presiona Reproducir y Voilà!

.. image:: /img/playinst.png

La pelota instanciada cayó hasta el fondo del pozo.

Un poco más
-----------

Puede haber tantas instancias como se desee en una escena,
simplemente intenta instanciar más pelotas, o duplícalas (Ctrl-D
o botón derecho -> Duplicar):

.. image:: /img/instmany.png

Luego intenta correr la escena nuevamente:

.. image:: /img/instmanyrun.png

Está bueno, eh? Así es como funciona instanciar.

Editando instancias
-------------------

Selecciona una de las muchas copias de las pelotas y ve al Inspector.
Hagamos que rebote mucho más, por lo que busca el parámetro
bounce(rebote) y configúralo en 1.0:

.. image:: /img/instedit.png

Lo próximo que sucederá es que un botón de "revertir" con forma de
"flecha en círculo" aparecerá. Cuando este botón está presente,
significa que hemos modificado una propiedad en la escena
instanciada, ignorando el valor original. Aún si esa propiedad es
modificada en la escena original, el valor personalizado siempre lo
sobrescribirá. Tocando el botón de revertir restaurará la propiedad
al valor original que vino de la escena.

Conclusión
----------

Instanciar parece útil, pero hay más de lo que se ve a simple vista!
La próxima parte del tutorial de instanciar cubrirá el resto...
