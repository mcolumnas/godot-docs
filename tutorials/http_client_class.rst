.. _doc_http_client_class:

Clase HTTP cliente
==================

Aquí un ejemplo usando la clase :ref:`HTTPClient <class_HTTPClient>`.
Es solo un script, por lo que puede correrse ejecutando:

::

    c:\godot> godot -s http_test.gd

Se conectara y descargara un sitio web.

::

    extends SceneTree

    # HTTPClient demo
    # Esta simple clase puede hacer pedidos HTTP, no bloqueara pero necesita ser polled (sondeada)

    func _init():

        var err=0
        var http = HTTPClient.new() # Crea el Client

        var err = http.connect("www.php.net",80) # Conexión a host/port
        assert(err==OK) # Asegurarse que la conexión fue exitosa

        # Esperar hasta que resuelve y conecte
        while( http.get_status()==HTTPClient.STATUS_CONNECTING or http.get_status()==HTTPClient.STATUS_RESOLVING):
            http.poll()
            print("Connecting..")
            OS.delay_msec(500)

        assert( http.get_status() == HTTPClient.STATUS_CONNECTED ) # No se pudo conectar

        # Algunos cabezales (headers)

        var headers=[
            "User-Agent: Pirulo/1.0 (Godot)",
            "Accept: */*"
        ]

        err = http.request(HTTPClient.METHOD_GET,"/ChangeLog-5.php",headers) # Pedir una página del sitio

        assert( err == OK ) # Asegurarse que todo está OK

        while (http.get_status() == HTTPClient.STATUS_REQUESTING):
            # Mantenerse sondeando hasta que el pedido este en marcha
            http.poll()
            print("Requesting..")
            OS.delay_msec(500)


        assert( http.get_status() == HTTPClient.STATUS_BODY or http.get_status() == HTTPClient.STATUS_CONNECTED ) # Asegurarse que el pedido termino bien.

        print("response? ",http.has_response()) # Puede que el sitio no tenga una respuesta.


        if (http.has_response()):
            # Si hay respuesta..

            var headers = http.get_response_headers_as_dictionary() # Obtener los cabezales de respuesta
            print("code: ",http.get_response_code()) # Mostrar código de respuesta
            print("**headers:\\n",headers) # Mostrar cabezales

            # Obteniendo el Body HTTP

            if (http.is_response_chunked()):
                # Usa trozos (chunks)?
                print("La respuesta está en trozos!")
            else:
                # O es solo contenido plano
                var bl = http.get_response_body_length()
                print("Largo de respuesta: ",bl)

            # Este método funciona para ambos de todas formas

            var rb = RawArray() # Arreglo que contendrá los datos

            while(http.get_status()==HTTPClient.STATUS_BODY):
                # Mientras haya "Body" para leer
                http.poll()
                var chunk = http.read_response_body_chunk() # Obtén un trozo
                if (chunk.size()==0):
                    # No se obtuvo nada, esperar a que los buffers se llenen un poco
                    OS.delay_usec(1000)
                else:
                    rb = rb + chunk # Agregar al buffer de lectura


            # Hecho!

            print("bytes got: ",rb.size())
            var text = rb.get_string_from_ascii()
            print("Text: ",text)


        quit()
