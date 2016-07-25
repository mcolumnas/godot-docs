.. _doc_scenes_and_nodes:

Escenas y nodos
================

Introducción
------------

.. image:: /img/chef.png

Imagina por un segundo que ya no eres un desarrollador de juegos. En
su lugar, eres un chef! Cambia tu atuendo hípster por un un gorro de
cocinero y una chaqueta doblemente abotonada. Ahora, en lugar de hacer
juegos, creas nuevas y deliciosas recetas para tus invitados.

Entonces, ¿Cómo crea un chef su receta? Las recetas se dividen en dos
secciones, la primera son los ingredientes y en segundo lugar las
instrucciones para prepararlos. De esta manera, cualquiera puede seguir
la receta y saborear tu magnífica creación.

Hacer juegos en Godot se siente prácticamente igual. Usar el motor es
como estar en una cocina. En esta cocina, los *nodos* son como un
refrigerador lleno de ingredientes frescos con los cuales cocinar.

Hay muchos tipos de nodos, algunos muestran imágenes, otros reproducen
sonido, otros muestran modelos 3D, etc. Hay docenas de ellos.

Nodos
-----

Pero vayamos a lo básico. Un nodo es el elemento básico para crear un
juego, tiene las siguientes características:

- Tiene un nombre.
- Tiene propiedades editables.
- Puede recibir una llamada a procesar en cada frame.
- Puede ser extendido (para tener mas funciones).
- Puede ser agregado a otros nodos como hijo.

.. image:: /img/tree.png

La  última  es muy importante. Los nodos pueden tener otros nodos como
hijos. Cuando se ordenan de esta manera, los nodos se transforman en
un **árbol**.

En Godot, la habilidad para ordenar nodos de esta forma crea una
poderosa herramienta para organizar los proyectos. Dado que diferentes
nodos tienen diferentes funciones, combinarlos permite crear funciones
mas complejas.

Esto probablemente no es claro aun y tiene poco sentido, pero todo va
a encajar unas secciones más adelante. El hecho mas importante a
recordar por ahora es que los nodos existen y pueden ser ordenados de
esa forma.

Escenas
------

.. image:: /img/scene_tree_example.png

Ahora que la existencia de nodos ha sido definida, el siguiente paso
lógico es explicar qué es una Escena.

Una escena esta compuesta por un grupo de nodos organizados
jerárquicamente (con estilo de árbol). Tiene las siguientes
propiedades:

- Una escena siempre tiene un solo nodo raíz.
- Las escenas pueden ser guardadas a disco y cargadas nuevamente.
- Las escenas pueden ser *instanciadas* (mas sobre esto después).
- Correr un juego significa ejecutar una escena.
- Puede haber varias escenas en un proyecto, pero para iniciar,
  una de ellas debe ser seleccionada y cargada primero.

Básicamente, el motor Godot es un **editor de escenas**. Tiene más
que suficientes herramientas para editar escenas 2D y 3D así como
interfaces de usuario, pero el editor gira entorno al concepto de
editar una escena y los nodos que la componen.

Creando un Nuevo Proyecto
----------------------

La teoría es aburrida, así que vamos a cambiar de enfoque y ponernos
prácticos. Siguiendo una larga tradición de tutoriales, el primer
proyecto va a ser el "Hola Mundo!". Para esto, se usará el editor.

Cuando godot se ejecuta sin un proyecto, aparecerá el Gestor
Proyectos. Esto ayuda a los desarrolladores a administrar sus
proyectos.

.. image:: /img/project_manager.png

Para crear un nuevo proyecto, la opción "Nuevo Proyecto" debe ser
utilizada. Elije y crea una ruta para el proyecto y especificá el
nombre del proyecto.

.. image:: /img/create_new_project.png

Editor
------

Una vez que el "Nuevo Proyecto" es creado, el siguiente paso es
abrirlo. Esto abrirá el editor Godot. Así luce el editor cuando
se abre:

.. image:: /img/empty_editor.png

Como mencionamos antes, hacer juegos en Godot se siente como estar
en una cocina, así que abramos el refrigerador y agreguemos algunos
nodos frescos al proyecto. Comenzaremos con un "Hola Mundo!" Para
hacer esto, el botón "Nuevo Nodo" debe ser presionado.

