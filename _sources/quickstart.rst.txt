Quickstart
==========

Just install FedCloud client, get an access token and you can use Secret management service. No additional setting
required. The basic commands are as follows:

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
