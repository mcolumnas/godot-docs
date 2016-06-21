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
            posx=get_pos().x, #Vector2 no es soportado por json
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
        var juegosalvado = File.new()
        if !juegosalvado.file_exists("user://juegosalvado.save"):
            return #Error!  No tenemos un juego salvado para cargar

        # Necesitamos revertir el estado del juego asi no clonamos objetos durante la carga.
        # Esto variara mucho dependiendo de las necesidades del proyecto, asi que ten cuidado con este paso.
        # Para nuestro ejemplo, vamos a lograr esto borrando los objetos que se pueden salvar.
        var savenodes = get_tree().get_nodes_in_group("Persist")
        for i in savenodes:
            i.queue_free()

        # Carga el archivo linea por linea y procesa ese diccionario para restaurar los objetos que representan
        var currentline = {} # dict.parse_json() requiere un diccionario declarado
        juegosalvado.open("user://juegosalvado.save", File.READ)
        while (!juegosalvado.eof_reached()):
            currentline.parse_json(juegosalvado.get_line())
            # Primero necesitamos crear el objeto y agregarlo al arbol ademas de ajustar su posicion
            var newobject = load(currentline["filename"]).instance()
            get_node(currentline["parent"]).add_child(newobject)
            newobject.set_pos(Vector2(currentline["posx"],currentline["posy"]))
            # Ahora ajustamos las variables restantes
            for i in currentline.keys():
                if (i == "filename" or i == "parent" or i == "posx" or i == "posy"):
                    continue
                newobject.set(i, currentline[i])
        juegosalvado.close()

Y ahora podemos salvar y cargar un numero arbitrario de objetos
distribuidos practicamente en cualquier lado a traves del arbol de
escena! Cada objeto puede guardar diferentes datos dependiendo en que
necesita salvar.

Algunas notas
-------------

Podemos haber pasado por alto un paso, pero ajustar el estado del juego
a uno acondicionado para empezar a cargar datos puede ser muy
complicado. Este paso va a necesitar ser fuertemente personalizado
basado en las necesidades de un proyecto individual.

Esta implementacion asume que ningun objeto persistente es hijo de otro
objeto persistente. Ello crearia paths invalidos. Si esta es una de las
necesidades del proyecto esto tiene que ser considerado. Salvar
objetos en etapas (objetos padre primero) de forma que esten disponibles
cuando los objetos hijos son cargados asegurara que estan disponibles
para la llamada add_child(). Tambien se necesitara algun forma de
vincular hijos a padres ya que el nodepath probablemente sera invalido.
