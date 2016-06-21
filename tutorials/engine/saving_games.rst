.. _doc_saving_games:

Salvando juegos
================

Introduccion
------------

Salvar juegos puede ser complicado. Puede desearse guardar mas
informacion que la que el nivel actual o numero de estrellas
ganadas en un nivel. Juegos salvados mas avanzados pueden necesitar
guardar informacion adicional sobre un numero arbitrario de objetos.
Esto permitira que la funcion de salvar sea escalada en la medida
que el juego crece en complejidad.

Identificar objetos persistentes
--------------------------------

Primeor deberiamos identificar que objetos queremos mantener entre
sesiones y que informacion queremos mantener de esos objetos. Para
este tutorial, vamos a usar grupos para marcar y manipular objetos
para ser salvadospero otros metodos son ciertamente posibles.

Vamos a empezar agregando objetos que queremos salvar al grupo
"Persist". Como en el tutorial :ref:`doc_scripting_continued`,
podemos hacer esto a traves de GUI o de script. Agreguemos los nodos
relevantes usando la GUI:

.. image:: /img/groups.png

Una vez que esto esta hecho vamos a necesitar salvar el juego, podemos
obtener todos los objetos para salvarlos y luego decirle a todos ellos
que salven con este script:

::

    var nodossalvados = get_tree().get_nodes_in_group("Persist")
        for i in nodossalvados:
        # Ahora podemos llamar nuestra funcion de salvar en cada nodo.

Serializando
-----------

El siguiente paso es serializar los datos. Esto vuelve mucho mas
sencillo leer y guardar en disco. En este caso, estamos asumiendo que
cada miembro del grupo Persist es un nodo instanciado y por lo tanto
tiene path. GDScript tiene funciones de ayuda para esto, como
:ref:`Dictionary.to_json() <class_Dictionary_to_json>` y
:ref:`Dictionary.parse_json() <class_Dictionary_parse_json>`,
asi que usaremos este diccionario. Nuestro nodo necesita contener una
funcion de salvar para que regresas estos datos. La funcion de salvar
sera similar a esto:

::

    func salvar():
        var salvardicc = {
            nombrearchivo=get_filename(),
            padre=get_parent().get_path(),
            posx=get_pos().x, #Vector2 is not supported by json
            posy=get_pos().y,
            ataque=ataque,
            defensa=defensa,
            saludactual=saludactual,
            saludmaxima=saludmaxima,
            daño=daño,
            regen=regen,
            experiencia=experiencia,
            TNL=TNL,
            nivel=nivel,
            ataquecrece=ataquecrece,
            defensacrece=defensacrece,
            saludcrece=saludcrece,
            estavivo=estavivo,
            ultimo_ataque=ultimo=ataque
            }
        return salvardicc

Esto nos da un diccionario con el estilo ``{ "nombre_variable":valor_esa_variable }``
el cual puede ser util para cargar.

Salvar y leer datos
-------------------

Como cubrimos en el tutorial :ref:`doc_filesystem`, necesitaremos abrir
un archivo y escribirlo y luego leer de el. Ahora que tenemos una forma
de llamar nuestros grupos y obtener los datos relevantes, vamos a usar
to_json() para convertirlos en un string que sea facilmente guardado y
lo grabaremos en un archivo. Haciendo esto asegura que cada linea es su
propio objeto de forma que tenemos una forma facil de obtener los datos
desde el archivo tambien.

::

    # Nota: Esto puede ser llamado desde cualquier parte dentro del arbol. Esta funcion es independiente del camino.
    # Ve a traves de todo en la categoria persistente y pideles el regreso de un diccionario de variables relevantes

    func salvar_juego():
        var juegosalvado = File.new()
        juegosalvado.open("user://juegosalvado.save", File.WRITE)
        var salvanodos = get_tree().get_nodes_in_group("Persist")
        for i in salvanodos:
            var datodenodo = i.save()
            juegosalvado.store_line(nodedata.to_json())
        juegosalvado.close()

Juego salvado! Cargarlo es bastante simple tambien. Para eso
vamos a leer cada linea, usar parse_json() para leerlo de nuevo a un
diccionario, y luego iterar sobre el diccionario para leer los valores.
Pero primero necesitaremos crear el objeto y podemos usar el nombre de
archivo y valores del padre para lograrlo. Aqui esta nuestra funcion de
carga:

::

    # Nota: Esto puede ser llamado desde cualqueir lugar dentro del
    # arbol. Esta funcion es independiente del camino.

    func load_game():
        var savegame = File.new()
        if !savegame.file_exists("user://savegame.save"):
            return #Error!  We don't have a save to load

        # We need to revert the game state so we're not cloning objects during loading.  This will vary wildly depending on the needs of a project, so take care with this step.
        # For our example, we will accomplish this by deleting savable objects.
        var savenodes = get_tree().get_nodes_in_group("Persist")
        for i in savenodes:
            i.queue_free()

        # Load the file line by line and process that dictionary to restore the object it represents
        var currentline = {} # dict.parse_json() requires a declared dict.
        savegame.open("user://savegame.save", File.READ)
        while (!savegame.eof_reached()):
            currentline.parse_json(savegame.get_line())
            # First we need to create the object and add it to the tree and set its position.
            var newobject = load(currentline["filename"]).instance()
            get_node(currentline["parent"]).add_child(newobject)
            newobject.set_pos(Vector2(currentline["posx"],currentline["posy"]))
            # Now we set the remaining variables.
            for i in currentline.keys():
                if (i == "filename" or i == "parent" or i == "posx" or i == "posy"):
                    continue
                newobject.set(i, currentline[i])
        savegame.close()

And now we can save and load an arbitrary number of objects laid out
almost anywhere across the scene tree! Each object can store different
data depending on what it needs to save.

Some notes
----------

We may have glossed over a step, but setting the game state to one fit
to start loading data can be very complicated. This step will need to be
heavily customized based on the needs of an individual project.

This implementation assumes no Persist objects are children of other
Persist objects. Doing so would create invalid paths. If this is one of
the needs of a project this needs to be considered. Saving objects in
stages (parent objects first) so they are available when child objects
are loaded will make sure they're available for the add_child() call.
There will also need to be some way to link children to parents as the
nodepath will likely be invalid.
