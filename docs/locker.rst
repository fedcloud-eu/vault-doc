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

