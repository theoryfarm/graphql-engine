.. meta::
   :description: Manage query collections with the Hasura schema/metadata API
   :keywords: hasura, docs, schema/metadata API, API reference, query collection

.. _schema_metadata_api_query_collections:

Schema/Metadata API Reference: Query collections (Deprecated)
=============================================================

.. admonition:: Deprecation

  In versions ``v2.0.0`` and above, the schema/metadata API is deprecated in favour of the :ref:`schema API <schema_apis>` and the
  :ref:`metadata API <metadata_apis>`.

  Though for backwards compatibility, the schema/metadata APIs will continue to function.

.. contents:: Table of contents
  :backlinks: none
  :depth: 1
  :local:

Introduction
------------

Group queries using query collections.

Create/drop query collections and add/drop a query to a collection using the following query types.

.. _schema_metadata_create_query_collection:

create_query_collection
-----------------------

``create_query_collection`` is used to define a collection.

.. code-block:: http

   POST /v1/query HTTP/1.1
   Content-Type: application/json
   X-Hasura-Role: admin

   {
       "type" : "create_query_collection",
       "args": {
            "name": "my_collection",
            "comment": "an optional comment",
            "definition": {
                "queries": [
                    {"name": "query_1", "query": "query { test {id name}}"}
                 ]
            }
        }
   }

.. _schema_metadata_create_query_collection_syntax:

Args Syntax
^^^^^^^^^^^

.. list-table::
   :header-rows: 1

   * - Key
     - Required
     - Schema
     - Description
   * - name
     - true
     - :ref:`CollectionName`
     - Name of the query collection
   * - definition
     - true
     - :ref:`CollectionQuery` array
     - List of queries
   * - comment
     - false
     - text
     - Optional comment


.. _schema_metadata_drop_query_collection:

drop_query_collection
---------------------

``drop_query_collection`` is used to drop a collection

.. code-block:: http

   POST /v1/query HTTP/1.1
   Content-Type: application/json
   X-Hasura-Role: admin

   {
       "type" : "drop_query_collection",
       "args": {
            "collection": "my_collection",
            "cascade": false
        }
   }

.. _schema_metadata_drop_query_collection_syntax:

Args syntax
^^^^^^^^^^^

.. list-table::
   :header-rows: 1

   * - Key
     - Required
     - Schema
     - Description
   * - collection
     - true
     - :ref:`CollectionName`
     - Name of the query collection
   * - cascade
     - true
     - boolean
     - When set to ``true``, the collection (if present) is removed from the allowlist

.. _schema_metadata_add_query_to_collection:

add_query_to_collection
-----------------------

``add_query_to_collection`` is used to add a query to a given collection.

.. code-block:: http

   POST /v1/query HTTP/1.1
   Content-Type: application/json
   X-Hasura-Role: admin

   {
       "type" : "add_query_to_collection",
       "args": {
            "collection_name": "my_collection",
            "query_name": "query_2",
            "query": "query {test {name}}"
        }
   }

.. _schema_metadata_add_query_to_collection_syntax:

Args Syntax
^^^^^^^^^^^

.. list-table::
   :header-rows: 1

   * - Key
     - Required
     - Schema
     - Description
   * - collection_name
     - true
     - :ref:`CollectionName`
     - Name of the query collection
   * - query_name
     - true
     - :ref:`QueryName`
     - Name of the query
   * - query
     - true
     - text
     - The GraphQL query text

.. _schema_metadata_drop_query_from_collection:

drop_query_from_collection
--------------------------

``drop_query_from_collection`` is used to remove a query from a given collection.

.. code-block:: http

   POST /v1/query HTTP/1.1
   Content-Type: application/json
   X-Hasura-Role: admin

   {
       "type" : "drop_query_from_collection",
       "args": {
            "collection_name": "my_collection",
            "query_name": "query_2"
        }
   }

.. _schema_metadata_drop_query_from_collection_syntax:

Args Syntax
^^^^^^^^^^^

.. list-table::
   :header-rows: 1

   * - Key
     - Required
     - Schema
     - Description
   * - collection_name
     - true
     - :ref:`CollectionName`
     - Name of the query collection
   * - query_name
     - true
     - :ref:`QueryName`
     - Name of the query

.. _schema_metadata_add_collection_to_allowlist:

add_collection_to_allowlist
---------------------------

``add_collection_to_allowlist`` is used to add a collection to the allow-list.

.. code-block:: http

   POST /v1/query HTTP/1.1
   Content-Type: application/json
   X-Hasura-Role: admin

   {
       "type" : "add_collection_to_allowlist",
       "args": {
            "collection": "my_collection"
        }
   }

.. _schema_metadata_add_collection_to_allowlist_syntax:

Args Syntax
^^^^^^^^^^^

.. list-table::
   :header-rows: 1

   * - Key
     - Required
     - Schema
     - Description
   * - collection
     - true
     - :ref:`CollectionName`
     - Name of a query collection to be added to the allow-list

.. _schema_metadata_drop_collection_from_allowlist:

drop_collection_from_allowlist
------------------------------

``drop_collection_from_allowlist`` is used to remove a collection from the allow-list.

.. code-block:: http

   POST /v1/query HTTP/1.1
   Content-Type: application/json
   X-Hasura-Role: admin

   {
       "type" : "drop_collection_from_allowlist",
       "args": {
            "collection": "my_collection_1"
        }
   }

.. _schema_metadata_drop_collection_from_allowlist_syntax:

Args Syntax
^^^^^^^^^^^

.. list-table::
   :header-rows: 1

   * - Key
     - Required
     - Schema
     - Description
   * - collection
     - true
     - :ref:`CollectionName`
     - Name of a query collection to be removed from the allow-list
