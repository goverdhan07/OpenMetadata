---
title: Okta SSO for Bare Metal
slug: /deployment/security/okta/bare-metal
---

# Okta SSO for Bare Metal

## Update conf/openmetadata.yaml

Once the `Client Id` and `Client Secret` are generated add the `Client Id` in `openmetadata.yaml` file in `client_id` field.

```yaml
authenticationConfiguration:
  provider: "okta"
  publicKeyUrls:
    - "{ISSUER_URL}/v1/keys"
  authority: "{ISSUER_URL}"
  clientId: "{CLIENT_ID - SPA APP}"
  callbackUrl: "http://localhost:8585/callback"
```

Then, 
- Update `authorizerConfiguration` to add login names of the admin users in `adminPrincipals` section as shown below.
- Update the `principalDomain` to your company domain name.
- update the `botPrincipals`, add the Ingestion Client ID for the Service application. This can be found in Okta -> Applications -> Applications, Refer to Step 3 for `Creating Service Application`.

```yaml
authorizerConfiguration:
  className: "org.openmetadata.catalog.security.DefaultAuthorizer"
  # JWT Filter
  containerRequestFilter: "org.openmetadata.catalog.security.JwtFilter"
  adminPrincipals:
    - "user1"
    - "user2"
  botPrincipals:
    - "ingestion-bot"
    - "<service_application_client_id>"
  principalDomain: "open-metadata.org"
```

Finally, update the Airflow information:

```yaml
airflowConfiguration:
  apiEndpoint: ${AIRFLOW_HOST:-http://localhost:8080}
  username: ${AIRFLOW_USERNAME:-admin}
  password: ${AIRFLOW_PASSWORD:-admin}
  metadataApiEndpoint: ${SERVER_HOST_API_URL:-http://localhost:8585/api}
  authProvider: okta
  authConfig:
    okta:
      clientId: ${OM_AUTH_AIRFLOW_OKTA_CLIENT_ID:-""}
      orgURL: ${OM_AUTH_AIRFLOW_OKTA_ORGANIZATION_URL:-""}
      privateKey: ${OM_AUTH_AIRFLOW_OKTA_PRIVATE_KEY:-""}
      email: ${OM_AUTH_AIRFLOW_OKTA_SA_EMAIL:-""}
      scopes: ${OM_AUTH_AIRFLOW_OKTA_SCOPES:-[]}
```
