.. _doc_internationalizing_games:

Internacionalizando juegos
==========================

Introducción
------------

"It would be great if the world would speak just one language."
Desgraciadamente para nosotros desarrolladores, este no es el caso.
Aunque en general no es un requerimiento grande cuando se desarrollan
juegos indies o de nicho, es también muy común que los juegos que
van a ir a un mercado mas masivo requiera localización.

Godot ofrece muchas herramientas para hacer este proceso sencillo,
por lo que esto tutorial se parece a una colección de consejos y
trucos.

La localización es usualmente hecho por estudios específicos contratados
para hacer el trabajo y, a pesar de la enorme cantidad de software y
formatos de archivos disponibles para esto, la forma mas común de
localización hasta este día es con planillas. El proceso de crear las
planillas e importarlas ya esta cubierto en el tutorial :ref:`doc_importing_translations`,
por lo que este puede ser visto mas como una continuación de aquel.

Configurando la traducción importada
------------------------------------

Las traducciones puede ser actualizadas y re-importadas cuando cambian,
pero de todas formas deben ser agregadas al proyecto. Esto es hecho en
Escena > Configuración de Proyecto > Localización:

.. image:: /img/localization_dialog.png

Este dialogo permite agregar o quitar traducciones que afectan al
proyecto entero.

Recursos de localización
------------------------

También es posible ordenarle a Godot que abra versiones alternativas
de assets (recursos) dependiendo el lenguaje actual. Para esto existe
la pestaña "Remaps":

.. image:: /img/localization_remaps.png

Selecciona el recurso a ser re-mapeado, y las alternativas para cada
idioma

Convertir claves a texto
------------------------

Algunos controles como :ref:`Button <class_Button>`, :ref:`Label <class_Label>`,
etc. van a traer una traducción cada vez que sean ajustadas a una clave
en lugar de texto. Por ejemplo, si una etiqueta esta asignada a
"PANTALLA_PRINCIPAL_SALUDO1" y una clave para diferentes lenguajes
existe en las traducciones, esto será convertido automáticamente. Pero
este proceso es hecho durante la carga, por lo que si el proyecto en
cuestión tiene un dialogo que permite cambiar el lenguajes en los
ajustes, las escenas (o al menos la escena de ajustes) debe ser cargada
nuevamente para que el nuevo texto tenga efecto.
fect.

Por código, la función :ref:`Object.tr() <class_Object_tr>` puede ser
utilizada. Esto solo buscara el texto en las traducciones y
convertirlo si es encontrado:

::

    level.set_text(tr("NOMBRE_NIVEL_5"))
    status.set_text(tr("ESTADO_JUEGO_" + str(indice_estado)))

Haciendo controles que cambian de tamaño
----------------------------------------

El mismo texto en diferentes lenguajes puede variar de gran forma de
largo. Para esto, asegúrate de leer el tutorial en :ref:`doc_size_and_anchors`,
ya que tener controles que ajusten su tamaño dinámicamente puede ayudar.
Los :ref:`Container <class_Container>` pueden ser muy útiles, así como las
opciones múltiples en :ref:`Label <class_Label>` para acomodar el texto.


TranslationServer (Servidor de traducción)
---------------------------------------

Godot tiene un servidor para manejar la administración de traducción
de bajo nivel llamada :ref:`TranslationServer <class_TranslationServer>`.
Las traducciones pueden ser agregadas o quitadas en tiempo de ejecución,
y el lenguaje actual puede ser cambiado también.

Línea de Comando
----------------

El lenguaje puede ser probado cuando se corre Godot desde una línea de
comandos. Por ejemplo, para probar el juego en francés, los siguientes
argumentos son suministrados:

::

   c:\MiJuego> godot -lang fr

Traducir el nombre de proyecto.
-------------------------------

El nombre de proyecto se vuelve el nombre de la aplicación cuando se
exporta a diferentes sistemas y plataformas. Para especificar el
nombre de proyecto en mas de un lenguaje, crea un nuevo ajuste de
aplicación/nombre en el dialogo de configuración de proyecto y
agrégale el identificador de lenguaje. Por ejemplo:

.. image:: /img/localized_name.png

Como siempre, si no sabes el código de un lenguaje o zona, :ref:`chequea
la lista <doc_locales>`.
