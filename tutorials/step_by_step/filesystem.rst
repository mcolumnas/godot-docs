.. _doc_filesystem:

Sistema de archivos (Filesystem)
==========

Introducción
------------

Los sistemas de archivos son otro tema importante en el desarrollo de
un motor. El sistema de archivos gestiona como los assets son
guardados, y como son accedidos. Un sistema de archivos bien diseñado
también permite a múltiples desarrolladores editar los mismos archivos
de código y assets mientras colaboran juntos.

Las versiones iniciales del motor Godot (y previas iteraciones antes
de que se llamase Godot) usaban una base de datos. Los assets eran
guardados en ella y se le asignaba un ID. Otros enfoques fuero
intentados también, como base de datos local, archivos con metadatos,
etc. Al final el enfoque mas simple gano y ahora Godot guarda todos
los assets como archivos en el sistema de archivos.

Implementación
--------------

El sistema de archivos almacena los recursos en disco. Cualquier cosa,
desde un script, hasta una escena o una imagen PNG es un recurso para
el motor. Si un recurso contiene propiedades que referencian otros
recursos en disco, los caminos (paths) a esos recursos con incluidos
también. Si un recurso tiene sub-recursos que están incorporados, el
recurso es guardado en un solo archivo junto a todos los demás
sub-recursos. Por ejemplo, un recurso de fuente es a menudo incluido
junto a las texturas de las fuentes.

En general el sistema de archivos de Godot evita usar archivos de
metadatos. La razón para esto es es simple, los gestores de assets y
sistemas de control de versión (VCSs) son simplemente mucho mejores
que cualquier cosa que nosotros podamos implementar, entonces Godot
hace su mejor esfuerzo para llevarse bien con SVN, Git, Mercurial,
Preforce, etc.

Ejemplo del contenido de un sistema de archivos:

::

    /engine.cfg
    /enemy/enemy.scn
    /enemy/enemy.gd
    /enemy/enemysprite.png
    /player/player.gd

engine.cfg
----------

El archivo engine.cfg es la descripción del proyecto, y siempre se
encuentra en la raíz del proyecto, de hecho su locación define donde
esta la raiz. Este es el primer archivo que Godot busca cuando se abre
un proyecto.

Este archivo contiene la configuración del proyecto en texto plano,
usando el formato win.ini. Aun un engine.cfg vacío puede funcionar
como una definición básica de un proyecto en blanco.

Delimitador de camino (path)
----------------------------

Godot solo soporta ``/`` como delimitador de ruta o camino. Esto es
hecho por razones de portabilidad. Todos los sistemas operativos
soportan esto, aun Windows, por lo que un camino como
``c:\project\engine.cfg`` debe ser tipiado como
``c:/project/engine.cfg``.

Camino de recursos
------------------

Cuando se accede a recursos, usar el sistema de archivos del sistema
operativo huésped puede ser complejo y no portable. Para resolver este
problema, el camino especial ``res://`` fue creado.

El camino ``res://`` siempre apuntara a la raíz del proyecto (donde
se encuentra engine.cfg, por lo que es un hecho que
``res://engine.cfg`` siempre es valido)

El sistema de archivos es de lectura-escritura solo cuando se corre
el proyecto localmente desde el editor. Cuando se exporta o cuando se
ejecuta en diferentes dispositivos (como teléfonos o consolas, o corre
desde DVD), el sistema de archivos se vuelve de solo lectura, y la
escritura no esta permitida.

Camino de usuario
---------

Escribir a disco es de todas formas necesario para varias tareas como
guardar el estado del juego o descargar paquetes de contenido. Para
este fin existe un camino especial ``user://``que siempre se puede
escribir.

Sistema de archivos de huésped
------------------------------

De forma alternativa se puede utilizar también caminos del sistema de
archivos huésped, pero esto no es recomendado para un producto
publicado ya que estos caminos no tienen garantía de funcionar en todas
las plataformas. Sin embargo, usar caminos de sistema de archivos
huésped puede ser muy útil cuando se escriben herramientas de
desarrollo en Godot!

Inconvenientes
--------------

Hay algunos inconvenientes con este simple diseño de sistema de
archivos. El primer tema es que mover assets (renombrarlos o
moverlos de un camino a otro dentro del proyecto) va a romper las
referencias a estos assets. Estas referencias deberán ser re-definidas
para apuntar a la nueva locación del asset.

El segundo es que bajo Windows y OSX los nombres de archivos y caminos
no toman en cuenta si son mayúsculas o minúsculas. Si un desarrollador
trabajando en un sistema de archivos huésped guarda un asset como
"myfile.PNG", pero lo referencia como "myfile.png", funcionara bien
en su plataforma, pero no en otras, como Linux, Android, etc. Esto
también puede aplicarse a archivos binarios exportados, que usan un
paquete comprimido para guardar todos los archivos.

Es recomendado que tu equipo defina con claridad una nomenclatura
para archivos que serán trabajados con Godot! Una forma simple y
a prueba de errores es solo permitir nombres de archivos y caminos
en minúsculas.
