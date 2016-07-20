.. _doc_importing_3d_scenes:

Importando escenas 3D
=====================

Introducción
------------

La mayoría de los motores de juegos solo importan objetos 3D, los cuales
pueden contener esqueletos o animaciones, y luego todo el demás trabajo
es hecho in la UI del motor, como la ubicación de los objetos,
animaciones de pantalla completa, etc. En Godot, dado que el sistema de
nodos es muy similar a cómo funcionan las herramientas 3D DCC (como Maya,
3DS Max o Blender), escenas completas en 3D pueden ser importadas en
toda su gloria. Adicionalmente, al usar un lenguaje simple de etiquetas,
es posible especificar que objetos son importados para diferentes cosas,
como colisionables, cuartos y portales, vehículos y ruedas, distancias LOD,
billboards, etc.

Esto permite algunas características interesantes:

-  Importar escenas simples, objetos con rig, animaciones, etc.
-  Importar escenas completas. Escenarios enteros pueden ser creados y
   actualizados en el 3D DCC e importados a Godot cada vez que cambian,
   luego apenas un poco de edición es necesaria del lado del motor.
-  Cutscenes completas pueden ser importadas, incluyendo animaciones de
   múltiples personajes, iluminación, movimiento de cámara, etc.
-  Las escenas pueden continuar siendo editadas y scripteadas en el motor,
   donde los shaders y efectos de ambiente pueden ser agregados, enemigos
   ser instanciados, etc. El importador va a actualizar los cambios de
   geometría si la escena de origen cambia pero mantendrá los cambios
   locales también (en tiempo real mientras usas el editor Godot!)
-  Las texturas pueden ser todas importadas en lotes y actualizadas
   cuando la escena de origen cambia.

Esto se logra usando un lenguaje de etiquetas muy simple que será
explicado en detalle a continuación.

Exportando archivos DAE
-----------------------

Porque no FBX?
~~~~~~~~~~~~~~

La mayoría de los motores de juegos usan el formato FBX para importar
escenas 3D, el cual definitivamente es uno de los más estandarizados
en la industria. Sin embargo, este formato requiere el uso de una
librería cerrada de Autodesk la cual es distribuida con términos de
licenciamiento más restrictivos que Godot. El plan es, en algún momento
del futuro, implementar una conversión binaria externa, pero mientras
tanto FBX no está soportado.

Exportando archivos DAE desde Maya y 3DS Max
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

Autodesk agrego soporte incorporado collada a Maya y 3DS Max, pero esta
realmente roto y no debería ser usado. La mejor forma de exportar este
formato es usando los plugins
`OpenCollada <https://github.com/KhronosGroup/OpenCOLLADA/wiki/OpenCOLLADA-Tools>`__
Estos funcionan realmente bien, aunque no siempre están al día con la
ultima versión del software.

Exportando archivos DAE desde Blender
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

Blender también tiene soporte collada incorporado, pero está realmente
roto y no debería ser usado tampoco.

Godot provee un `Python
Plugin <https://github.com/godotengine/godot/tree/master/tools/export/blender25>`__
que hará un trabajo mucho mejor de exportar las escenas.

El proceso de importación
-------------------------

El proceso de importación comienza con el menú de importar escena 3D:

.. image:: /img/3dimp_menu.png

Esto abre lo que es probablemente el más grande dialogo de importación
de todos:

.. image:: /img/3dimp_dialog.png

Muchas opciones existen allí, así que cada sección será explicada a
continuación:

Source & target paths
~~~~~~~~~~~~~~~~~~~~~

Para importar, dos opciones se necesitan. La primera es el archivo
.dae fuente (.dae es por Collada. Mas formatos de importación se
agregaran eventualmente, pero Collada es el formato abierto más completo
cuando esto escribió).

Un target folder necesita proveerse, entonces el importador puede
importar la escena allí. La escena importada tendrá el mismo nombre
de archivo que la fuente, excepto por la extensión .scn, así que
asegúrate de elegir buenos nombres cuando exportas!

