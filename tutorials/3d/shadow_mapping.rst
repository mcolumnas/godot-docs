.. _doc_shadow_mapping:

Shadow mapping (Mapeo de sombras)
=================================

Introducción
------------

Simplemente tirar una luz no es suficiente para iluminar realísticamente
una escena. Debería ser, en teoría, pero debido a la forma en la que el
hardware de video funciona, partes de objetos que no deberían ser
alcanzados por la luz son iluminados de todas formas.

La mayoría de la gente (incluyendo artistas), ven las sombras como algo
proyectado por la luz, como si fueran creados por la misma luz al
oscurecer lugares que están escondidos del origen de luz.

Esto en realidad no es correcto y es importante entender que las sombras
son lugares donde la luz simplemente no llega. Como regla (y sin contar
la luz indirecta) si una luz es apagada, los lugares donde hay sombras
deberían permanecer igual. En otras palabras, las sombras no deberían
ser vistas como algo "agregado" a la escena, pero como un área que
"permanece oscura".

Todos los tipos de luces en Godot pueden usar shadow mapping, y todos
soportan varias técnicas diferentes que intercambian calidad por
rendimiento. Shadow mapping usa una textura que guarda la "vista de
profundidad" de la luz y chequea contra ella en tiempo real para cada
pixel que renderiza.

Cuanto mas grande la resolución de la textura shadow map, mayor el
detalle que la sombra tiene, pero consumirá mas memoria de video y
ancho de banda (lo cual significa que los cuadros por segundo bajan).

Sombras por tipo de luz
-----------------------

Sombras para Directional lights
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

Directional lights pueden afectar un área realmente grande. Cuanto mas
grande la escena, mayor el área afectada. Dado que la resolución del
shadow map se mantiene igual, la misma cantidad de pixeles de sombra
cubren un área mayor, resultando en sombras en bloques. Muchas técnicas
existen para tratar con problemas de resolución, pero la mas común es
PSSM (Parallel Split Shadow Maps):

.. image:: /img/shadow_directional.png

Esta técnica divide la vista en 2 o 4 secciones, y una sombra es
renderizada para cada uno. De esta forma, los objetos cercanos pueden
usar sombras mas grandes mientras que cuando mas lejano el objeto usara
una con menos detalle, pero en proporción esto parece hacer que el
tamaño del shadow map crezca cuando en realidad se mantiene igual.
Por supuesto, esta técnica no es gratis, cuanto mas divisiones mas
bajara el rendimiento. Para mobile, es generalmente inconveniente
usar mas de 2 secciones.

Una técnica alternativa es PSM (Perspective Shadow Mapping). Esta técnica
es mucho mas barata que PSSM (tan barata como ortogonal), pero solo
funciona realmente para unos pocos ángulos de cámara respecto a la luz.
En otras palabras, PSM solo es util para juegos donde la dirección de la
cámara y luz son ambas fijas, y la luz no es paralela a la cámara (que
es cuando PSM se rompe por completo).

Sombras para Omni light
~~~~~~~~~~~~~~~~~~~~~~~

Las luces omnidireccionales también son problemáticas. Como representar
360 grados de luz con una sola textura? Hay dos alternativas, la primera
es usar DPSM (Dual Paraboloid Shadow Mapping). Esta técnica es rápida,
pero requiere la utilización de DISCARD (lo que la vuelve no muy útil
para mobile). DPSM también puede lucir mal si la geometría no tiene
suficiente tessellated, por lo que mas vértices pueden ser necesarios
si no luce correcto. La segunda opción es simplemente no usar un shadow
map, y usar un shadow cubemap. Esto es mas rápido, pero requiere seis
pasadas para renderizar en todas las direcciones y no es soportado en
el renderer actual (GLES2).

.. image:: /img/shadow_omni.png

Algunas consideraciones cuando se usan shadow mas DPSM:

-  Mantén Slope-Scale en 0.
-  Usa un valor pequeño para Z-Offset, si luce mal, redúcelo mas.
-  ESM filtering puede mejorar como luce.
-  Las juntas entre las dos mitades de la sombra son generalmente
   perceptibles, así que rota la luz para hacer que se vean menos.

Sombras Spot light
~~~~~~~~~~~~~~~~~~

Las sombras spot light son generalmente las mas simples, solo necesitan
una única textura y sin técnicas especiales.

.. image:: /img/shadow_spot.png

