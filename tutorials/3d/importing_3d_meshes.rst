.. _doc_importing_3d_meshes:

Importando mallas 3D
====================

Introducción
------------

Godot soporta un importador de escena flexible y poderoso
:ref:`3D scene importer <doc_importing_3d_scenes>` que permite importar
una escena completa. Para un montón de artistas y desarrolladores esto
es mas que suficiente. Sin embargo, a muchos no les gusta este workflow
tanto y prefieren importar 3D Meshes individualmente y construir la
escena dentro del editor 3D de Godot. (Nota que para características más
avanzadas como animación por esqueletos, no hay opción en el 3D Scene
Importer).

El workflow de importación de malla 3D es simple y funciona usando el
formato de archivo OBJ. Las mallas importadas resultan en un archivo
binario .msh que el usuario puede poner en un :ref:`class_meshinstance`,
el cual a su vez es puede ser ubicado en algún lugar de la escena editada.

Importando
----------

La importación es hecha a través del menú Importar > Mesh:

.. image:: /img/mesh_import.png

El cual abre la ventana de importación de Mesh:

.. image:: /img/mesh_dialog.png

Este dialogo permite la importación de uno o más archivos OBJ en un
target path. Los archivos OBJ son convertidos a archivos .msh. Son
importados sin material en ellos, el material debe ser agregado por el
usuario (ve el tutorial :ref:`doc_fixed_materials`). Si el archivo
externo OBJ cambia será re-importado, manteniendo el material
recientemente asignado.

Opciones
--------

Algunas opciones están presentes. Las Normals son necesarias para
sombreado regular, mientras que Tangents son necesarias si planificas
usar normal-mapping en el material. En general, los archivos OBJ
describen como se debe sombrear muy bien, pero una opción para forzar
smooth shading está disponible.

Finalmente, hay una opción para soldar vértices. Dado que los archivos
OBJ son basados en texto, es común encontrar algunos de esos vértices
que no cierran, lo que resulta en sombreado extraño. La opción weld
vertices mezcla vértices que están demasiado cercanos para mantener
un sombreado suave adecuado.

Uso
---

Los recursos de Mesh (lo que este importador importa hacia) son usados
dentro de nodos MeshInstance. Simplemente ajusta la propiedad Mesh de
ellos.

.. image:: /img/3dmesh_instance.png

Y eso es todo.
