.. _doc_project_organization:

Organización de proyectos
=========================

Introducción
------------

Este tutorial esta dirigido a proponer un workflow simple sobre como
organizar proyectos. Debido a que Godot permite que el programador
use el sistema de archivos como el o ella quieren, encontrar una forma
de organizar los proyectos cuando empiezas a usar el motor puede ser
algo difícil. Por esto, un workflow simple sera descrito, el cual
puede ser usado o no, pero deberia servir como un punto inicial.

Ademas, usar control de versiones puede ser un rato, por lo que este
documento también lo incluirá.

Organización
------------

Otros motores de juegos a menudo trabajan teniendo una base de datos,
donde puedes navegar imágenes, modelos, sonidos, etc. Godot es mas
basado en escenas por naturaleza así que la mayor parte del tiempo
los assets están empacados dentro de las escenas o simplemente existen
como archivos pero no referenciados desde escenas.

Importación & directorio del juego
----------------------------------

Es muy a menudo necesario usar la importación de assets en Godot. Como
los assets de origen para importar también son reconocidos como recursos
por el motor, esto puede volverse un problema si ambos están dentro de
la carpeta de proyecto, porque al momento de exportar el exportador va a
reconocerlos y exportar ambos.

Para resolver esto, es una buena practica  tener tu carpeta de juego
dentro de otra carpeta (la verdadera carpeta del proyecto). Esto
permite tener los assets del juego separados de los assets de origen,
y también permite el uso de control de versiones (como svn o git) para
ambos. Aquí hay un ejemplo:

::

    myproject/art/models/house.max
    myproject/art/models/sometexture.png
    myproject/sound/door_open.wav
    myproject/sound/door_close.wav
    myproject/translations/sheet.csv

Luego también, el juego en si, en este caso, dentro de la carpeta game/:

::

    myproject/game/engine.cfg
    myproject/game/scenes/house/house.scn
    myproject/game/scenes/house/sometexture.tex
    myproject/game/sound/door_open.smp
    myproject/game/sound/door_close.smp
    myproject/game/translations/sheet.en.xl
    myproject/game/translations/sheet.es.xl

Siguiendo este diseño, muchas cosas pueden ser hechas:

-  El proyecto entero sigue estando dentro de una carpeta (myproject/).
-  Exportar el proyecto no exportara los archivos .wav y .png que
   fueron importados.
-  myproject/ puede ser puesta directamente dentro de un VCS (como svn
   o git) para control de versión, tanto los assets de origen como del
   juego serán seguidos.
-  Si un equipo esta trabajando en el proyecto, los assets pueden ser
   re-importados por otros miembros del proyecto, porque Godot sigue a
   los assets de origen usando paths relativos.

Organización de escena
----------------------

Dentro de la carpeta del juego, una pregunta que a menudo aparece es
como organizar las escenas en el sistema de archivos. Muchos
desarrolladores intentan la organización  de assets por tipo y terminan
con un desorden luego de un tiempo, por lo que la mejor respuesta
probablemente es organizarlos basados en como funciona el juego y no
en el tipo de asset. Aquí algunos ejemplos.

Si organizas tu proyecto basado en tipo de asset, luciría como esto:

::

    game/engine.cfg
    game/scenes/scene1.scn
    game/scenes/scene2.scn
    game/textures/texturea.png
    game/textures/another.tex
    game/sounds/sound1.smp
    game/sounds/sound2.wav
    game/music/music1.ogg

Lo cual en general es una mala idea. Cuando un proyecto comienza a
crecer mas allá de cierto punto, esto se vuelve inmanejable. Es
realmente difícil decir que pertenece donde.

En general es una mejor idea usar organización basada en contexto del
juego, algo como esto:

::

    game/engine.cfg
    game/scenes/house/house.scn
    game/scenes/house/texture.tex
    game/scenes/valley/canyon.scn
    game/scenes/valley/rock.scn
    game/scenes/valley/rock.tex
    game/scenes/common/tree.scn
    game/scenes/common/tree.tex
    game/player/player.scn
    game/player/player.gd
    game/npc/theking.scn
    game/npc/theking.gd
    game/gui/main_screen/main_sceen.scn
    game/gui/options/options.scn

Este modelo o modelos similares permiten que los proyectos crezcan hasta
tamaños realmente grandes y aun ser completamente administrable. Nota
que todo esta basado en partes del juego que pueden ser nombradas o
descritas, como la pantalla de ajustes (settings screen) o el valle
(valley). Debido a que todo en Godot es hecho con escenas, y todo lo
que puede ser nombrado o descrito puede ser una escena, este workflow
es muy suave y llevadero.

Archivos de Cache
-----------

Godot usa un archivo oculto llamado ".fscache" en la raíz del proyecto.
En el, se cachean los archivos de proyecto y es usado para rápidamente
saber cuando uno es modificado. Asegúrate de **no enviar este archivo**
a git o svn, ya que contiene información local y puede confundir otra
instancia del editor en otra computadora.