Las texturas usadas serán copiadas y convertidas. Las texturas en
aplicaciones 3D son usualmente solo archivos PNG o JPG. Godot los
convertirá a formato de compresión de textura de memoria de video(s3tc,
pvrtc, ericsson, etc.) por defecto para mejorar el rendimiento y
ahorrar recursos.

Debido a que las texturas originales, archivo 3D y texturas no son
usualmente necesarias, es recomendado mantenerlas fuera del proyecto.
Para algunos consejos sobre cómo hacer esto de la mejor forma, puedes
chequear el tutorial :ref:`doc_project_organization`.

Dos opciones para texturas se proveen. Pueden ser copiadas al mismo
lugar que la escena, o pueden copiarse a un path común (ajustable
en la configuración de proyecto). Si eliges esto, asegúrate que no
haya dos texturas con el mismo nombre.

Consejos de Rigging 3D
~~~~~~~~~~~~~~~~~~~~~~

Antes de ir a las opciones, aquí hay algunos consejos para asegurarte
que tus rigs se importen adecuadamente

-  Solo se importan hasta 4 weights por vertex, si un vertex depende de
   más de 4 huesos, solo los 4 más importantes (los que tienen más peso)
   van a ser importados. Para la mayoría de los modelos esto funciona
   usualmente bien, pero tenlo en mente.
-  No uses animaciones de hueso con escala no uniforme, ya que esto
   es probable que no se importe adecuadamente. Intenta lograr el mismo
   efecto agregando huesos.
-  Cuando exportas desde Blender, asegúrate que los objetos modificados
   por un esqueleto sean hijos de el. Muchos objetos pueden ser modificados
   por un solo esqueleto, pero todos deben ser hijos directos.
-  De la misma forma, cuando uses Blender, asegúrate que la transformación
   relativa de los nodos hijos al esqueleto sea cero (sin rotación, sin
   translación, sin escala. Todos ceros y con una escala de 1.0). La
   posición de ambos objetos (el pequeño punto naranja) debe estar en el
   mismo lugar.

Opciones de importación 3D
~~~~~~~~~~~~~~~~~~~~~~~~~~

Esta sección contiene muchas opciones para cambiar la forma en que
el workflow de importación funciona. Algunos (como HDR) serán mejor
explicadas en otras secciones, pero en general un patrón puede ser
visible en las opciones y esto es, muchas de las opciones terminan con
"-something". Por ejemplo:

-  Remover Nodos (-noimp)
-  Ajustar Alpha en Materiales (-alpha)
-  Crear colisiones (-col)

Esto significa que los nombres de objeto en el DCC 3D necesitan tener
esas opciones agregadas al final para que el importador sepa que son.
Cuando se importan, Godot las convertirá a lo que están destinadas a ser.

**Nota:** Usuarios de Maya deben usar “_" (underscore) en lugar de
"-" (minus).

Aquí hay un ejemplo de como una escena en el DCC 3D luce (usando Blender),
y como se importa en Godot:


.. image:: /img/3dimp_blender.png

Fíjate que:

-  La cámara se importó normalmente.
-  Un cuarto fue creado (-room).
-  Un portal fue creado (-portal).
-  El Mesh tuvo agregado de colisión estática (-col).
-  La luz no se importo (-noimp).

Opciones en detalle
~~~~~~~~~~~~~~~~~~~

A continuación una lista de las opciones más importantes y lo que hacen
con mas detalle.

Remove nodes (-noimp)
^^^^^^^^^^^^^^^^^^^^^^

Los nombres de nodos que tengan esto al final serán removidos en tiempo
de importación, no importa su tipo. Borrarlos luego no tiene sentido la
mayoría de las veces porque serán restauradas si la escena fuente cambia.

Import animations
^^^^^^^^^^^^^^^^^^^^

