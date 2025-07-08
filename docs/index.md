---
layout: page
title: Omnissa Identity Service
hide:
  #- navigation
  - toc
---
![Omnissa Identity Service](../../../assets/logos/Identity-Service-v-lm.png){ align=right }
!!! danger "Important Note"
    Please see [Upcoming Changes to the API MIME type for Omnissa Access and Omnissa Identity Service (6000953) KB](https://kb.omnissa.com/s/article/6000953) for information on important updates to the Omnissa Identity Services API. These changes have been made to the swagger provided on the [Omnissa Identity Service REST APIs](REST-APIs.md) page.

Welcome to the Omnissa Identity Service API Reference documentation.
The documentation provides Getting Started information about how to set up an OAuth 2.0 client and obtain access tokens, 
a comprehensive API reference that includes code samples, and a list of all data structures available through the APIs.

The Omnissa Identity Service APIs are designed for easy configuration and provide a seamless experience for users and applications regardless of the identity provider that you use.

## About Omnissa Identity Service

Omnissa Identity Service provides centralized user management for Omnissa Cloud services.
The service provisions users and groups from cloud identity providers such as Microsoft Entra ID or Okta to Omnissa services and enables federated authentication to the identity provider.
Omnissa Identity Service is only available for new Workspace ONE tenants.

Omnissa Identity Service supports integration with the following Omnissa Cloud services:
- Omnissa Access Cloud service
- Workspace ONE UEM 2212 or later

Omnissa Identity Service supports the following use cases:
- Configure a provisioned directory of users and groups using the System for Cross-domain Identity Management (SCIM 2.0) protocol.
- Manage identity provider configuration for federated authentication into Omnissa services.
- Leverage centralized user management and authentication across Omnissa Access and Workspace ONE UEM.

## Getting Started with Omnissa Identity Service REST APIs

To get you started quickly, let's dive into the necessary steps required to configure an external identity provider (IdP) with Omnissa Identity Service REST API for the cloud.

### Before You Start

Omnissa Identity Service can be integrated with the following cloud-based identity providers:

- Microsoft Entra ID
- Okta
- Any generic SCIM 2.0 identity source

Omnissa Identity Service supports federated authentication against identity providers that follow OIDC (OpenID Connect) or SAML (Security Assertion Markup Language) standards.

Before you start using the API, make sure you meet the following requirements:

- You have a new Workspace ONE tenant.
- You have an administrator account in the Workspace ONE portal.
- You can log into your tenant's Workspace ONE portal.
- You have completed the Omnissa Identity Service setup in the Workspace ONE portal under Accounts > End User Management > Get started. This includes enabling Omnissa Identity Service, configuring the integration with your third-party identity provider, and selecting the Omnissa services to use with the identity provider. For more information, see [Configuring User Provisioning and Identity Federation with Omnissa Identity Service](https://docs.omnissa.com/bundle/IdentityServices/page/GettingStartedwithIdentityServices.html).

### Step 1 - Create OAuth 2.0 Client from the UI

See [Enabling API Access for Omnissa Identity Service](https://docs.omnissa.com/bundle/IdentityServices/page/EnablingAPIAccessforIdentityServices.html).

You will need the client ID and client secret to acquire the access token in the next step.

### Step 2 - Get access token for the OAuth 2.0 Client

An access token is required for authorization of the HTTP calls to the Omnissa Identity Service APIs.

Once the OAuth 2.0 client is created, obtain an access token for the client using the client credentials flow defined in [OAuth 2.0 RFC 6749, section 4.4](https://tools.ietf.org/html/rfc6749#section-4.4).

```sh
curl --location --request POST 'https://{baseURL}/acs/token' \
--header 'Authorization: Basic {base64_encoded_client_credentials}' \
--header 'Content-Type: application/x-www-form-urlencoded' \
--data-urlencode 'grant_type=client_credentials'
```

Encode the client id and client secret from Step 1 in the format `{client_id}:{client_secret}` to Base64 format and use it in the "Authorization" header in the request.

The response contains the generated client's access token ("access_token") used for authorization in the subsequent API calls, and the number of seconds the access token is valid. When you make calls to a REST API, include the access token in the "Authorization" header with the type "Bearer".

The access token can be reused until it expires. The token time to live ("access_token_ttl") is set on client creation. When your access token expires, call the above API again to request a new one.

### Step 3 - Create OAuth 2.0 Client with Identity Provider and Directory Admin RuleSet

Next, create an OAuth 2.0 client for the tenant with the IDP_AND_DIRECTORY_ADMIN rule set. A client with the IDP_AND_DIRECTORY_ADMIN rule set is allowed to perform identity provider and directory CRUD operations.

```sh
curl --location --request POST 'https://{baseURL}/acs/broker/oauth2-clients' \
--header 'Content-Type: application/json' \
--header 'Accept: application/json' \
--header 'Authorization: Bearer {access_token}' \
--data-raw '{
"client_id": "my-auth-grant-client1",
"grant_types": [
"client_credentials"
],
"scope": [
"admin"
],
"secret": "my-auth-grant-client1-secret",
"access_token_ttl": 10080,
"refresh_token_ttl": 525600,
"refresh_token_idle_ttl": 525600,
"rule_set_names": [ "IDP_AND_DIRECTORY_ADMIN" ]
}'
```

Replace `{baseURL}` with your Workspace ONE tenant URL and `{access_token}` with the access token that you obtained in Step 2 for authorization. You can set the token time to live ("access_token_ttl") field which determines the lifespan for all access tokens acquired by the client.

### Step 4 - Get access token for the OAuth 2.0 Client

Use this access token for subsequent API calls. Include the token in the "Authorization" header with the type "Bearer". When the token expires, you can request a new one.

### Step 5 (Optional) - Delete the OAuth 2.0 Client that you created from the UI

To delete the OAuth 2.0 client, follow step 4 from [Enabling API Access for Omnissa Identity Service](https://docs.omnissa.com/bundle/IdentityServices/page/EnablingAPIAccessforIdentityServices.html).

Congratulations! You have set up an OAuth 2.0 client to integrate your application with Omnissa Identity Service.

## Next Steps

Utilise the REST APIs documented in the [REST API page](REST-APIs.md) within your favorite REST API tool, such as [Postman](https://postman.com). This page describes the following info:

- URI
- Request
    - Query Parameters
    - Request Body + Properties
- Response + Properties
- Errors
- Code Samples

## Request Failures

Errors are reported using standard HTTP response codes. The API returns `HTTP 4XX` status codes when the request is invalid and `HTTP 5XX` status codes when something is wrong with the server.

For authorization errors, the API returns `HTTP 401 (Unauthorized)` or `HTTP 403 (Forbidden)`. These kinds of errors are usually related to an access token and can be cleared by making sure that the token is present and hasn't expired.

The API will fail and returns `HTTP 444 (No Response)` (with no response body) when the request `Host` header doesn't match the server FQDN.

For all errors, the API returns an error response body that includes the error code and additional error message in the format below:

```json
{
  "errors": [
    {
      "code": "oauth2.client.with.client.id.already.exists",
      "message": "OAuth2 Client with client id {client_id} already exists.",
      "parameters": {
        "oauth2_client_id": "{client_id}"
      }
    }
  ]
}
```

## For More Information

- [Omnissa Identity Service](https://docs.omnissa.com/bundle/IdentityServices/page/ConfiguringUserProvisioningandIdentityFederationwithIdentityServices.html).
