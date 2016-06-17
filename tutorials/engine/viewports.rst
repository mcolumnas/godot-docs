.. _doc_viewports:

Viewports (Visores)
===================

Introducción
------------

Godot tiene una característica pequeña pero útil llamada viewports.
Los Viewports son, como su nombre implica, rectángulos donde el
mundo es dibujado. Tienen 3 usos principales, pero pueden ser
flexiblemente adaptados para hacer mucho mas. Todo esto se hace con
el nodo :ref:`Viewport <class_Viewport>`.

.. image:: /img/viewportnode.png

Los principales usos de los que hablábamos son:

-  **Scene Root**: Las raiz de la escena activa siempre es un viewport.
   Esto es lo que muestra las escenas creadas por el usuario. (Deberías
   saber esto de los tutoriales previos!)
-  **Sub-Viewports**: Estos pueden ser creados cuando un Viewport es
   hijo de un :ref:`Control <class_Control>`.
-  **Render Targets**:: Viewports pueden ser ajustadas en el modo
   "RenderTarget". Esto implica que el viewport no es visible
   directamente, pero sus contenidos pueden ser accedidos con una
   :ref:`Texture <class_Texture>`.

Entrada
-------

Los viewports también son responsables de entregar eventos de entrada
correctamente ajustados y escalados a todos sus nodos hijos. Ambos el
root del viewport y sub-viewports hacen esto automáticamente, pero los
rendertargets no. Por este motivo, es usuario debe hacerlo manualmente
con la funcion :ref:`Viewport.input() <class_Viewport_input>` si es necesario.

Listener (Oyente)
--------

Godot soporta sonido 3D (tanto en nodos 2D como 3D), mas sobre esto
puede ser encontrado en otro tutorial (un día..). Para que este tipo
de sonido sea audible, el viewport necesita ser habilitado como un
listener (para 2D o 3D). Si estas usando un viewport para mostrar tu
mundo, no olvides de activar esto!

Cámaras (2D y 3D)
-----------------

Cuando se usa una :ref:`Camera <class_Camera>` :ref:`Camera2D <class_Camera2D>`
2D o 3D, las cámaras siempre mostraran en el viewport padre mas cercano
(yendo hacia root).  Por ejemplo, en la siguiente jerarquía:

-  Viewport

   -  Camera

Las cámaras se mostraran en el viewport padre, pero en el siguiente orden:

-  Camera

   -  Viewport

No lo hará (o puede mostrarse en viewport root si es una subescena).

Solo puede haber una cámara activa por viewport, por lo que si hay mas
de una, asegúrate que la correcta tiene la propiedad "current" ajustada,
o hazlo llamando:

::

    camera.make_current()

Escala y estiramiento
---------------------

Los viewports tienen la propiedad "rect". X e Y no son muy usados (
solo el viewport root realmente los utiliza), mientras WIDTH y
HEIGHT representan el tamaño del viewport en pixels. Para sub-viewports,
estos valores son sobrescritos por los del control padre, pero para
render targets ajusta su resolución.
)

También es posible escalar el contenido 2D y hacerle creer que la
resolución del viewport es otra que la especificada en el rect,
al llamar:

::

    viewport.set_size_override(w,h) #tamaño personalizado para 2D
    viewport.set_size_override_stretch(true/false) #habilita el estiramiento para tamaño personalizado

El viewport root usa esto para las opciones de estiramiento en la
configuración del proyecto.

Worlds (Mundos)
------

Para 3D, un viewport contendrá :ref:`World <class_World>`. Este es
básicamente el universo que une la física y la renderización. Los
nodos Spatial-base serán registrados usando el World del viewport
mas cercano. Por defecto, los viewports recientemente creados no
contienen World pero usan el mismo como viewport padre (pero el
viewport root no tiene uno, el cual es donde los objetos son
renderizados por defecto). World puede ser ajustado en un viewport
usando la propiedad "world", y eso separara todos los nodos hijos
de ese viewport de interactuar con el viewport world padre. Esto
es especialmente útil en escenarios donde, por ejemplo, puedes querer
mostrar un personaje separado en 3D impuesto sobre sobre el juego (como
en Starcraft).

Como un ayudante para situaciones donde puedes querer crear viewports
que muestran objetos únicos y no quieres crear un mundo, viewport tiene
la opción de usar su propio World. Esto es muy útil cuando tu quieres
instanciar personajes 3D u objetos en el mundo 2D.

Para 2D, cada viewport siempre contiene su propio :ref:`World2D <class_World2D>`.
Esto es suficiente en la mayoría de los casos, pero si se desea
compartirlo, es posible hacerlo al llamar la API viewport manualmente.

Captura
-------

Es posible requerir una captura de los contenidos del viewport. Para
el viewport root esto es efectivamente una captura de pantalla. Esto es
hecho con la siguiente API:

::

    # encola una captura de pantalla, no sucederá inmediatamente
    viewport.queue_screen_capture()

Luego de un frame o dos (check_process()), la captura estará lista,
obtenla usando:

::

    var capture = viewport.get_screen_capture()

Si la imagen retornada esta vacía, la captura aun no sucedió, espera
un poco mas, esta API es asincrónica.

Sub-viewport
------------

Si el viewport es hijo de un control, se volverá activa y mostrara
lo que tenga dentro. El diseño es algo como esto:

-  Control

   -  Viewport

El viewport cubrirá el área de su control padre completamente.

.. image:: /img/subviewport.png

Render target (objetivo de renderizacion)
-------------

Para ajustar como un render target, solo cambia la propiedad "render
target" de el viewport a habilitado. Ten en cuenta que lo que sea que
esta dentro no será visible en el editor de escena. Para mostrar el
contenido, la textura de render target debe ser usada. Esto puede ser
pedido con código usando (por ejemplo):

::

    var rtt = viewport.get_render_target_texture()
    sprite.set_texture(rtt)

Por defecto, la re-renderizacion del render target sucede cuando la
textura del render target ha sido dibujada en el frame. Si es visible,
será renderizada, de otra contrario no lo será. Este comportamiento
puede ser cambiado a renderizado manual (una vez), o siempre renderizar,
no importando si es visible o no.

Algunas clases son creadas para hacer esto mas sencillo en la mayoría de
los casos dentro del editor:

-  :ref:`ViewportSprite <class_ViewportSprite>` (para 2D).

Asegúrate de chequear los demos de viewports! La carpeta viewport en
el archivo disponible para descarga en el sitio principal de Godot, o
https://github.com/godotengine/godot/tree/master/demos/viewport
