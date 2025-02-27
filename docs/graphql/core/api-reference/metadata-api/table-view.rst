.. meta::
   :description: Manage tables and views with the Hasura metadata API
   :keywords: hasura, docs, metadata API, API reference, table, view

.. _metadata_api_tables_views:

Metadata API Reference: Tables/Views
====================================

.. contents:: Table of contents
  :backlinks: none
  :depth: 1
  :local:

Introduction
------------

Track/untrack a table/view in Hasura GraphQL engine.

Only tracked tables/views are available for querying/mutating/subscribing data over the GraphQL API.

.. admonition:: Supported from

  The metadata API is supported for versions ``v2.0.0`` and above and replaces the older
  :ref:`schema/metadata API <schema_metadata_apis>`.

.. _metadata_pg_track_table:

pg_track_table
--------------

``pg_track_table`` is used to add a table/view to the GraphQL schema with configuration.
You can customise the root field names.

Add a table/view ``author``:

.. code-block:: http

   POST /v1/metadata HTTP/1.1
   Content-Type: application/json
   X-Hasura-Role: admin

   {
      "type": "pg_track_table",
      "args": {
        "source": "default",
        "table": "author",
        "configuration": {
           "custom_root_fields": {
              "select": "Authors",
              "select_by_pk": "Author",
              "select_aggregate": "AuthorAggregate",
              "insert": "AddAuthors",
              "insert_one":"AddAuthor",
              "update": "UpdateAuthors",
              "update_by_pk": "UpdateAuthor",
              "delete": "DeleteAuthors",
              "delete_by_pk": "DeleteAuthor"
           },
           "custom_column_names": {
              "id": "authorId"
           },
           "comment": "Authors of books"
        }
      }
   }

A table can be tracked with a ``custom name``. This can be useful when a table
name is not GraphQL compliant, like ``Users Address``. A ``custom name`` like
``users_address`` will complement the ``"Users Address"``
table, so that it can be added to the GraphQL schema.

.. code-block:: http

   POST /v1/metadata HTTP/1.1
   Content-Type: application/json
   X-Hasura-Role: admin

   {
      "type": "pg_track_table",
      "args": {
        "source": "default",
        "table": "Author Details",
        "configuration": {
           "custom_name": "author_details"
        }
      }
   }

The GraphQL nodes and typenames
that are generated will be according to the ``identifier``. For example, in this case,
the nodes generated will be:

- ``users_address``
- ``users_address_one``
- ``users_address_aggregate``
- ``insert_users_address``
- ``insert_users_address_one``
- ``update_users_address``
- ``update_users_address_by_pk``
- ``delete_users_address``
- ``delete_users_address_by_pk``

.. note::

   Hasura GraphQL engine requires the constraint names (if any) of a table to be
   `GraphQL compliant <https://spec.graphql.org/June2018/#sec-Names>`__ in order to be able to track it.

.. _metadata_pg_track_table_syntax:

Args syntax
^^^^^^^^^^^

.. list-table::
   :header-rows: 1

   * - Key
     - Required
     - Schema
     - Description
   * - table
     - true
     - :ref:`TableName <TableName>`
     - Name of the table
   * - configuration
     - false
     - :ref:`Table Config <table_config>`
     - Configuration for the table/view
   * - source
     - false
     - :ref:`SourceName <SourceName>`
     - Name of the source database of the table (default: ``default``)

.. _metadata_pg_untrack_table:

pg_untrack_table
----------------

``untrack_table`` is used to remove a table/view from the GraphQL schema.

Remove a table/view ``author``:

.. code-block:: http

   POST /v1/metadata HTTP/1.1
   Content-Type: application/json
   X-Hasura-Role: admin

   {
       "type": "pg_untrack_table",
       "args": {
           "table": {
               "schema": "public",
               "name": "author"
            },
           "source": "default",
           "cascade": true
       }
   }

.. _metadata_pg_untrack_table_syntax:

Args syntax
^^^^^^^^^^^

.. list-table::
   :header-rows: 1

   * - Key
     - Required
     - Schema
     - Description
   * - table
     - true
     - :ref:`TableName <TableName>`
     - Name of the table
   * - cascade
     - false
     - Boolean
     - When set to ``true``, the effect (if possible) is cascaded to any metadata dependent objects (relationships, permissions, templates)
   * - source
     - false
     - :ref:`SourceName <SourceName>`
     - Name of the source database of the table (default: ``default``)

