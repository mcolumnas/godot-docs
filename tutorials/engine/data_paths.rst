.. _doc_data_paths:

Paths(Caminos) de Datos
========================

Separadores de caminos
----------------------

Con el motivo de soportar tantas plataformas como es posible, Godot
solo acepta separadores de camino de estilo unix (``/``). Estos funcionan
en cualquier lado, incluyendo Windows.

Un camino como ``C:\Projects`` se transforma en ``C:/Projects``.

Path de recursos
----------------

Como se menciono antes, Godot considera que un proyecto existe en
cualquier carpeta que contenga un archivo de texto "engine.cfg",
aun si el mismo esta vacío.

Acceder a archivos de proyectos puede ser hecho abriendo cualquier
camino con ``res://`` como base. Por ejemplo, una textura localizada
en la raíz de la carpeta del proyecto puede ser abierta con el
siguiente camino: ``res://algunatextura.png``.

Camino de datos de usuario (datos persistentes)
-----------------------------------------------

Mientras el proyecto esta corriendo, es un escenario muy común que el
camino de recursos sea de solo lectura, debido a que esta dentro de
un paquete, ejecutable autónomo, o lugar de instalación de sistema.

Guardar archivos persistentes en esos escenarios debería ser hecho
usando el prefijo ``user://``, por ejemplo: ``user://gamesave.txt``.

En algunos dispositivos (por ejemplo, móvil y consola) este camino es
único para la applicacion. Bajo sistemas operativos de escritorio, el
motor usa el nombre tipico ~/.Name (chequea el nombre de proyecto bajo
los ajustes) en OSX y Linux, y APPDATA/Name para Windows.