.. image:: /img/newnode_button.png

Esto abrirá el diálogo de Crear Nodo, mostrando una larga lista de
nodos que pueden ser creados:

.. image:: /img/node_classes.png

Desde allí, selecciona el nodo Label (Etiqueta) primero. Buscarlo es
probablemente la forma más rápida:

.. image:: /img/node_search_label.png

Y finalmente, crea el Label! Un montón de cosas suceden cuando
Crear es presionado:

.. image:: /img/editor_with_label.png

Primero que nada, la escena cambia hacia el editor 2D (porque
Label es un Nodo de tipo 2D), y el Label aparece, seleccionada,
en la esquina superior izquierda del viewport (ventana de
visualización).

El nodo aparece en el editor de árbol de escena (caja en la esquina
superior izquierda), y las propiedades de Label están en el
Inspector (caja en el costado derecho)

El siguiente paso será cambiar la propiedad "Text" de la etiqueta,
vamos a cambiarla a "Hola, Mundo!":

.. image:: /img/hw.png

Bien, todo esta listo para correr la escena! Presiona el botón
"PLAY SCENE" en la barra superior (o presiona F6):

.. image:: /img/playscene.png

Y... Uups.

.. image:: /img/neversaved.png

Las escenas necesitan ser salvadas para correr, por lo que guarda la
escena en algo como hola.scn en Escena -> Guardar:

.. image:: /img/save_scene.png

Y aquí es donde algo gracioso sucede. El de archivo es especial, y
solo permite guardar dentro del proyecto. La raiz del proyecto es
"res://" que significa "resource path" (camino de recursos).
Esto significa que los archivos sólo pueden ser guardados dentro
del proyecto. En el futuro, cuando hagas operaciones con archivos
en Godot, recuerda que "res://" es el camino de recursos, y no
importa la plataforma o lugar de instalación, es la forma de
localizar donde están los archivos de recursos dentro del juego.

Luego de salvar la escena y presionar Reproducir Escena nuevamente,
el demo "Hola, Mundo!" debería finalmente ejecutarse:

.. image:: /img/helloworld.png

Éxito!

.. _doc_scenes_and_nodes-configuring_the_project:

Configurando el proyecto
-----------------------

Ok, es momento de hacer algunas configuraciones en el proyecto. En
este momento, la única forma de correr algo es ejecutar la escena
actual. Los proyectos, sin embargo, tienen varias escenas por lo que
una de ellas debe ser configurada como la escena principal. Esta
escena es la que será cargada cuando el proyecto corre.

Estas configuraciones son todas guardadas en el archivo engine.cfg,
que es un archivo de texto plano en el formato win.ini, para una
edición fácil. Hay docenas de configuraciones que pueden ser
configuradas en ese archivo para alterar como un proyecto se
ejecuta, por lo que para hacer más simple el proceso, existe un
cuadro de diálogo de configuración del proyecto, el cual es un tipo
de interfaz para editar engine.cfg

Para acceder al cuadro de diálogo, simplemente ve a Escena ->
Configuración de proyecto.

Cuando la ventana abre, la tarea será seleccionar la escena
principal. Esto puede ser hecho fácilmente cambiando la propiedad
application/main_scene y seleccionando 'hola.scn'

.. image:: /img/main_scene.png

Con este cambio, presionar el botón de Play regular (o F5) va a
correr el proyecto, no importa la escena que se está editando.

Yendo atrás con el diálogo de configuración de proyecto. Este diálogo
permite una cantidad de opciones que pueden ser agregadas a engine.cfg
y mostrar sus valores por omisión. Si el valor por defecto está bien,
entonces no hay necesidad de cambiarlo.

Cuando un valor cambia, se marca un tick a la izquierda del nombre.
Esto significa que la propiedad va a ser grabada al archivo
engine.cfg y recordada.

Como una nota aparte, para futura referencia y un poco fuera de
contexto (al fin de cuentas este es el primer tutorial!), también
es posible agregar opciones de configuración personalizadas y
leerlas en tiempo de ejecución usando el singleton :ref:`Globals <class_Globals>`


Continuará...
------------------

Este tutorial habla de "escenas y nodos", pero hasta ahora ha habido
sólo *una* escena y *un* nodo! No te preocupes, el próximo tutorial
se encargará de ello...
