.. meta::
   :description: Hasura Cloud multiple JWT Secrets
   :keywords: hasura, docs, cloud, security, allow, , multiple, JWT, secrets

.. _multiple_jwt_secrets:

Multiple JWT Secrets
===========

.. contents:: Table of contents
  :backlinks: none
  :depth: 1
  :local:

Introduction
------------

You can configure Hasura with a list of JWT Secrets so that you can integrate with different JWT issuers. This is useful when you have different authentication providers using the same Hasura infrastructure.

How to use multiple jwt secrets
-------------------------------

Multiple JWT secrets can be provided in the env var ``HASURA_GRAPHQL_JWT_SECRETS`` which takes a JSON array of JWT secret objects.

Bearer Tokens are authenticated against the secret with a matching or absent `issuer`.

The authentication is resolved as follows:

1. Raw token values matched with configurations by looking in locations configured in HASURA_GRAPHQL_JWT_SECRETS under the "header" field (Authorization Header if missing).
2. Tokens are filtered to ensure that the `issuer` field matches, or that the issuer is absent either from the configuration, or the token.
3. If no candidate tokens are found then the unauthorized flow is performed (depends on ``HASURA_GRAPHQL_UNAUTHORIZED_ROLE``).
3. If multiple candidate tokens are found then an error is raised as the desired token is ambiguous.
3. If one candidate token is found then it is verified against the corresponding configured secret.

.. note::
   Authentication resolution is identical when using ``HASURA_GRAPHQL_JWT_SECRET`` or a single ``HASURA_GRAPHQL_JWT_SECRETS`` configuration.

.. note::

    If both ``HASURA_GRAPHQL_JWT_SECRET`` and ``HASURA_GRAPHQL_JWT_SECRETS`` are set, then ``HASURA_GRAPHQL_JWT_SECRETS`` will be used.
