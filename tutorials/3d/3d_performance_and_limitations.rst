.. _doc_3d_performance_and_limitations:

Rendimiento 3D y limitaciones
=============================

Introducción
~~~~~~~~~~~~

Godot sigue una filosofía de rendimiento balanceado. En el mundo del
rendimiento, siempre hay concesiones, que consisten en intercambiar
velocidad por usabilidad y flexibilidad. Algunos ejemplos prácticos son:

-  Renderizar objetos eficientemente en grandes cantidades es fácil, pero
   cuando una escena grande debe ser renderizada se puede volver
   ineficiente. Para resolver esto, la computación de la visibilidad
   debe ser agregado al renderizado, lo cual vuelve el renderizado menos
   eficiente, pero al mismo tiempo se renderizan menos objetos, entonces
   la eficiencia en su conjunto mejora.
-  Configurar las propiedades de cada material para cada objeto que
   necesita ser renderizado también es lento. Para resolver esto, los
   objetos son ordenados por material para reducir los costos, pero al
   mismo tiempo ordenarlos tiene un costo.
-  En física 3D sucede una situación similar. Los mejores algoritmos para
   manejar grandes cantidades de objetos físicos (como SAP) son muy lentos
   para insertar/remover objetos y hacer ray-casting. Los algoritmos que
   permiten inserción y remoción mas rápida, asi como ray-casting no serán
   capaces de manejar tantos objetos activos.

Y hay muchos ejemplos más de esto! Los motores de juegos se esfuerzan en
ser de propósito general por naturaleza, así que los algoritmos
balanceados siempre son favorecidos sobre algoritmos que pueden ser mas
rápidos en algunas situaciones y lentos en otras.. o algoritmos que son
rápidos pero vuelven la usabilidad más difícil.

Godot no es una excepción y, aunque está diseñado para tener backends
intercambiables para diferentes algoritmos, los que están por defecto (o
mejor dicho, los únicos que están allí por ahora) priorizan balance y
flexibilidad sobre rendimiento.

Con esto aclarado, el objetivo de este tutorial es explicar cómo obtener
la máxima performance de Godot.

Renderizado
~~~~~~~~~~~

El renderizado 3D es una de las áreas mas difíciles desde la cual obtener
rendimiento, así que esta sección tendrá una lista de consejos.

Reusar shaders y materiales
---------------------------

El renderizador de Godot es un poco diferente a lo que hay por ahí. Esta
diseñado para minimizar los cambios de estado de la GPU lo más posible.
:ref:`FixedMaterial <class_FixedMaterial>` hace un buen trabajo de
reusar materiales que necesitan shaders similares pero, si un shader
personalizado es usado, asegúrate de reusarlo lo mas posible.
Las prioridades de Godot serán las siguientes:

-  **Reusar Materiales**: Cuanto menor es la cantidad de materiales
   diferentes en una escena, mas rápido será el renderizado. Si una
   escena tiene una cantidad enorme de objetos (en los cientos o miles)
   intenta reusar los materiales o en el peor caso usa atlases.
-  **Reusar Shaders**: Si los materiales no pueden ser reusados, al menos
   intenta reusar los shaders (o FixedMaterials con diferentes parámetros
   pero misma configuración).

Si una escena tiene, por ejemplo, 20.000 objetos con 20.000 diferentes
materiales cada uno, el renderizado será realmente lento. Si la misma
escena tiene 20.000 objetos, pero apenas usa 100 materiales, el
renderizado será muy veloz.

Costo de pixel vs costo de vertex
---------------------------------

Es un pensamiento común que cuanto menor cantidad de polígonos en un
modelo, mas rápido será renderizado. Esto es *realmente* relativo y
depende de muchos factores.

En PC y consolas modernas, el costo de vertex es bajo. Muy bajo.
Las GPUs originalmente solo renderizaban triángulos, así que todos
los vértices:

1. Tenían que ser transformados por la CPU (incluyendo clipping).

2. Tenían que ser enviadas a la memoria del GPU desde la memoria RAM.

Hoy en día, todo esto es manejado dentro de la GPU, así que la
performance es extremadamente alta. Los artistas 3D usualmente tienen
el pensamiento incorrecto sobre la performance por cantidad de polígonos
debido a los DCCs 3D (como blender, Max, etc.) necesitan mantener la
geometría en la memoria CPU para poder ser editada, reduciendo la
performance real. La verdad es, un modelo renderizado por un motor 3D
es mucho más optimo que como se muestran por los DCCs 3D.

En dispositivos móviles, la historia es diferente. Las GPU de PCs y
consolas son monstruos de fuerza-bruta que pueden obtener tanta
electricidad como necesiten de la red eléctrica. Las GPUs móviles están
limitadas a una pequeña batería, por lo que necesitan ser mucho mas
eficiente con la energía.