Algunos formatos de escena (.dae) soportan una o más animaciones. Si esto
es chequeado, un nodo `AnimationPlayer <class_animationplayer>`__ será
creado, conteniendo las animaciones.


Compress geometry
^^^^^^^^^^^^^^^^^

Esta opción (deshabilitada [STRIKEOUT:o mas bien, siempre habilitada] al
momento de escribir esto) va a comprimir la geometría de forma que tome
menos espacio y renderize mas rápido (al costo de menos precisión).

Force generation of tangent arrays
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

El importador detecta cuando has usado una textura normalmap, o cuando
el archivo fuente contiene información de tangentes/binormales. Estos
arreglos son necesarios para que funcione normalmapping, y la mayoría de
los exportadores saben lo que hacen cuando exportan esto. Sin embargo,
es posible encontrarse con escenas que no tienen esta información lo
cual, como resultado, hace que normal-mapping no funcione. Si notas que
los normal-maps no funcionan cuando importas la escena, prende esto!

SRGB -> linear of diffuse textures
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

Cuando renderizas usando HDR (High Dynamic Range) puede ser deseable
usar texturas linear-space para lograr iluminación más real. De otra
forma, los colores pueden saturar y contrastar demasiado cuando cambia
la exposición. Esta opción debe ser usada junto con SRGB en
`WorldEnvironment <class_worldenvironment>`__. Las opciones de
importación de textura también tienen la opción para hacer esta
conversión, pero si esta propiedad esta encendida, las conversión
siempre será hecha a las texturas difusas (usualmente lo que se desea).
Para mas información, lee el tutorial :ref:`doc_high_dynamic_range`.

Set alpha in materials (-alpha)
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

Cuando trabajas con la mayoría de los DCCs 3D, es bastante obvio cuando
una textura es transparente y tiene opacidad lo cual raramente afecta
el workflow del renderizado final. Sin embargo, cuando tratas con
renderizado en tiempo real, los materiales con alpha blending son
usualmente menos óptimos para dibujar, por lo que deben ser marcados
específicamente como tales.

Originalmente Godot detectaba esto basado en si la textura fuente tenia
un canal alpha, pero la mayoría de las aplicaciones de manipulación de
imágen como Photoshop o Gimp van a exportar este canal de todas formas
aun si no es usado. Se agregó código más tarde para chequear manualmente
si realmente había alguna transparencia en la textura, pero los artistas
de todas formas muy a menudo dejan uvmaps en partes opacas de la
textura y otras áreas sin usar (donde no existe UV) transparentes,
volviendo esta detección sin sentido.

Finalmente, se decidió que es mejor importar todo como opaco y dejar a
los artistas solucionar los materiales que necesitan transparencia
cuando es obvio que no lucen bien (ve el tutorial :ref:`doc_materials`).

Como un ayudante, dado que todo DCC 3D permite nombrar los materiales
y mantener su nombre al exportar, el modificador (-alpha) en su nombre
apuntara al importador de escena 3D de Godot que ese material va a usar
el canal alpha para transparencia.

Set vert. color in materials (-vcol)
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

La mayoría de los DCC 3D soportan pintado por vertex. Esto es
generalmente aplicado como una multiplicación o mezcla de pantalla.
Sin embargo, a menudo se presenta el caso de que tu exportador va a
exportar esta información como 1s, o exportarla como alguna otra cosa
y no te darás cuenta. Debido a que en la mayoría de los casos esta
opción no es deseada, solo agrega esto a cualquier material para
confirmar que se desea usar vertex colors.

Create collisions (-col, -colonly)
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

La opción "-col" solo funcionara para nodos Mesh. Si es detectada,
un nodo hijo de colisión estática será agregado, usando la misma
geometría que la malla.

Sin embargo, a menudo sucede que la geometría visual es demasiado
compleja o muy poco suave para colisiones, lo que termina no funcionando
bien. Para resolver esto, existe el modificador -"colonly", el cual
remueve la malla cuando se importa y en su lugar crea una colisión
`StaticBody <class_staticbody>`__. Esto ayuda a separar la malla visual
y colisión.

