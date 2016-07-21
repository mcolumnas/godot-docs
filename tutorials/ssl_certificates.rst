.. _doc_ssl_certificates:

Certificados SSL
================

Introducción
------------

A menudo se quieren usar conexiones SSL para las comunicaciones de forma
de evitar ataques "hombre en el medio". Godot tiene un wraper de conexión,
:ref:`StreamPeerSSL <class_StreamPeerSSL>`,
el cual puede tomar una conexión regular y agregarle seguridad. La clase
:ref:`HTTPClient <class_HTTPClient>` también soporta HTTPS al usar el
mismo wrapper.

Para que funcione SSL, los certificados necesitan ser aprobados. Un
archivo .crt debe ser especificado en la configuración de proyecto:

.. image:: /img/ssl_certs.png

Este archivo debe contener cualquier número de certificados públicos
en formato http://en.wikipedia.org/wiki/Privacy-enhanced_Electronic_Mail

Por supuesto, recuerda agregar .crt como filtro así el exportador lo
reconoce cuando exporta tu proyecto.

.. image:: /img/add_crt.png

Hay dos formas de obtener certificados:


Opción 1: self signed cert
--------------------------

La primer opción es la más simple, solo genera un par de claves privadas
y públicas, y pon el par público en el archivo .crt (nuevamente, en
formato PEM). La clave privada solo debería ir en tu servidor.

OpenSSL tiene `algo
de documentación <https://www.openssl.org/docs/HOWTO/keys.txt>`__ sobre
esto. Esta opción **no requiere validación de dominio** ni gastar una
cantidad considerable de dinero comprando certificados de una autoridad
de certificados (CA).

Opción 2: CA cert
-------------------

La segunda opción consiste de usar una autoridad de certificados (CA)
como Verisign, Geotrust, etc. Este es un proceso más pesado, pero es
más "oficial" y asegura que tu identidad está claramente representada.

A no ser que estés trabajando con grandes compañias o corporaciones, o
necesites conectarte al servidor de alguien más (ejemplo: conectarse a
un proveedor REST API via HTTPS como Google) este método no es tan útil.

También, cuando usas un certificado emitido por una CA, **deber habilitar
la validación de dominio**, para asegurar que el dominio al que te estas
conectando es el adecuado, de otra forma cualquier sitio web puede emitir
certificados en la misma CA y funcionaria.

Si usas Linux, puedes usar el archivo de certificados suministrado,
generalmente ubicado en:

::

    /etc/ssl/certs/ca-certificates.crt

Este archivo contiene conexiones HTTPS a virtualmente cualquier sitio
web (ejemplo: Google, Microsoft, etc.).

O solo elije uno de los certificados mas específicos allí si te quieres
conectar a uno en particular.