Para ser más eficientes, las GPUs móviles intentan evitar el *overdraw*.
Esto significa, el mismo pixel en la pantalla siendo renderizado (con
cálculos de luces, etc.) más de una vez. Imagina una ciudad con varios
edificios, las GPUs realmente no saben que esta visible y que esta
oculto hasta que lo dibujan. Una casa puede estar dibujada y luego otra
casa frente de la primera (el renderizado sucedió dos veces para el
mismo pixel!). Las GPUs de PC normalmente no se preocupan mucho por
esto y solo agregan mas procesadores de pixels al hardware para aumentar
el rendimiento (pero esto también aumenta el consumo de poder).

En móvil, obtener más energía no es una opción, así que una técnica
llamada "Tile Based Rendering" es usada (prácticamente todo hardware
móvil usa una variante de dicha técnica), la cual divide la pantalla en
una cuadricula (grid). Cada celda mantiene la lista de triángulos que
se dibujan en ella y los ordena por profundidad para minimizar el
*overdraw*. Esta técnica mejora el rendimiento y reduce el consumo de
poder, pero impone una pena para el rendimiento vertex. Como resultado,
menos vértices y triángulos pueden ser procesados para dibujar.

Generalmente, esto no es tan malo, pero hay un caso para móvil que debe
ser evitado, que es tener objetos pequeños con mucha geometría dentro
de una pequeña porción de la pantalla. Esto fuerza al GPU móvil a poner
un gran esfuerzo en una sola celda de la pantalla, disminuyendo

Resumiendo, no te preocupes tanto por la cantidad de vértices en móvil,
pero evita la concentración de vértices en partes pequeñas de la pantalla.
Si, por ejemplo, un personaje, NPC, vehículo, etc. esta lejos (y luce
minúsculo), usa un modelo con menor nivel de detalle (LOD).

Una situación extra donde el costo de vértices debe ser considerado son
objetos que tienen procesamiento extra por vértice, como es:

-  Skinning (skeletal animation)
-  Morphs (shape keys)
-  Vertex Lit Objects (común en móvil)

Compresión de texturas
----------------------

Godot ofrece comprimir las texturas de los modelos 3D cuando se importan
(compresión VRAM). La compresión Video RAM no es tan eficiente en tamaño
como PNG o JPG al guardarse, pero aumenta enormemente el rendimiento
cuando se dibuja.

Esto es debido a que el objetivo principal de la compresión de textura es
la reducción del ancho de banda entre la memoria y la GPU.

En 3D, la forma de los objetos depende más de la geometría que la textura,
por lo que la compresión en general no es aparente. En 2D, la compresión
depende más de las formas dentro de las texturas, por lo que los
artefactos resultantes de la compresión es más aparente.

Como una advertencia, la mayoría de los dipositivos Android no soportan
compresión de texturas para texturas con transparencia (solo opacas),
así que ten esto en cuenta.

Objetos transparentes
---------------------

Como se mencionó antes, Godot ordena los objetos por material y shader
para mejorar el rendimiento. Esto, sin embargo, no puede ser hecho en
objetos transparentes. Los objetos transparentes son renderizados desde
el fondo al frente para hacer que la mezcla con lo que esta atrás
funcione. Como resultado, por favor trata de mantener los objetos
transparentes al mínimo! Si un objeto tiene una sección pequeña con
transparencia, trata de hacer esa sección un material separado.

Level of detail (LOD)
---------------------

Como se mencionó antes, usar objetos con menos vértices puede mejorar
el rendimiento en algunos casos. Godot tiene un sistema muy simple
para usar nivel de detalle, los objetos basados en
:ref:`GeometryInstance <class_GeometryInstance>` tienen un rango de
visibilidad que puede ser definido. Tener varios objetos
GeometryInstance en diferentes rangos funciona como LOD.

Usar instanciamiento (MultiMesh)
--------------------------------

Si varios objetos idénticos tienen que ser dibujados en el mismo lugar
o cerca, intenta usar :ref:`MultiMesh <class_MultiMesh>` en su lugar.
MultiMesh permite dibujar docenas de miles de objetos con muy poco
costo de rendimiento, haciéndolo ideal para bandadas, pasto, partículas,
etc.

Bake lighting
-------------

Las luces pequeñas usualmente no son un problema de rendimiento. Las
sombras un poco más. En general, si varias luces necesitan afectar la
escena, es ideal hacerles bake (:ref:`doc_light_baking`). Baking también
puede mejorar la calidad de la escena al agregar rebotes de luz
indirectos.

Si trabajas en móvil, hacer baking a las texturas es recomendado, dado
que este método es mas rápido.