Parámetros de sombras
---------------------

El hecho de que las sombras son en realidad una textura puede generar
varios problemas. El mas común es Z fighting (líneas en los bordes de
los objetos que proyectan las sombras). Hay dos formas de solucionar
esto, la primera es ajustar los parámetros offset, y la segunda es usar
un algoritmo de filtración de sombras, que generalmente luce mejor y no
tiene tantos problemas técnicos, pero consume mas GPU.

Ajustando z-offset
~~~~~~~~~~~~~~~~~~

Entonces, te decidiste a ir con sombras no filtradas porque son mas
rápidas, necesitas un poco mas de detalle o tal vez es que te gustan las
sexy líneas exteriores de sombras con forma de sierra porque te recuerdan
a tus juegos favoritos de generación anterior. La verdad es que, esto
puede ser un dolor, pero la mayoría de las veces puede ser ajustado para
tener lindos resultados. No hay número mágico y cualquiera sea el
resultado que obtengas será diferente de escena a escena, necesita un
tiempo de ajuste. Vayamos paso por paso.

El primer paso es encender las sombras, asumamos que ambos Z-Offset y
Z-Slope-Scale estan en 0. Esto se presentara:

.. image:: /img/shadow_offset_1.png

Rayos, la sombra esta por todos lados y con problemas técnicos extremos!
Esto sucede porque la sombra esta peleando con la misma geometría que
la esta proyectando. Esto es llamado "self-shadowing". Para evitar esto
no tiene sentido luchar, te darás cuenta que necesitas la paz entre la
sombra y la geometría, asi que empujas la sombre un poco al incrementar
el Z-Offset de la sombra. Esto mejora las cosas un montón:

.. image:: /img/shadow_offset_2.png

Pero aun no es perfecto, self-shadowing no desapareció por completo.
Estamos cerca de la perfección pero aun no la logramos.. así que en un
impulso de codicia aumentas el Z-Offest aún más!

.. image:: /img/shadow_offset_3.png

Y logra eliminar esos self-shadowings! Hooray! Excepto que algo esta
mal.. oh, bueno. Al ser empujadas demasiado, las sombras empiezan a
desconectarse de sus emisores, lo cual luce bastante feo. Ok, vas de
nuevo al Z-Offset previo.

Aquí es cuando Z-Slope-Scale viene a salvar el día. Este ajusta hace
mas delgados a los objetos emisores de sombras, entonces los bordes
no sufren self-shadow:

.. image:: /img/shadow_offset_4.png

Aha! Finalmente algo que luce aceptable. Es perfectamente aceptable y
tu puedes perfectamente sacar un juego que luce como esto (imagina que
estas mirando calidad de arte de Final Fantasy, no este horrible intento
de modelado 3D). Puede haber muy pequeñas partes aun con self-shadowing
que a nadie le importa, así que tu codicia interminable vuelve a aparecer
y aumentas nuevamente Z-Slope-Scale:


.. image:: /img/shadow_offset_5.png

Bueno, eso fue mucho, las sombras emitidas son demasiado finas y ya no
lucen bien. Mala suerte, el ajuste previo era bueno de todas
formas, aceptemos que la perfección no existe y vayamos por algo mas.

Importante!
~~~~~~~~~~

Si estas usando shadow maps con directional lights, asegúrate que la
*view distance* de la *camara* esta ajustada en *optimal range*. Esto
implica que si la distancia entre tu cámara y el final visible de la
escena es 100, entonces ajusta la view distance a ese valor. Si un
valor mas grande que el necesario es usado, los shadow maps van a perder
detalle ya que intentaran cubrir un área mayor.

Así que, siempre asegúrate de usar el rango optimo!

Filtrado de sombras
~~~~~~~~~~~~~~~~~~~

Las sombras crudas tienen bloques. Aumentar su resolución solo vuelve
los bloques más pequeños, pero aún son bloques.

Godot ofrece algunas formas de filtrarlas (la sombre en este ejemplo es
de baja resolución a propósito!):

.. image:: /img/shadow_filter_options.png

PCF5 y PCF13 son filtros textura-espacio simples. Harán la textura un
poco mas aceptable pero aun necesitara una resolución considerable para
lucir bien.

ESM es un filtro más complejo y tiene algunos parámetros de ajuste. ESM
usa shadow blurring (la cantidad de blur passes y multiplier pueden ser
ajustados).