.. _metadata_pg_set_table_is_enum:

pg_set_table_is_enum
--------------------

``pg_set_table_is_enum`` sets whether an already-tracked table should be used as an :ref:`enum table <pg_create_enum_table>`.

Use table ``user_role`` as an enum table:

.. code-block:: http

  POST /v1/metadata HTTP/1.1
  Content-Type: application/json
  X-Hasura-Role: admin

  {
    "type": "pg_set_table_is_enum",
    "args": {
      "table": {
        "schema": "public",
        "name": "user_role"
      },
      "source": "default",
      "is_enum": true
    }
  }

.. _metadata_pg_set_table_is_enum_syntax:

Args syntax
^^^^^^^^^^^

.. list-table::
   :header-rows: 1

   * - Key
     - Required
     - Schema
     - Description
   * - table
     - true
     - :ref:`TableName <TableName>`
     - Name of the table
   * - is_enum
     - true
     - Boolean
     - Whether or not the table should be used as an :ref:`enum table <enum table>`.
   * - source
     - false
     - :ref:`SourceName <SourceName>`
     - Name of the source database of the table (default: ``default``)

.. _metadata_pg_set_table_customization:

pg_set_table_customization
--------------------------

``pg_set_table_customization`` allows you to customize any given table with
a custom name, custom root fields and custom column names of an already tracked
table. This will **replace** the already present customization.

Set the configuration for a table/view called ``author``:

.. code-block:: http

   POST /v1/metadata HTTP/1.1
   Content-Type: application/json
   X-Hasura-Role: admin

   {
      "type": "pg_set_table_customization",
      "args": {
        "table": "author_details",
        "source": "default",
        "configuration": {
          "identifier": "author",
          "custom_root_fields": {
             "select": "Authors",
             "select_by_pk": "Author",
             "select_aggregate": "AuthorAggregate",
             "insert": "AddAuthors",
             "insert_one":"AddAuthor",
             "update": "UpdateAuthors",
             "update_by_pk": "UpdateAuthor",
             "delete": "DeleteAuthors",
             "delete_by_pk": "DeleteAuthor"
          },
          "custom_column_names": {
             "id": "authorId"
          }
        }
      }
   }

.. _metadata_pg_set_table_customization_syntax:

Args syntax
^^^^^^^^^^^

.. list-table::
   :header-rows: 1

   * - Key
     - Required
     - Schema
     - Description
   * - table
     - true
     - :ref:`TableName <TableName>`
     - Name of the table
   * - configuration
     - false
     - :ref:`TableConfig <table_config>`
     - Configuration for the table/view
   * - source
     - false
     - :ref:`SourceName <SourceName>`
     - Name of the source database of the table (default: ``default``)

.. _mssql_track_table:

mssql_track_table
-----------------

``mssql_track_table`` is used to add a table/view to the GraphQL schema with configuration.
You can customise the root field names.

Add a table/view ``author``:

.. code-block:: http

  POST /v1/metadata HTTP/1.1
  Content-Type: application/json
  X-Hasura-Role: admin

  {
      "type": "mssql_track_table",
      "args": {
        "table": "author",
        "source": "default"
      }
  }

.. TODO: MSSQL_UNSUPPORTED

  A table can be tracked with a ``custom name``. This can be useful when a table
  name is not GraphQL compliant, like ``Users Address``. A ``custom name`` like
  ``users_address`` will complement the ``"Users Address"``
  table, so that it can be added to the GraphQL schema.

  .. code-block:: http

    POST /v1/metadata HTTP/1.1
    Content-Type: application/json
    X-Hasura-Role: admin

    {
        "type": "mssql_track_table",
        "args": {
          "table": "Author Details"
        }
    }

.. TODO: MSSQL_UNSUPPORTED

  The GraphQL nodes and typenames
  that are generated will be according to the ``identifier``. For example, in this case,
  the nodes generated will be:

  - ``users_address``
  - ``users_address_one``
  - ``users_address_aggregate``
  - ``insert_users_address``
  - ``insert_users_address_one``
  - ``update_users_address``
  - ``update_users_address_by_pk``
  - ``delete_users_address``
  - ``delete_users_address_by_pk``

