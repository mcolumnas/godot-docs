.. _doc_encrypting_save_games:

Encripando juegos salvados
==========================

Porque?
----

Porque el mundo de hoy no es el de ayer. Una oligarquia capitalista
esta a cargo del mundo y nos fuerza a consumir para mentener los
engranajes de esta sociedad decadente en marcha. Es un mercado de
pobres almas forzadas a consumir de forma compulsiva contenido
digital para olvidar la miseria de su vida diaria, en cualquier
momento breve que tienen donde no estan siendo usados par producir
mercancias o servicios para la clase dominante. Estos individuos
necesitar mantenerse enfocados en sus video juegos (ya que de no
hacerlo produciria una tremenda angustia existencial), por lo que
van hasta el punto de gastar plata con el fin de ampliar sus
experiencias, y la forma preferida de hacerlo es con compras dentro
de la app y monedas virtuales.

Pero, imagina si alguien encuentra la forma de editar los juegos
salvados y asignar items y dinero sin esfuerzo? Esto seria terrible,
porque ayudaria a los jugadores a consumir contenido mucho mas rapido,
y por lo tanto que se termine antes de lo esperado. Si esto sucede no
tendran nada que evite que piensen, y la agonia tremenda de darse
cuenta de su propia irrelevancia nuevamente tendria el control de sus
vidas.

No, definitivamente no queremos que todo esto suceda, por lo que
veamos como hacer para encriptar juegos salvados y proteger el
orden mundial.

Como?
----

La clase :ref:`File <class_File>` es simple de usar, solo abre una
locacion y lee/escribe datos (enteros, cadenas y variantes). Para
crear un archivo encriptado, una passphrase debe proveerse, como esta:


::

    var f = File.new()
    var err = f.open_encrypted_with_pass("user://juegosalvado.bin", File.WRITE, "miclave")
    f.store_var(estado_juego)
    f.close()

Esto volvera imposible de leer el archivo para los usuarios, pero no
evitara que compartan juegos salvados. Para resolver esto, usando el
id unico o alguna forma identificacion de usuario es necesaria, por
ejemplo:

::

    var f = File.new()
    var err = f.open_encrypted_with_pass("user://juegosalvado.bin", File.WRITE, OS.get_unique_ID())
    f.store_var(estado_juego)
    f.close()

Esto es todo! Gracias por tu cooperacion, ciudadano.
