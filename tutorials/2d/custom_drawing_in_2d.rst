.. _doc_custom_drawing_in_2d:

Dibujo personalizado en 2D
==========================

Porque?
----

Godot ofrece nodos para dibujar sprites, polígonos, partículas y todo
tipo de cosas. Para la mayoría de los casos esto es suficiente, pero
no siempre. Si se desea algo que no esta soportado, y antes de llorar
de miedo y angustia porque un nodo para dibujar ese-algo-especifico no
existe... seria bueno saber que es posible hacer que cualquier nodo 2D
(sea basado en :ref:`Control <class_Control>` o :ref:`Node2D <class_Node2D>`)
dibuje comandos personalizados. Es *realmente* fácil de hacer.

Pero...
-------

Dibujar personalizadamente de forma manual en un nodo es *realmente*
útil. Aquí algunos ejemplos de porque:

-  Dibujar formas o lógica que no esta manejada por nodos (ejemplos:
   hacer un nodo que dibuje un circulo, una imagen con estelas, un tipo
   especial de polígono animado, etc).
-  Las visualizaciones que no son tan compatibles con nodos: (ejemplo:
   una tabla de tetris). El ejemplo de tetris usa una función de dibujo
   personalizada para dibujar los bloques.
-  Gestionar la lógica de dibujo de una gran cantidad de objetos simples
   (en los cientos de miles). Usar mil nodos probablemente ni cerca tan
   eficiente como dibujar, pero mil llamadas de dibujo son baratas.
   Chequea el demo "Shower of Bullets" como ejemplo.
-  Hacer un control de UI personalizado. Hay muchos controles
   disponibles, pero es fácil llegar a la necesidad de hacer uno nuevo,
   personalizado.

OK, como?
--------

Agrega un script a cualquier nodo derivado de :ref:`CanvasItem <class_CanvasItem>`,
como :ref:`Control <class_Control>` o :ref:`Node2D <class_Node2D>`.
Sobrescribe la función _draw().

::

    extends Node2D

    func _draw():
        #Tus comandos de dibujo aquí
        pass

Los comandos de dibujo son descritos en la referencia de clase
:ref:`CanvasItem <class_CanvasItem>`. Hay muchos de ellos.

Actualizando
------------

La función _draw() es solo llamada una vez, y luego los comandos de
dibujo son cacheados y recordados, por lo que llamadas siguientes no
son necesarias.

Si se necesita redibujar porque un estado o algo cambio, solo llama
:ref:`CanvasItem.update() <class_CanvasItem_update>` en ese mismo nodo
y una nueva llamada a _draw() se realizara.

Aquí hay un ejemplo algo mas complejo. Una variable de textura que
será redibujada si cambia:

::

    extends Node2D

    var texture setget _set_texture

    func _set_texture(value):
        #si la variable textura es modificada externamente,
        #esta llamada de retorno es realizada.
        texture=value #La textura ha cambiado
        update() #actualiza el nodo

    func _draw():
        draw_texture(texture,Vector2())

En algunos casos, puede deseare dibujar cada frame. Para esto, solo
llama a update() desde la llamada de retorno _process(), asi:

::

    extends Node2D

    func _draw():
        #Tus comandos aquí
        pass

    func _process(delta):
        update()

    func _ready():
        set_process(true)

Un ejemplo: dibujar arcos circulares
------------------------------------

Ahora usaremos la funcionalidad personalizada de dibujo del Motor Godot
para dibujar algo que Godot no provee como funciones. Como un ejemplo,
Godot provee una función draw_circle() que dibuja un circulo completo.
Sin embargo, que hay sobre dibujar una porción de un circulo? Vas a
necesitar programar una función para hacer esto, y dibujarla tu mismo.

Función Arc (Arco)
^^^^^^^^^^^^^^^^^^

Un arco se define por sus parámetros soporte del circulo, esto es: la
posición del centro, y el radio. El arco en si mismo se define por
el ángulo donde empieza, y el ángulo donde termina. Estos son los 4
parámetros que tenemos que proveer a nuestro dibujo. También proveeremos
el valor de color asi podemos dibujar el arco en diferentes colores si
lo deseamos.

