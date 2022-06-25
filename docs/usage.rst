User guides
===========

Secret management service is implemented on the base of `Hashicorp’s Vault <https://www.vaultproject.io/>`_
which has web-based GUI and is supported by
many client tools and libraries. However, using Secret management service via
`FedCloud client <https://fedcloudclient.fedcloud.eu/>`_ is strongly recommended
as the client is tightly integrated with the service, it works out of the box without additional configuration,
has simple syntax and also advanced features like encrypted secrets.

Concepts of Secret objects
**************************

Secret objects in Secret management service are identified by their paths, like files on disk. Each user has a private
secret space for storing his secret objects and cannot see secret objects from other users. Each secret object may
contain several secret values, each value is identified by its key. For example, a secret object for a service in Cloud
may contain several different passwords for different components: database, webservice and so on. Secret objects in
Secret management service are created, retrieved, deleted as the whole, users cannot edit individual secret values of an
existing secret.

Prerequisite
************

.. highlight:: console

* The latest version of `FedCloud client <https://fedcloudclient.fedcloud.eu/>`_
  (1.2.18 and higher) is installed. If not, install or upgrade the client:

::

    $ pip3 install -U fedcloudclient

* Valid access token from EGI Check-in for authentication, either from
  `EGI Check-in Token Portal <https://aai.egi.eu/token>`_
  or `oidc-agent <https://indigo-dc.gitbook.io/oidc-agent/>`_, is set to environment variable:

::

    $ export OIDC_ACCESS_TOKEN=<ACCESS_TOKEN>

Basic usage
***********

As FedCloud client is tightly integrated with Secret management service, no additional setting is required. Users can
access the service immediately with simple commands without understanding details described in the Section
`Service design and implementation <https://vault.docs.fedcloud.eu/design.html>`_. There is a quickstart:

* Create a secret object ``my_app_secrets`` in Secret management service, store MySQL and admin passwords in the
  secret object:

::

    $ fedcloud secret put my_app_secrets mysql_password=123456 admin_password=abcdef

* List secret objects stored in Secret management service:

::

    $ fedcloud secret list
    my_app_secrets

* Read a secret object from Secret management service. If a key is given as parameter, the client will print only the
  secret value corresponding to the key (what is useful for scripting), otherwise it will print the table of all
  key:value pairs.

::

    $ fedcloud secret get my_app_secrets
    key             value
    --------------  -------
    admin_password  abcdef
    mysql_password  123456


    $ fedcloud secret get my_app_secrets mysql_password
    123456

* Delete a secret object from Secret management service.

::

    $ fedcloud secret delete my_app_secrets


Secret values from small text files
***********************************

If the value string is started with ``@``, the FedCloud client will read the content of the file with the name for the
value of the key. The following command creates a secret object ``certificate`` in Secret management service for storing
host certificate and host key:

::

    $ fedcloud secret put certificate cert=@hostcert.pem key=@hostkey.pem

Users can get the certificate latter on the target VM as follows:

::

    $ fedcloud secret get certificate cert > hostcert.pem
    $ fedcloud secret get certificate key  > hostkey.pem

So far, only values from text files are supported. For binary files, it is recommended to use ``uuencode`` command
for encoding the files as texts before putting them to the service  and ``uudecode`` for converting the content back
to the original format.

The limit size of the secret object is 512kB, it is sufficient for storing tokens, certificates, configuration files
and so on. For larger datasets, please use permanent cloud storages.

Encrypted secrets
*****************

For highly-sensitive secrets, users can choose to encrypt the secret values before putting them to the service. The
encryption is done automatically on the fly if an encryption key (passphrase) is provided:

::

    $ fedcloud secret put certificate cert=@hostcert.pem key=@hostkey.pem --encrypt-key my-pass-phrase

Decryption is done in a similar way, just by providing decryption key when reading, the secret values will be decrypted
automatically if the key is correct:

::

    $ fedcloud secret get certificate cert --decrypt-key my-pass-phrase

Users can verify what is actually stored in the Secret management service by reading the secret object without
providing decryption key:

::

    $ fedcloud secret get certificate cert
    gAAAAAB...............................

The encryption/decryption is done by standard Python crytography library. Security experts are invited to review
the code (available at `GitHub <https://github.com/tdviet/fedcloudclient/blob/master/fedcloudclient/secret.py#L124>`_)
and give feedback and suggestions for improvements if possible.

Export and import secrets
*************************

Users can print secret objects to files YAML/JSON format for further processing by option ``--output-format``
or simply ``-f``:

::

    $ fedcloud secret get my_app_secrets -f json

    $ fedcloud secret get my_app_secrets -f yaml > my_app_secrets.yaml

The secret objects in YAML/JSON files can be imported back to the service by adding ``@`` before filenames as parameters,
telling client to read secret objects from files:

::

    $ fedcloud secret put my_second_app_secrets @my_app_secrets.yaml


Note the difference in examples: ``cert=@hostcert.pem`` for reading the content of the file ``horstcert.pem`` as the
value for the key ``cert``, and ``@my_app_secrets.yaml`` for reading whole secret object with all key:value pairs
from the YAML file.

As YAML format is a superset of JSON, it is expected by default unless the filename has ``.json`` extension. Try to
export your secrets to both formats to see the differences between formats.

Importing secret objects from files in free text format ``key=value`` is not supported as the format is error-prone,
especially for multi-line secret values or values with special characters. Users can replace ``=`` to ``:`` for
converting simple free text files to YAML format. Note that a blank space after ``:`` is required by YAML syntax.

Modifying existing secrets
**************************

As mentioned in the Concepts above, secret values in secret objects cannot be edited individually in the service.
However, users can get the contents of existing secret objects, change them locally, then put the new contents
back to the service. For examples:

* Adding new secret values to an existing secret object:

::

    $ fedcloud secret get certificate -f json > certificate.json

    $ fedcloud secret put certificate @certificate.json another_cert=@usercert.pem another_key=@userkey.pem

* Deleting secret values in an existing secret object:

::

    $ fedcloud secret get certificate -f json | jq 'del (.another_cert, .another_key)' > certificate.json

    $ fedcloud secret put certificate @certificate.json

* Updating secret values in an existing secret object:

::

    $ fedcloud secret get certificate -f json > certificate.json

    $ fedcloud secret put certificate @certificate.json cert=@new_hostcert.pem key=@new_hostkey.pem