La opción "-colonly" puede también ser usada con objetos vacíos de
Blender. Al importar creara un `StaticBody <class_staticbody>`__
con nodos de colisión como hijos. Los nodos de colisión serán de las
formas predefinidas, dependiendo en el tipo de empty draw de Blender:

.. image:: /img/3dimp_BlenderEmptyDrawTypes.png

-  Una flecha crea `RayShape <class_rayshape>`__
-  El cubo crea `BoxShape <class_boxshape>`__
-  Una imagen crea `PlaneShape <class_planeshape>`__
-  Una esfera (y otros no listados) crea `SphereShape <class_sphereshape>`__

Para mejor visibilidad en el editor de Blender el usuario puede ajustar
la opción "on colision empties" y ajustar algún color distintivo para
ellas en User Preferences / Themes / 3D View / Empty.

Create rooms (-room)
^^^^^^^^^^^^^^^^^^^^

Esto es usado para crear un cuarto. Como regla general, cualquier nodo
que es un hijo de este nodo será considerado dentro del cuarto (incluyendo
portales).

Para más información sobre rooms/portals, mira el tutorial
[[Portals and Rooms]].

Hay dos formas posibles para usar este modificador. La primera es usando
un nodo Dummy/Empty en la aplicación 3D con la etiqueta "-room". Para
que esto funcione, el "interior" del cuarto debe estar cerrado (la
geometría de los hijos debe contener paredes, techo, piso, etc. y los
únicos orificios al exterior deben estar cubiertos por portales). El
importador entonces creara una versión simplificada de la geometría para
el cuarto.

La segunda forma es usando el modificador "-room" en un nodo Mesh. Esto
usará la malla como base para el árbol BPS que contiene los límites de
los cuartos. Asegúrate que la forma de la malla sea **cerrada**, todas
las normales **apunten hacia afuera** y que la geometría no se
**intersecte a si misma**, de otra forma los limites pueden ser mal
computados (los árboles BSP son demasiado detallistas y difíciles de
trabajar, motivo por el cual se usan muy poco en la actualidad..).

De cualquier forma, el cuarto necesitara portales, que se describen a
continuación.

Create portals (-portal)
^^^^^^^^^^^^^^^^^^^^^^^^

Los portales son la vista para mirar fuera del cuarto. Siempre son
algún tipo de forma plana en la superficie del cuarto. Si el portal es
dejado solo, es usado para activar la oclusión cuando miras
dentro<->fuera del cuarto.

.. Nuevamente, mas información en el tutorial [[Portals and Rooms]].

Básicamente, las condiciones para hacer e importar un portal desde un
DCC 3D son:

-  Debe ser hijo de un cuarto.
-  Debe reposar en la superficie del cuarto (esto no necesita ser súper
   exacto, solo hazlo lo más cercano posible a ojo y Godot lo ajustara)
-  Debe ser plano, con forma convexa, cualquier forma plana y convexa
   está bien, no importa el eje o tamaño.
-  Las normales de la forma plana deben **todas apuntar hacia AFUERA**
   del cuarto.

Así es como luce usualmente:

.. image:: /img/3dimp_portal.png

Para conectar los cuartos, simplemente haz dos portales idénticos para
ambos cuartos y ubícalos superpuestos. Esto no tiene que ser
perfectamente exacto, nuevamente, Godot intentara arreglarlo.

[..]
^^^^

El resto de las etiquetes en este sección deberían ser bastante obvios,
o serán documentados/cambiados en el futuro.

Double-sidedness
~~~~~~~~~~~~~~~~

Collada y cualquier otro formato soporta especificar el doble-lado de
la geometría (en otras palabras, cuando no tiene doble lado, las caras
hacia atrás no se dibujaran). Godot soporta esta opción por Material,
no por Geometría.