Básicamente, dibujar una forma en la pantalla requiere que sea
descompuesta en un cierto numero de puntos unidos uno a uno con el
siguiente. Como puedes imaginar, cuanto mayor cantidad de puntos tenga
tu forma, mas suave aparecerá, pero mas pesada será en términos de costo
de procesador. En general, si tu forma es enorme (o en 3D, cercana a la
cámara), requerirá mas puntos para ser dibujado sin verse angular. Por
el contrario, si tu forma es pequeña (o en 3D, lejana de la cámara),
tu puedes reducir su numero de puntos y ahorrar costo de procesamiento.
Esto se llama *Level of Detail (LoD)*. En nuestro ejemplo, vamos
simplemente a usar un numero fijo de puntos, no importando el radio.

::

    func draw_circle_arc( center, radius, angle_from, angle_to, color ):
        var nb_points = 32
        var points_arc = Vector2Array()

        for i in range(nb_points+1):
            var angle_point = angle_from + i*(angle_to-angle_from)/nb_points - 90
            var point = center + Vector2( cos(deg2rad(angle_point)), sin(deg2rad(angle_point)) ) * radius
            points_arc.push_back( point )

        for indexPoint in range(nb_points):
            draw_line(points_arc[indexPoint], points_arc[indexPoint+1], color)

Recuerdas el numero de puntos en que nuestra forma tiene que ser
descompuesta? Fijamos este numero en la variable nb_points al valor
de 32. Luego, inicializamos un Vector2Array vacío, el cual es
simplemente un arreglo de tipo Vector2.

El siguiente paso consiste en computar las posiciones actuales de estos
32 puntos que componen el arco. Esto es hecho en el primer for-loop:
iteramos sobre el numero de puntos con los que queremos computar las
posiciones, mas uno para incluir el ultimo punto. Primero determinamos
el ángulo de cada punto, entre los ángulos de comienzo y fin.

La razón por la cual cada ángulo es reducido 90º es que vamos a computar
posiciones 2D a partir de cada ángulo usando trigonometría (ya sabes,
cosas de coseno y seno...). Sin embargo, para ser simple, cos() y sin()
usan radianes, no grados. El ángulo de 0º (0 radian) empieza a las 3 en
punto, aunque queremos empezar a contar a las 0 en punto. Entonces, vamos
a reducir cada ángulo 90º para poder empezar a contar desde las 0 en
punto.

La posición actual de un punto localizado en un circulo a un ángulo
'angle' (en radianes) es dada por Vector2(cos(angle), sen(angle)). Ya
que cos() y sin() regresa valores entre -1 y 1, la posición esta
localizada en un circulo de radio 1. Para tener esta posición en nuestro
circulo de soporte, el cual tiene radio 'radius', debemos simplemente
multiplicar la posición por 'radius'. Finalmente, necesitamos posicionar
nuestro circulo de soporte en la posición 'center', lo cual es hecho
al agregarlo a nuestro valor Vector2. Finalmente, insertamos el punto
en el Vector2Array que fue previamente definido.

Ahora, necesitamos dibujar los puntos. Como puedes imaginar, no vamos
simplemente a dibujar nuestros 32 puntos: necesitamos dibujar todo lo
que esta entre medio de ellos. Podríamos haber computado cada punto
nosotros mismos usando el método previo, y dibujarlos uno por uno, pero
es muy complicado e ineficiente (a no ser que sea realmente necesario).
Así que, vamos simplemente a dibujar líneas entre cada par de puntos.
A no ser que el radio de nuestro circulo de soporte sea muy grande, el
largo de cada línea entre un par de puntos nunca será suficientemente
largo para verlos todos. Si esto sucede, solo tendríamos que incrementar
el numero de puntos.


Dibujar el arco en la pantalla
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

Ahora tenemos una función que dibuja cosas en la pantalla, es tiempo de
llamarla en la función _draw().

::

    func _draw():
        var center = Vector2(200,200)
        var radius = 80
        var angle_from = 75
        var angle_to = 195
        var color = Color(1.0, 0.0, 0.0)
        draw_circle_arc( center, radius, angle_from, angle_to, color )

Result:

.. image:: /img/result_drawarc.png

Función arco polígono
^^^^^^^^^^^^^^^^^^^^^

Podemos llevar esto un paso mas y escribir una función que dibuja la
porción llana del disco definido por el arco, no solo la forma. Este
método es exactamente el mismo que el previo, excepto que dibujamos
un polígono en lugar de líneas:

::

    func draw_circle_arc_poly( center, radius, angle_from, angle_to, color ):
        var nb_points = 32
        var points_arc = Vector2Array()
        points_arc.push_back(center)
        var colors = ColorArray([color])

        for i in range(nb_points+1):
            var angle_point = angle_from + i*(angle_to-angle_from)/nb_points - 90
            points_arc.push_back(center + Vector2( cos( deg2rad(angle_point) ), sin( deg2rad(angle_point) ) ) * radius)
        draw_polygon(points_arc, colors)