.. note::

  Hasura GraphQL engine requires the constraint names (if any) of a table to be
  `GraphQL compliant <https://spec.graphql.org/June2018/#sec-Names>`__ in order to be able to track it.

.. _mssql_track_table_syntax:

Args syntax
^^^^^^^^^^^

.. list-table::
  :header-rows: 1

  * - Key
    - Required
    - Schema
    - Description
  * - table
    - true
    - :ref:`TableName <TableName>`
    - Name of the table
  * - configuration
    - false
    - :ref:`Table Config <table_config>`
    - Configuration for the table/view
  * - source
    - false
    - :ref:`SourceName <SourceName>`
    - Name of the source database of the table (default: ``default``)

.. _mssql_untrack_table:

mssql_untrack_table
-------------------

``untrack_table`` is used to remove a table/view from the GraphQL schema.

Remove a table/view ``author``:

.. code-block:: http

  POST /v1/metadata HTTP/1.1
  Content-Type: application/json
  X-Hasura-Role: admin

  {
      "type": "mssql_untrack_table",
      "args": {
          "table": {
              "schema": "dbo",
              "name": "author"
            },
          "source": "default",
          "cascade": true
      }
  }

.. _mssql_untrack_table_syntax:

Args syntax
^^^^^^^^^^^

.. list-table::
  :header-rows: 1

  * - Key
    - Required
    - Schema
    - Description
  * - table
    - true
    - :ref:`TableName <TableName>`
    - Name of the table
  * - cascade
    - false
    - Boolean
    - When set to ``true``, the effect (if possible) is cascaded to any metadata dependent objects (relationships, permissions, templates)
  * - source
    - false
    - :ref:`SourceName <SourceName>`
    - Name of the source database of the table (default: ``default``)

.. _mssql_set_table_customization:

mssql_set_table_customization
-----------------------------

``mssql_set_table_customization`` allows you to customize any given table with
a custom name, custom root fields and custom column names of an already tracked
table. This will **replace** the already present customization.

Set the configuration for a table/view called ``author``:

.. code-block:: http

    POST /v1/metadata HTTP/1.1
    Content-Type: application/json
    X-Hasura-Role: admin

    {
      "type": "mssql_set_table_customization",
      "args": {
        "table": "author_details",
        "source": "default",
        "configuration": {
          "identifier": "author",
          "custom_root_fields": {
              "select": "Authors",
              "select_aggregate": "AuthorAggregate",
          },
          "custom_column_names": {
              "id": "authorId"
          }
        }
      }
    }

.. _mssql_set_table_customization_syntax:

Args syntax
^^^^^^^^^^^

.. list-table::
    :header-rows: 1

    * - Key
      - Required
      - Schema
      - Description
    * - table
      - true
      - :ref:`TableName <TableName>`
      - Name of the table
    * - configuration
      - false
      - :ref:`TableConfig <table_config>`
      - Configuration for the table/view
    * - source
      - false
      - :ref:`SourceName <SourceName>`
      - Name of the source database of the table (default: ``default``)


.. _metadata_bigquery_track_table:

bigquery_track_table
--------------------

``bigquery_track_table`` is used to add a table/view to the GraphQL schema with configuration.
You can customise the root field names.

Add a table/view ``author``:

.. code-block:: http

  POST /v1/metadata HTTP/1.1
  Content-Type: application/json
  X-Hasura-Role: admin

  {
      "type": "bigquery_track_table",
      "args": {
        "table": {
          "dataset": "hasura",
          "name": "author",
        },
        "source": "default"
      }
  }

In the case of BigQuery, dataset names are prefixed to table/view names to form
a unique root field name, such that the above example will result in the root
field name being ``hasura_author``.

