# ws1-identity-services-api

## The Workspace ONE Identity Services API doc

VMware Identity Services provides centralized user management for Workspace ONE cloud services.
The service provisions users and groups from cloud identity providers such as Microsoft Entra ID or Okta to Workspace ONE services and enables federated authentication to the identity provider.
VMware Identity Services is only available for new Workspace ONE tenants.

VMware Identity Services supports integration with the following Workspace ONE cloud services:
- Workspace ONE Access Cloud service
- Workspace ONE UEM 2212 or later

VMware Identity Services supports the following use cases:
- Configure a provisioned directory of users and groups using the System for Cross-domain Identity Management (SCIM 2.0) protocol.
- Manage identity provider configuration for federated authentication into Workspace ONE services.
- Leverage centralized user management and authentication across Workspace ONE Access and Workspace ONE UEM.

The VMware Identity Services APIs are designed for easy configuration and provide a seamless experience for users and applications regardless of the identity provider that you use.

This repo is structured to feed into the developer.omnissa.com Developer Portal via the [](https://github.com/euc-dev/developer.omnissa.github.io) repo using MkDocs published by GitHub Pages. All documentation should be created in MarkDown format with the capabilities of MkDocs and the Material theme in mind under the /docs folder. This folder will be integrated into the [developer portal repo](https://github.com/euc-dev/developer.omnissa.github.io) when built using a GitHub Action.