Cuando exportas desde el DCC 3D que funciona con doble-lado por objeto
(como Blender o Maya), asegúrate que los objetos con doble lado no
comparten material con los que tienen un solo lado, o el importador
no sabrá discernir.

Animation options
~~~~~~~~~~~~~~~~~

Algunas cosas a tener en cuenta cuando se importan animaciones. Los
DCCs 3D permiten animación con curvas para cada componente x,y,z haciendo
IK constraints y otras cosas. Cuando se importa para tiempo real, las
animaciones son muestreadas (en pequeños intervalos) por lo que toda
esta información se pierde. Las animaciones muestreadas son rápidas
para procesar, pero pueden requerir cantidades considerables de
memoria.

Por este motivo, la opción "Optimize" existe pero, en algunos casos,
esta opción puede romper una animación, así que asegúrate de
deshabilitarla si notas algún problema.

Algunas animaciones están destinadas a ser cíclicas (como animaciones de
caminar) si este es el caso, las animaciones con nombres que terminan
en "-cycle" o "-loop" son automáticamente ajustadas para repetirse.

Import script
~~~~~~~~~~~~~

Crear un script para procesar la escena importada es en los hechos
realmente simple. Esto es genial para post processing, cambiar materiales,
hacer cosas con la geometría, etc.

Crea un script que básicamente luce así:

::

    tool # necesario para que corra en el editor
    extends EditorScenePostImport

    func post_import(scene):
      # haz tus cosas aquí
      pass # la escena contiene la escena importada comenzando del nodo raíz

La función por importación toma la escena importada como un parámetro
(el parámetro es en realidad el nodo raíz de la escena).


Update logic
~~~~~~~~~~~~

Otros tipos de recursos (como sonidos, mallas, fuentes, imágenes, etc)
son re importados por completo cuando cambian y los cambios del usuario
no se mantienen.

Debido a que las escenas 3D pueden ser realmente complejas, usan una
estrategia de actualización diferente. El usuario puede haber hecho
cambios locales para tomar ventaja de las características del motor y
sería realmente frustrante si todo se pierde al re importar por que
el asset fuente cambio.

Esto llevo a la implementación de una estrategia de actualización.
La idea detrás de la misma es que el usuario no pierde nada de lo
que hizo, y solo datos agregados o que no pueden ser editados en Godot
se actualizarán.

Funciona como esto:

Estrategia
^^^^^^^^^^

Cuando se realizan cambios en el asset fuente (ejemplo: .dae), y se
re importa, el editor recordara como lucia la escena originalmente,
y seguirá tus cambios locales como nodos renombrados, moviéndolos
o re aparentándolos. Finalmente, lo siguiente será actualizado:

-  Los datos de mallas serán reemplazados por los datos de la escena
   actualizada.
-  Los materiales serán mantenidos si no fueron modificados por el
   usuario.
-  Las formas de Portals y Rooms serán remplazadas por las de la escena
   actualizada.
-  Si el usuario movio un nodo dentro de Godot, la transformación se
   mantendrá. Si el usuario movió un nodo en el asset fuente, la
   transformación será reemplazada. Finalmente, si el nodo fue movido
   en ambos lugares, la transformación será combinada.

En general, si el usuario borra cualquier cosa de la escena importada
(nodo, malla, material, etc.), actualizar el asset fuente va a restaurar
lo que fue borrado. Esta es una buena forma de revertir cambios locales
para cualquier cosa. Si realmente no quieres más un nodo en la escena,
bórralo de los dos lugares o agrega la etiqueta "-noimp" en el asset
fuente.


Fresh re-import
^^^^^^^^^^^^^^^

También puede suceder que el asset fuente cambio hasta deja de ser
reconocible y se desea una re importación completa. Si es así, solo
re abre el dialogo de importación de escena 3D desde el menú
Import -> Re-Import y realiza re-import.
