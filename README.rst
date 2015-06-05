.. image:: https://pypip.in/version/connexion/badge.svg
   :target: https://pypi.python.org/pypi/connexion
   :alt: Latest Version

.. image:: https://pypip.in/status/connexion/badge.svg
   :target: https://pypi.python.org/pypi/connexion
   :alt: Development Status

.. image:: https://img.shields.io/pypi/l/connexion.svg
   :target: https://github.com/zalando/connexion/blob/master/LICENSE
   :alt: License


Connexion
=========

Connexion is a framework on top of Flask_ to automagically handle your REST API requests
based on `Swagger 2.0 Specification <swagger.spec_>`_ files
in YAML.

How to use
----------

Put your API YAML inside a folder in the root path of your application (e.g ``swagger\``) and then do

.. code-block:: python

    import connexion

    app = connexion.App(__name__, port = 8080, specification_dir='swagger/')
    app.add_api('my_api.yaml')
    app.run()

Endpoint Routing
----------------
Connexion uses the ``OperationId`` from each `Operation Object <swagger.spec.operation_>`_  to identify which function
should handle each url.

For example:

.. code-block:: yaml

    paths:
      /hello_world:
        post:
          operationId: myapp.api.hello_world

If you provided this path in your specification POST requests to ``http://MYHOST/hello_world`` would be handled by the
function ``hello_world`` in ``myapp.api``.

Additionally you can also define a ``basePath`` on the top level of the API specification, which is useful for versioned
APIs. If you wanted to serve the previous endpoint from  ``http://MYHOST/1.0/hello_world`` you could do:

.. code-block:: yaml

    basePath: /1.0

    paths:
      /hello_world:
        post:
          operationId: myapp.api.hello_world

Other alternative if you don't want to include the base path in your specification is provide the base path when adding
the API to your application:

.. code-block:: python

    app.add_api('my_api.yaml', base_path='/1.0')

Response Serialization
----------------------
If the specification defines that a endpoint returns **only** ``application/json`` connexion will automatically
serialize the return value with JSON and set the right content type in the HTTP header using Flask's jsonify.

Authentication and Authorization
--------------------------------
If the specification includes a Oauth2 `Security Definition <swager.spec.security_definition_>`_ compatible with the
Zalando Greendale Team's infrastructure connexion will automatically handle token validation and authorization for
operations that have `Security Requirements <swager.spec.security_requirement_>`_. One main difference between the usual
Oauth flow and the one connexion uses is that the API Security Definition **must** include a 'x-tokenInfoUrl' with the
url to use to validate and get the token information.
Connexion expects to receive the Oauth token in the ``Authorization`` header field in the format described in
`RFC 6750 <rfc6750_>`_ section 2.1.

Swagger Json
------------
Connexion makes the Swagger specification in json format available from ``swagger.json`` in the base path of the api.

Swagger UI
----------
The Swagger UI for an API is available in ``{base_path}/ui/`` where ``base_path`` is the base path of the api.

Server Backend
--------------
By default connexion uses the default flask server but you can also use Tornado_ as the http server, to do so set server
to ``tornado``:

.. code-block:: python

    import connexion

    app = connexion.App(__name__, port = 8080, specification_dir='swagger/', server='tornado')

License
-------
Copyright 2015 Zalando SE

Licensed under the Apache License, Version 2.0 (the "License");
you may not use this file except in compliance with the License.
You may obtain a copy of the License at

http://www.apache.org/licenses/LICENSE-2.0

Unless required by applicable law or agreed to in writing, software
distributed under the License is distributed on an "AS IS" BASIS,
WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied.
See the License for the specific language governing permissions and
limitations under the License.

.. _Flask: http://flask.pocoo.org/
.. _rfc6750: https://tools.ietf.org/html/rfc6750
.. _swagger.spec: https://github.com/swagger-api/swagger-spec/blob/master/versions/2.0.md
.. _swagger.spec.operation: https://github.com/swagger-api/swagger-spec/blob/master/versions/2.0.md#operation-object
.. _swager.spec.security_definition: https://github.com/swagger-api/swagger-spec/blob/master/versions/2.0.md#security-definitions-object
.. _swager.spec.security_requirement: https://github.com/swagger-api/swagger-spec/blob/master/versions/2.0.md#security-requirement-object
.. _Tornado: http://www.tornadoweb.org/en/stable/