.. TODO: BIGQUERY_UNSUPPORTED

  A table can be tracked with a ``custom name``. This can be useful when a table
  name is not GraphQL compliant, like ``Users Address``, or when the admin
  wishes to not expose the root fields in terms of the ``dataset + table/view name``.

  A ``custom name`` like ``users_address`` will complement the ``"Users
  Address"`` table, so that it can be added to the GraphQL schema.

  .. code-block:: http

    POST /v1/metadata HTTP/1.1
    Content-Type: application/json
    X-Hasura-Role: admin

    {
        "type": "bigquery_track_table",
        "args": {
          "source": "default",
          "table": {
            "dataset": "hasura",
            "name": "Author Details"
           }
        }
    }


  ``writer_info`` will complement the ``"Users Address"`` table, in the
  following case.

  .. code-block:: http

    POST /v1/metadata HTTP/1.1
    Content-Type: application/json
    X-Hasura-Role: admin

    {
        "type": "bigquery_track_table",
        "args": {
          "source": "default",
          "table": {
            "dataset": "hasura",
            "name": "Author Details"
          },
          "configuration": {
            "custom_name": "writer_info",
            "custom_root_fields": {
              "select_aggregate": "writer_info_agg"
            }
          }
        }
    }

.. TODO: BIGQUERY_UNSUPPORTED

  The GraphQL nodes and typenames that are generated will be according to the
  ``identifier``. For example, in this case, the nodes generated will be:

  - ``users_address``
  - ``users_address_aggregate``

  or

  - ``writer_info``
  - ``writer_info_agg``

  respectively

.. note::

  Hasura GraphQL engine requires the constraint names (if any) of a table to be
  `GraphQL compliant <https://spec.graphql.org/June2018/#sec-Names>`__ in order to be able to track it.

.. _metadata_bigquery_track_table_syntax:

Args syntax
^^^^^^^^^^^

.. list-table::
  :header-rows: 1

  * - Key
    - Required
    - Schema
    - Description
  * - table
    - true
    - {"dataset":_, "name":_}
    - Name of the table
  * - configuration
    - false
    - :ref:`Table Config <table_config>`
    - Configuration for the table/view
  * - source
    - false
    - :ref:`SourceName <SourceName>`
    - Name of the source database of the table (default: ``default``)

.. _metadata_bigquery_untrack_table:

bigquery_untrack_table
----------------------

``bigquery_untrack_table`` is used to remove a table/view from the GraphQL schema.

Remove a table/view ``author``:

.. code-block:: http

  POST /v1/metadata HTTP/1.1
  Content-Type: application/json
  X-Hasura-Role: admin

  {
      "type": "bigquery_untrack_table",
      "args": {
          "table": {
              "dataset": "hasura",
              "name": "author"
            },
          "source": "default",
          "cascade": true
      }
  }

.. _metadata_bigquery_untrack_table_syntax:

Args syntax
^^^^^^^^^^^

.. list-table::
  :header-rows: 1

  * - Key
    - Required
    - Schema
    - Description
  * - table
    - true
    - {"dataset":_, "name":_}
    - Name of the table
  * - cascade
    - false
    - Boolean
    - When set to ``true``, the effect (if possible) is cascaded to any metadata dependent objects (relationships, permissions, templates)
  * - source
    - false
    - :ref:`SourceName <SourceName>`
    - Name of the source database of the table (default: ``default``)

.. _metadata_bigquery_set_table_customization:

bigquery_set_table_customization
--------------------------------

``bigquery_set_table_customization`` allows you to customize any given table with
a custom name, custom root fields and custom column names of an already tracked
table. This will **replace** the already present customization.

Set the configuration for a table/view called ``hasura_author_details`` to ``author``:

.. code-block:: http

    POST /v1/metadata HTTP/1.1
    Content-Type: application/json
    X-Hasura-Role: admin

    {
      "type": "bigquery_set_table_customization",
      "args": {
        "table": {
          "dataset": "hasura",
          "name": "author_details",
        },
        "source": "default",
        "configuration": {
          "custom_name": "author",
          "custom_root_fields": {
              "select": "Authors",
              "select_aggregate": "AuthorAggregate",
          },
          "custom_column_names": {
              "id": "authorId"
          }
        }
      }
    }

.. _metadata_bigquery_set_table_customization_syntax:

Args syntax
^^^^^^^^^^^

.. list-table::
    :header-rows: 1

    * - Key
      - Required
      - Schema
      - Description
    * - table
      - true
      - {"dataset":_, "name":_}
      - Name of the table
    * - configuration
      - false
      - :ref:`TableConfig <table_config>`
      - Configuration for the table/view
    * - source
      - false
      - :ref:`SourceName <SourceName>`
      - Name of the source database of the table (default: ``default``)
