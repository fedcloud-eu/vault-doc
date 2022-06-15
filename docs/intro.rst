Introduction
============

Applications in EGI Infrastructure may need different secrets (credentials, tokens, passwords, etc.) during deployments
and operations. The secrets are often stored as clear texts in configuration files or code repositories that expose
security risks. Furthermore, the secrets stored in files are static and difficult to change/rotate. The secret
management service for EGI Infrastructure is developed to solve the issues.

The secret management service is designed as follows:

* Non-intrusion: Operates as a stand-alone service, no extra efforts from site admins to support the service, no
  additional permissions are needed for users.
* Simple usage: Authentication via OIDC tokens from EGI Check-in, no extra credentials are required. The service is
  based on Hashicorp’s Vault which is well-known in industry, with many client tools and libraries.
* High-availability: Service instances are distributed on different sites, without single point of failure. A generic
  endpoint https://vault.services.fedcloud.eu:8200 is dynamically assigned
  to a healthy instance via Dynamic DNS service.
