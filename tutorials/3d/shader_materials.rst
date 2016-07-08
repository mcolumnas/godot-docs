.. _doc_shader_materials:

Shader materials (Materiales Shader)
====================================

Introducción
------------

Para la mayoría de los casos, :ref:`doc_fixed_materials` es suficiente
para crear las texturas deseadas o el "look and feel". Los materiales
shader están un paso más adelante, agregando una cantidad enorme de
flexibilidad. Con ellos es posible:

-  Crear texturas procedurales.
-  Crear texturas con mezclas complejas.
-  Crear materiales animados, o materiales que cambian con el tiempo.
-  Crear efectos refractarios u otros efectos avanzados.
-  Crear shaders de iluminación especial para materiales mas exóticos.
-  Animar vértices, como hojas de árboles o pasto.
-  Y mucho mas!

Tradicionalmente, la mayoría de los motores te pedirán que aprendas GLSL,
HLSL o CG, los cuales son bastante complejos para las habilidades de la
mayoría de los artistas. Godot usa una versión simplificada de un lenguaje
de shaders que detecta errores en la medida que escribes, así puedes ver
la edición de tus shaders en tiempo real. Adicionalmente, es posible editar
shaders usando un editor gráfico visual basado en nodos.

Creando un ShaderMaterial
-------------------------

Crea un nuevo ShaderMaterial en algín objeto de tu elección. Ve a la
propiedad "Shader", luego crea un nuevo "MaterialShader" (usa
"MatrialShaderGraph" para acceder al editor gráfico visual):

.. image:: /img/shader_material_create.png

Edita el shader recientemente creado, y el editor de shader aparecerá:

.. image:: /img/shader_material_editor.png

Hay tres pestañas de código abiertas, la primera es para el vertex
shader (shader de vértice), la segunda para el fragment (fragmento) y
la tercera para lighting (iluminación). El lenguaje de shader esta
documentado en :ref:`doc_shading_language` asique un pequeño ejemplo
será presentado a continuación.

Crear un shader fragment muy simple que escribe un color:

::

    uniform color col;
    DIFFUSE = col.rgb;

Los cambios de código tienen lugar en tiempo real. Si el código es
modificado, se recompila instantáneamente y el objeto es actualizado.
Si se comete un error de escritura, el editor notificara la falla de
compilación:

.. image:: /img/shader_material_typo.png

Finalmente, ve atrás y edita el material, y la exportación uniforme será
visible instantáneamente:

.. image:: /img/shader_material_col.png

Esto permite crear de forma muy rápida y personalizada, materiales
complejos para cada tipo de objeto.
