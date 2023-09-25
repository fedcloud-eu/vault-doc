Lockers
=======

The Locker mechanism introduces an innovative and robust approach to securely deliver secrets to VMs.
Users can effortlessly create a locker, deposit their secrets within it, and then furnish the locker's
token to their VMs. Key security attributes of the locker system include:

* **Temporary and autoclean**: Lockers have a limited lifespan and quantity. Upon expiration, lockers are
  automatically purged, along with all the secrets contained within them.

* **Isolation**: Access to the secrets within a locker is exclusively through its associated token, which
  can solely be used for accessing the locker's secrets—nothing more. This isolation allows users to store
  tokens in Continuous Integration/Continuous Deployment (CI/CD) pipelines and similar tools, mitigating
  the risk of exposing personal secrets.

* **Malfeasance detection**: The locker mechanism possesses the capability to detect if a token has been
  compromised and is being misused.

Basic usage
***********

* **Create a locker**: Users can explicitly define the number of uses and time-to-live

::

    $ fedcloud secret locker create
    hvs.CAESIGXXX

    $ fedcloud secret locker create --ttl 24h --num-uses 10 –-verbose
    key              value
    ---------------  -------------------------------------------------------
    client_token     hvs.CAESIGXXX        <= This is the token
    accessor         o3GXXXXXXXXXXXXX
    policies         ['default']
    token_policies   ['default']
    lease_duration   86400
    renewable        False
    orphan           False
    num_uses         10

* **Using a locker**: Add the option `--locker-token locker-token` and use `fedcloud secret put/get/list`
  as normally.

::

    $ fedcloud secret put mysecret password=123456 --locker-token hvs.CAESIXXX


The locker token may be set as OS environment variable like access token

::

    $ export FEDCLOUD_LOCKER_TOKEN=hvs.CAESIXXX
    $ fedcloud secret get mysecret

* **Checking and destroying lockers**: Users can check locker information and destroy it if needed. If not,
  lockers will be automatically deleted if the number of uses or TTL are expired.

::

    $ fedcloud secret locker check hvs.CAESIXXX
    key               value
    ----------------  -----------------------------------------------------
    accessor          qb52XXXXXX
    creation_time     1685008416
    creation_ttl      86400
    ...

    $ fedcloud secret locker revoke hvs.CAESIXXX

