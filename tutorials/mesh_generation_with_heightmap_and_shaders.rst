.. _doc_mesh_generation_with_heightmap_and_shaders:

Generación de mallas con heightmap y shaders
============================================

Introducción
------------

Este tutorial te ayudara a usar los shaders de Godot para deformar una
malla de plano de forma que parezca terreno básico. Recuerda que esta
solución tiene pros y contras.

Pros:

-  Bastante fácil de hacer.
-  Este enfoque permite la computación LOD de terreno.
-  El heightmap puede ser usado en Godot para crear un normal map.

Contras:

-  El Shader de Vértices no puede re-computar las normales de las caras.
   Por lo que, si tu malla no es estática, este método no funcionara
   en materiales con sombras.
-  Este tutorial usa una malla de plano importada de Blender al motor
   Godot. Godot puede crear mallas también.

Ve este tutorial como una introducción, no un método que deberías utilizar
en tus juegos, excepto si piensas hacer LOD. De otra forma, esta
probablemente no es la mejor forma.

Sin embargo, vamos primero a crear un heightmap, o una representación del
terreno. Para hacer esto, voy a usar GIMP, pero puedes usar el editor que
te guste.

El heightmap (mapa de altura)
-----------------------------

Usaremos unas pocas funciones del editor de imágenes GIMP para producir
un simple heightmap. Ejecuta GIMP y crea una imagen cuadrada de
512x512 pixels.

.. image:: /img/1_GIMP_createImage512.png

Ahora te encuentras frente a una nueva imagen cuadrada y en blanco.

.. image:: /img/2_GIMP.png

Luego, usa un filtro para renderizar algunas nubes en este nueva imagen.

.. image:: /img/3_GIMP_FilterRenderClouds.png

Parametriza este filtro de la forma que gustes. Un pixel blanco
corresponde con el punto más alto del heightmap, un pixel negro
corresponde al más bajo. Entonces, las regiones más oscuras son
valles y las más brillantes montañas. Si quieres, puedes chequear
"tileable" para renderizar un heightmap que puede ser clonado y
embaldosados cerca uno del otro. El tamaño de X e Y no importa
mucho mientras que sea lo suficientemente grande para proveer
un terreno decente. Un valor de 4.0 o 5.0 para ambos está bien.
Haz clic en el botón "New Seed" para tirar el dado y GIMP va a
crear un nuevo heightmap aleatorio. Una vez que estés feliz con
el resultado, haz clic en "OK".

.. image:: /img/4_GIMP_Clouds.png

Puedes continuar editando tu imágen si deseas. Para nuestro ejemplo,
vamos a mantener el heightmap com oesta, y vamos a exportarlo como
un archivo PNG, como "heightmap.png". Guárdalo en tu carpeta de
proyecto de Godot.

La malla de plano
-----------------

Ahora, vamos a necesitar una malla de plano para importar en Godot.
Vamos a ejecutar Blender.

.. image:: /img/5_Blender.png

Remueve la malla de cubo inicial, luego agrega un nuevo plano a la escena.

.. image:: /img/6_Blender_CreatePlane.png

Haz un poco de zoom, luego cambia a Edit mode (tecla Tab) y en el
grupo de botones de herramientas en el lado izquierda, oprime "Subdivide"
5 o 6 veces.

.. image:: /img/7_Blender_subdivided.png

Tu malla ahora está subdividida, lo cual significa que agregamos vértices
a la malla de plano que luego podremos mover. El trabajo aun no finalizo:
para poder texturizar esta malla es necesario un mapa UV adecuado.
Actualmente, el mapa UV por defecto contiene solo los 4 vértices que
teníamos al principio. Sin embargo, ahora tenemos más, y queremos poder
texturizar correctamente sobre la malla entera.

Si todos los vértices de tu malla no están seleccionados, selecciónalos
todos (toca "A"). Deben aparecer anaranjados, no negros. Luego, en el grupo
de botones Shading/UVs a la izquierda, haz clic en el botón "Unwrap"
(o simplemente toca "U") y selecciona "Smart UV Project". Mantén las
opciones por defecto y dale "Ok".

.. image:: /img/8_Blender_UVSmart.png

Ahora, necesitamos cambiar nuestra vista a "UV/Image editor".

.. image:: /img/9_Blender_UV_editor.png

Selecciona todos los vértices de nuevo ("A") luego en el menú UV,
selecciona "Export UV Layout".


.. image:: /img/10_Blender_exportUV.png

Exporta el layout como un archivo PNG. Nómbralo "plane.png" y guárdalo
en tu carpeta de proyecto Godot. Ahora, vamos a exportar nuestra malla
como un archivo OBJ. En la parte superior de la pantalla, haz clic en
"File/Export/Wavefront (obj)". Guarda tu proyecto como "plane.obj" en
tu carpeta de proyecto Godot.

Magia de shaders
----------------

Ahora abramos el Editor Godot.

Crea un nuevo proyecto en la carpeta que creaste previamente y nómbralo
como quieras.

.. image:: /img/11_Godot.png

En nuestra escena por defecto (3D), crea un nodo raíz "Spatial". Luego,
importa el archivo de malla OBJ. Haz clic en "Import", selecciona
"3D Mesh" y selecciona tu archivo plane.obj, ajusta el camino de
destino como "/" (o lo que sea que tengas en tu carpeta de proyecto).

.. image:: /img/12_Godot_ImportMesh.png

Me gusta chequear "Normals" en la ventana emergente de importación de
modo que el importador también considere las normales de las caras,
lo que puede ser útil (aun si no las usamos en este tutorial). Tu malla
ahora es mostrada en el FileSystem en "res://".

