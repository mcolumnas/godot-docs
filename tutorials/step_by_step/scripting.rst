.. _doc_scripting:

Scripting
=========

Introducción
------------

Mucho se ha dicho sobre herramientas que permiten a los usuarios crear
juegos sin programar. Ha sido un sueño para muchos desarrolladores
independientes el crear juegos sin aprender a escribir código. Esto ha
sido así por un largo tiempo, aun dentro de compañias, donde los
desarrolladores de juegos desean tener mas control del flujo del juego
(game flow).

Muchos productos han sido presentados prometiendo un entorno sin
programación, pero el resultado es generalmente incompleto, demasiado
complejo o ineficiente comparado con el código tradicional. Como
resultado, la programación esta aquí para quedarse por un largo tiempo.
De hecho, la dirección general en los motores de jugos ha sido agregar
herramientas que reducen la cantidad de código que necesita ser escrito
para tareas especificas, para acelerar el desarrollo.

En ese sentido, Godot ha tomado algunas decisiones de diseño útiles con
ese objetivo. La primera y mas importante es el sistema de escenas. El
objetivo del mismo no es obvio al principio, pero trabaja bien mas
tarde. Esto es, descargar a los programadores de la responsabilidad de
la arquitectura del codigo.

Cuando se diseñan juegos usando el sistema de escenas, el proyecto
entero esta fragmentado en escenas complementarias (no individuales).
Las escenas se complementar entre si, en lugar de estar separadas.
Tendremos un montón de ejemplos sobre esto mas tarde, pero es muy
importante recordarlo.

Para aquellos con una buena base de programación, esto significa que
un patrón de diseño diferente a MVC(modelo-vista-controlador). Godot
promete eficiencia al costo de dejar los hábitos MVC, los cuales se
reemplazan por el patrón *escenas como complementos*.

Godot también utiliza el <http://c2.com/cgi/wiki?EmbedVsExtend>`__
patrones para scripting, por lo que los scripts se extienden desde
todas las clases disponibles.

GDScript
--------

:ref:`doc_gdscript` es un lenguaje de scripting de tipado dinámico
hecho a medida de Godot. Fue diseñado con los siguientes objetivos:

-  El primero y mas importante, hacerlo simple, familiar y fácil,
   tan fácil  de aprender como sea posible.
-  Hacer el código legible y libre de errores. La sintaxis es
   principalmente extraída de Python.

A los programadores generalmente les toma unos días aprenderlo, y
entre las primeras dos semanas para sentirse cómodos con el.

Como con la mayoría de los lenguajes de tipado dinámico, la mayor
productividad (el código es mas fácil de aprender, mas rápido de
escribir, no hay compilación, etc) es balanceada con una pena de
rendimiento, pero el código mas critico esta escrito en C++ en primer
lugar dentro del motor (vector ops, physics, match, indexing, etc),
haciendo que la rendimiento resultante sea mas que suficiente para
la mayoría de los juegos.

En cualquier caso, si se requiere rendimiento, secciones criticas
pueden ser reescritas en C++ y expuestas transparentemente al script.
Esto permite reemplazar una clase GDScript con una clase C++ sin
alterar el resto del juego.

Scripting de una Escena
-----------------------

Before continuing, please make sure to read the :ref:`doc_gdscript` reference.
It's a simple language and the reference is short, should not take more
than a few minutes to glance.

Scene setup
~~~~~~~~~~~

This tutorial will begin by scripting a simple GUI scene. Use the add
node dialog to create the following hierarchy, with the following nodes:

- Panel

  * Label
  * Button

It should look like this in the scene tree:

.. image:: /img/scriptscene.png

And try to make it look like this in the 2D editor, so it makes sense:

.. image:: /img/scriptsceneimg.png

Finally, save the scene, a fitting name could be "sayhello.scn"

.. _doc_scripting-adding_a_script:

Adding a script
~~~~~~~~~~~~~~~

Select the Panel node, then press the "Add Script" Icon as follows:

.. image:: /img/addscript.png

The script creation dialog will pop up. This dialog allows to select
the language, class name, etc. GDScript does not use class names in
script files, so that field is not editable. The script should inherit
from "Panel" (as it is meant to extend the node, which is of Panel type,
this is automatically filled anyway).

Select the filename for the script (if you saved the scene previously,
one will be automatically generated as sayhello.gd) and push "Create":

.. image:: /img/scriptcreate.png

Once this is done, the script will be created and added to the node. You
can see this both as an extra icon in the node, as well as in the script
property:

.. image:: /img/scriptadded.png

To edit the script, pushing the icon above should do it (although, the
UI will take you directly to the Script editor screen). So, here's the
template script:

.. image:: /img/script_template.png

There is not much in there. The "_ready()" function is called when the
node (and all its children) entered the active scene. (Remember, it's
not a constructor, the constructor is "_init()" ).

The role of the script
~~~~~~~~~~~~~~~~~~~~~~

A script basically adds a behavior to a node. It is used to control the
node functions as well as other nodes (children, parent, siblings, etc).
The local scope of the script is the node (just like in regular
inheritance) and the virtual functions of the node are captured by the
script.

.. image:: /img/brainslug.jpg

Handling a signal
~~~~~~~~~~~~~~~~~

Signals are used mostly in GUI nodes, (although other nodes have them
too). Signals are "emitted" when some specific kind of action happens,
and can be connected to any function of any script instance. In this
step, the "pressed" signal from the button will be connected to a custom
function.

There is a GUI for connecting signals, just select the node and press
the "Signals" button:

.. image:: /img/signals.png

which will show the list of signals a Button can emit.

.. image:: /img/button_connections.png

But this example will not use it. We don't want to make things *too*
easy. So please close that screen!

In any case, at this point it is clear that that we are interested in
the "pressed" signal, so instead of doing it with the visual
interface, the connection will be done using code.

For this, there is a function that is probably the one that Godot
programmers will use the most, this is
:ref:`Node.get_node() <class_Node_get_node>`.
This function uses paths to fetch nodes in the current tree or anywhere
in the scene, relative to the node holding the script.

To fetch the button, the following must be used:

::

    get_node("Button")

So, next, a callback will be added for when a button is pressed, that
will change the label's text:

::

    func _on_button_pressed():
        get_node("Label").set_text("HELLO!")

Finally, the button "pressed" signal will be connected to that callback
in _ready(), by using :ref:`Object.connect() <class_Object_connect>`.

::

    func _ready():
        get_node("Button").connect("pressed",self,"_on_button_pressed")

The final script should look like this:

::

    extends Panel

    # member variables here, example:

    # var a=2
    # var b="textvar"

    func _on_button_pressed():
        get_node("Label").set_text("HELLO!")

    func _ready():
        get_node("Button").connect("pressed",self,"_on_button_pressed")

Running the scene should have the expected result when pressing the
button:

.. image:: /img/scripthello.png

**Note:** As it is a common mistake in this tutorial, let's clarify
again that get_node(path) works by returning the *immediate* children of
the node controlled by the script (in this case, *Panel*), so *Button*
must be a child of *Panel* for the above code to work. To give this
clarification more context, if *Button* were a child of *Label*, the code
to obtain it would be:

::

    # not for this case
    # but just in case
    get_node("Label/Button")

And, also, try to remember that nodes are referenced by name, not by
type.
