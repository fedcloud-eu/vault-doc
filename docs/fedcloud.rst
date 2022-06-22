Using Secret management service via FedCloud client
===================================================

Secret management service is implemented based on on Hashicorp’s Vault which has web-based GUI and is supported by
many client tools and libraries. However, using Secret management service via FedCloud client is strongly recommended
as it is working out of the box and have advanced features.

Prerequisite
************

* The latest version of FedCloud client (1.2.18 and higher) is installed. If not, install the client:

::

    $ pip3 install -U fedcloudclient

* Valid access token from EGI Check-in, either from `EGI Check-in Token Portal <https://aai.egi.eu/token>`_
  or `oidc-agent <https://indigo-dc.gitbook.io/oidc-agent/>`_, is set to environment variable:

::

    $ export OIDC_ACCESS_TOKEN=<ACCESS_TOKEN>

Usage
*****













The Secret management service is build on the base of Hashicorp’s Vault which is well-known and
well-supported by different tools and services. However, using the service via Vault CLI is not comfortable: users
need at least two steps for creating/getting a secret: first log in to the service and get Vault's token with
EGI Check-in access token, then read/write secrets with the Vault's token. The FedCloud client module for accessing
secrets is being developed to solve the issues.

Expected features of FedCloud client module for the Secret management service:

Integration with EGI Infrastructure, working out of the box
***********************************************************

As the native command-line client for EGI Cloud infrastructure, FedCloud client is tightly integrated with different
EGI services. The client should have default setting tailored to EGI infrastructure environment and work out of the
box without requiring additional configuration from end-users. Single execution of a simple command is sufficient for
most of operations:

::

    $ fedcloud secret list
    first_secret
    second_secret

    $ fedcloud secret get first_secret
    key    value
    -----  -------
    key1   secret_value


Support for advanced features requested by user communities
***********************************************************

Some advanced features requested by user communities are already planned: native support for using small files as
secret values, encryption/decryption of secrets on the fly and so on. The module is in active development, more
features are being added, so stay tuned.

