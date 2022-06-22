Qhickstart
==========

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

As FedCloud client is tightly integrated with Secret management service, no additional setting is required. Users can
access the service immediately with simple commands:

* Create a secret in Secret management service, with a name "my_first_secret", store a string "My secret word" in
the key "mykey":

::

    $ fedcloud secret put my_first_secret mykey="My secret word"

* List secrets stored in Secret management service:

::

    $ fedcloud secret list
    my_first_secret

* Get a secret from Secret management service. If the key is given, the client will print only the secret value stored
in the key (useful for scripting), otherwise it prints the table of all key:value pairs.

::

    $ fedcloud secret get my_first_secret
    key    value
    -----  --------------
    mykey  My secret word

    $ fedcloud secret get my_first_secret mykey
    My secret word