.. image:: /img/13_Godot_ImportPopup.png


Crea un nodo MeshInstance. En el Inspector, carga la malla de recién
importamos. Selecciona "plane.msh" y dale OK.

.. image:: /img/14_Godot_LoadMesh.png

Genial! Nuestro plano esta ahora renderizado en la vista 3D.

.. image:: /img/15_Godot_MeshPlaneRendered.png

Es momento de agregar otras cosas de shaders. En el inspector, en la
línea "Material Override", agrega un "New ShaderMaterial". Edítalo al
hacer clic en el botón ">" justo a su derecha.

.. image:: /img/16_Godot_ShaderMaterial.png

Tienes dos formas de crear un shader: por código (MaterialShader), o
usando un shader graph (MaterialShaderGraph). La segunda es más visual,
pero no la cubriremos por ahora. Crea un "New MaterialShader".

.. image:: /img/17_Godot_newMaterialShader.png

Edítalo haciendo clic en el botón ">" justo a su derecha. El editor
de Shaders se abre.

.. image:: /img/18_Godot_ShaderEditorOpened.png

La pestaña Vertex es para el shader de vértice, y la pestaña Fragment
es para el shader de fragmento. No hay necesidad de explicar que hacen
ambos, correcto? Si no, ve a la página :ref:`doc_shading_language`.
De otra forma, empecemos con el Fragment shader. Este es usado para
texturizar el plano usando una imagen. Para este ejemplo, lo
texturizaremos con la imagen de heightmal propia, de forma que
veamos las montañas como regiones más brillantes y los cañones como
regiones más oscuras. Usa este código:

::

    uniform texture source;
    uniform color col;
    DIFFUSE = col.rgb * tex(source,UV).rgb;

Este shader es muy simple (en realidad viene de la página
:ref:`doc_shading_language`). Lo que hace básicamente es tomar 2
parámetros que tenemos que proveer desde fuera del shader ("uniform"):

-  el archivo de textura
-  un color
-  a color
   Luego, vamos a multiplicar cada pixel de la imagen por un
   ``tex(source, UV).rgb`` por el color definido ``col`` y lo ajustamos
   a la variable DIFFUSE, que es el color renderizado. Recuerda que la
   variable ``UV`` es una variable de shader que retorna la posición
   2D de la variable DIFFUSE, la cual es el color renderizado. Recuerda
   el pixel en la imagen de textura, de acuerdo al vértice con el que
   estamos tratando. Ese es el uso del UV Layout que hicimos antes.
   El color ``col`` no es en realidad necesario para mostrar la textura,
   pero es interesante jugar y ver lo que hace, correcto?

Sin embargo, el plano se muestra negro! Esto es porque no ajustamos el
archivo de textura y el color a usar.

.. image:: /img/19_Godot_BlackPlane.png

En el Inspector, haz clic en el botón "Previous" para ir atrás al
ShaderMaterial. Aquí es donde tu quieres ajustar la textura y el color.
En "Source", haz clic en "Load" y selecciona el archivo de textura
"heightmap.png". Pero la malla aun es negra! Esto es porque nuestro
Fragment shader multiplica cada valor de pixel de la textura por el
parámetro ``col``. Sin embargo, este color está actualmente ajustado
a negro (0,0,0), y como sabes, 0\*x = 0 ;) . Solo cambia el parámetro
``col`` a otro color para que tu textura aparezca.

.. image:: /img/20_Godot_TexturedPlane.png

Bien. ahora, el Vertex Shader.

El Vertex Shader es el primer shader en ser ejecutado. Trata con los
vértices.

Haz clic en la pestaña "Vertex" para cambiar, y pega este código:

::

    uniform texture source;
    uniform float height_range;
    vec2 xz = SRC_VERTEX.xz;
    float h = tex(source, UV).g * height_range;
    VERTEX = vec3(xz.x, h, xz.y);
    VERTEX = MODELVIEW_MATRIX *  VERTEX;

Este shader usa dos parámetros "uniform". El parámetro``source`` ya
esta ajustado para el fragment shader. Entonces, la misma imagen será
usada en este shader como el heightmap. El parámetro ``height_range``
es un parámetro que usaremos para incrementar el efecto de la altura.

En la línea 3, guardamos la posición x y z de el SRC_VERTEX, porque
no queremos que cambien : el plano debe permanecer cuadrado. Recuerda
que el eje Y corresponde a la "altura", la cual es lo único que
queremos cambiar con el heightmap.

En la línea 4, computamos una variable ``h`` al multiplicar el valor
de pixel por la posición UV y el ``height_range``. Como el heightmap
es una imagen en escala de grises, todos los canales r, g y b contienen
el mismo valor. Use ``g``, pero cualquiera de r, g y b tendría el
mismo efecto.

En la línea 5, ajustamos la posición actual del vértice a la posición
(xz.x, h, xz.y). sobre xz.y recuerda que su tipo es "vec2". Entonces,
sus componentes son x e y. El componente y simplemente contiene la
posición z que ajustamos en la línea 3.

Finalmente, en la línea 6, multiplicamos el vértice por la matriz
model/view de forma de ajustar su posición de acuerdo a la posición
de la cámara. Si tratas de comentar esta línea, veras que la malla
se comporta de forma extraña cuando mueves y rotas la cámara.

Eso esta todo bien, pero nuestro plano permanece chato. Esto es porque
el valor de ``height_range`` es 0. Incrementa dicho valor para observar
la malla distorsionarse y tomar la forma del terreno que ajustamos antes:

.. image:: /img/21_Godot_Fini.png
