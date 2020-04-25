# Keycloak-Admin-OpenAPI-Definition

[OpenAPI definitions](https://github.com/OAI/OpenAPI-Specification) for
[Keycloak](https://www.keycloak.org/)'s Admin API.

These can be used to generate libraries for interacting with Keycloak
from any mainstream programming langauge.

OpenAPI definitions are sometimes known by their previous name of Swagger
specifications.

[Keycloak 9.0 Admin API](./keycloak/9.0.json)

[Other versions](./keycloak/)

[Red Hat Single
Sign-On](https://access.redhat.com/products/red-hat-single-sign-on)
Admin API definitions are not distributed in this repository, but can
be generated.

```bash
make keycloak/sso-6.json keycloak/sso-7.3.json
```

## Licensing

The OpenAPI definitions are [Apache 2.0 licensed](./keycloak/LICENSE.txt).

The transformer is [MIT licensed](keycloak-openapi-transformer/LICENSE).

The example app has a [CC0 waiver](example_app/WAIVER) (has had its
copyright waived).

## General Usage

Typically to use Keycloak's admin Rest API, you first get a token from
a realm. This is done using the OAuth2 protocol.

```bash
bearer_token=$(curl -X POST 'http://localhost:8080/auth/realms/master/protocol/openid-connect/token' \
     --data-urlencode 'username=admin-user' \
     --data-urlencode 'password=admin-password' \
     --data-urlencode 'grant_type=password' \
     --data-urlencode 'client_id=admin-cli' \
      | jq -r '.access_token')
```

The realm used for the token is not necessarily the realm used in the API.

The endpoints in this specification can then be used with a base url
of the form `http://localhost:8080/auth/admin/realms` and the above
bearer token.

Please see the [example app](example_app/src/index.ts).

## Example App

[This example](./example_app/) uses
[openapi-generator](https://github.com/OpenAPITools/openapi-generator)
to build an API-client. The app in the example is very simple and only
fetches a list of Keycloak-clients.

```bash
# Create a keycloak instance to run against
docker container run --rm -e KEYCLOAK_USER=admin-user -e KEYCLOAK_PASSWORD=admin-password -p 8080:8080 jboss/keycloak:9.0.3
# Open http://localhost:8080/ and wait for keycloak to start up

# In a second terminal run
cd example_app
npm install
npm run generate-client
npm run compile
node dist/index.js
# prints out
# The default clients:
#   account
#   account-console
#   admin-cli
#   broker
#   master-realm
#   security-admin-console
```

In general, clients can be generated by running a command similar to:

```bash
openapi-generator generate -i 'https://github.com/ccouzens/keycloak-openapi/raw/master/keycloak/9.0.json' -g 'typescript-axios' -o 'src/keycloak-client'
```

## Keycloak OpenAPI Transformer

All the OpenAPI definitions are generated from the published HTML
documentation. This tool transforms the HTML documentation into OpenAPI
definitions.

HTML of the documentation is inputted through stdin and the JSON of the
definition is outputted through stdout.

## Requirements

Building Red Hat Single Sign-On definitions or rebuilding Keycloak's
definitions requires:

- `make`
- [`Rust`](https://www.rust-lang.org/tools/install)

Building the transformer requires
[`Rust`](https://www.rust-lang.org/tools/install).

Building the example app requires `make`, `Node` and `Java`.

Running the keycloak instance that pairs with the example app requires
`Docker`.
