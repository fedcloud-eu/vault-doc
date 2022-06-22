User guides
===========

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

Quickstart
**********

As FedCloud client is tightly integrated with Secret management service, no additional setting is required. Users can
access the service immediately with simple commands:

* Create a secret in Secret management service, with a name ``my_first_secret``, store a string ``My secret word`` in
  the key ``mykey``:

::

    $ fedcloud secret put my_first_secret mykey="My secret word"

* List secrets stored in Secret management service:

::

    $ fedcloud secret list
    my_first_secret

* Get a secret from Secret management service. If a key is given, the client will print only the secret value stored
  in the key (useful for scripting), otherwise it will print the table of all key:value pairs.

::

    $ fedcloud secret get my_first_secret
    key    value
    -----  --------------
    mykey  My secret word

    $ fedcloud secret get my_first_secret mykey
    My secret word

* Delete a secret from Secret management service.

::

    $ fedcloud secret delete my_first_secret


Secret values from files
************************

If the value string is started with "@", the FedCloud client will read the content of the file with the name for the
value for the key. The following command creates a secret in Secret management service with a name ``certificate`` and
store host certificate and key in the secret:

::

    $ fedcloud secret put certificate cert=@hostcert.pem key=@hostkey.pem

Users can get the certificate on VM as follows:

::

    $ fedcloud secret get certificate cert > hostcert.pem
    $ fedcloud secret get certificate key  > hostkey.pem

So far, only text files are supported. For binary files, it is recommended to use ``uuencode`` command for encoding
the files as texts before uploading and ``uudecode`` for converting the content back to the original format.

The limit of the secret size is 512kB, it is sufficient for storing certificates, service configuration files and
so on. For larger datasets, please use permanent cloud storages.

Encrypted secrets
*****************

For highly-sensitive secrets, users can choose to encrypt the secret values before uploading it to the service. The
encryption is done automatically on the fly if encryption key (passphrase) is provided:

::

    $ fedcloud secret put certificate cert=@hostcert.pem key=@hostkey.pem --encrypt-key my-pass-phrase

Decryption is done in a similar way, just by providing decryption key, the secret value will be decrypted
automatically:

::

    $ fedcloud secret get certificate cert --decrypt-key my-pass-phrase

Users cam verify what is stored in the Vault by reading the secrets without providing decryption key.

::

    $ fedcloud secret get certificate cert
    gAAAAAB...............................

The encryption is done by standard Python crytography library. Security experts are invited to review the code
(available at `GitHub <https://github.com/tdviet/fedcloudclient/blob/master/fedcloudclient/secret.py#L124>`_)
and give feedback and suggestions for improvements if possible.