.. image:: /img/result_drawarc_poly.png

Dibujo personalizado dinámico
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

Bien, ahora somos capaces de dibujar cosas personalizadas en la pantalla.
Sin embargo, es muy estático: hagamos que esta forma gire alrededor
de su centro. La solución para hacer esto es simplemente cambiar los
valores angle_from y angle_to a lo largo del tiempo. Para nuestro
ejemplo, vamos a incrementarlos en 50. Este valor de incremento tiene
que permanecer constante, de lo contrario la velocidad de rotación
cambiara de forma acorde.

Primero, tenemos que hacer ambos angle_from y angle_to variables
globales al comienzo del script. También toma nota que puedes guardarlos
en otros nodos y accederlo con get_node().

::

 extends Node2D

 var rotation_ang = 50
 var angle_from = 75
 var angle_to = 195

Hacemos que estos valores cambien en la función _process(delta). Para
activar esta función, necesitamos llamar set_process(true) en la función
_ready().

También incrementamos aquí nuestros valores angle_from y angle_to. Sin
embargo, no debemos olvidar hacer wrap() a los valores resultantes
entre 0º y 360º! Esto es, si el ángulo es 361º, entonces en realidad
es 1º. Si no haces wrap de estos valores, el script funcionara
correctamente pero los valores de ángulo crecerán mas y mas en el
tiempo, hasta llegar al máximo valor entero que Godot puede manejar
(2^31 - 1). Cuando esto sucede, Godot puede colgarse o producir
comportamiento no esperado. Como Godot no provee una función wrap(),
vamos a crearla aqui, ya que es relativamente simple.

Finalmente, no debemos olvidar llamar la función update(), que de forma
automática llama a _draw(). De esta forma, puedes controlar cuando
quieres refrescar el frame.

::

 func _ready():
     set_process(true)

 func wrap(value, min_val, max_val):
     var f1 = value - min_val
     var f2 = max_val - min_val
     return fmod(f1, f2) + min_val

 func _process(delta):
     angle_from += rotation_ang
     angle_to += rotation_ang

     # solo hacemos wrap si los angulos son mayores que 360
     if (angle_from > 360 && angle_to > 360):
         angle_from = wrap(angle_from, 0, 360)
         angle_to = wrap(angle_to, 0, 360)
     update()

También, no olvides modificar la función _draw() para hacer uso de estas
variables:

 func _draw():
	var center = Vector2(200,200)
	var radius = 80
	var color = Color(1.0, 0.0, 0.0)

	draw_circle_arc( center, radius, angle_from, angle_to, color )

Corrámoslo!
Funciona, pero el arco gira extremadamente rápido! Que salió mal?

La razón es que tu GPU esta mostrando frames tan rápido como puede.
Necesitamos "normalizar" el dibujo por esta velocidad. Para lograrlo,
tenemos que hacer uso del parámetro 'delta' en la función _process().
'delta' contiene el tiempo transcurrido entre los dos últimos frames
mostrados. En general es pequeño (unos 0.0003 segundos, pero esto depende
de tu hardware). Por lo que, usar 'delta' para controlar tu dibujado
asegura a tu programa correr a la misma velocidad en todo hardware.

En nuestro caso, vamos a necesitar multiplicar nuestra variable
'rotation_angle' por 'delta' en la funcion _process(). De esta forma,
nuestros 2 ángulos se incrementaran por un valor mucho menor, el
cual depende directamente de la velocidad de renderizado.


::

 func _process(delta):
     angle_from += rotation_ang * delta
     angle_to += rotation_ang * delta

     # solo hacemos wrap en los ángulos si ambos son mayores de 360
     if (angle_from > 360 && angle_to > 360):
         angle_from = wrap(angle_from, 0, 360)
         angle_to = wrap(angle_to, 0, 360)
     update()

Corrámoslo nuevamente! Esta vez, la rotación se muestra bien!

Herramientas
------------

Dibujar tus propios nodos puede también ser deseable mientras los corres
dentro del editor, para usar como pre visualización o visualización de
alguna característica o comportamiento.

Recuerda solo usar la palabra clave "tool" en la parte superior del
script (chequea la referencia :ref:`doc_gdscript` si olvidaste que logra
esto)
