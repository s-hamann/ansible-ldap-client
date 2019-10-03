LDAP Client
===========

This role prepares the system to connect to an LDAP directory which requires a client certificate.
For this purpose, [stunnel](https://www.stunnel.org/) is set up as a local proxy which accepts unencrypted LDAP traffic and forwards it to the directory server via an encrypted connection.
This proxy handles the client certificate, but not binding to the directory itself.

Requirements
------------

The client certificate and matching private key need to be present on the target system.
This roles does not handle generating or deploying them.

Role Variables
--------------

* `ldap_client_extra_groups`  
  A list of groups that the stunnel system user is added to.
  This allows granting access to additional resources, such as the private key file.
  All groups need to exist on the target system; this role does not create them.
  Empty by default.
* `ldap_client_inaccessible_paths`  
  If the target system uses systemd, this option takes a list of paths, that should not be accessible at all for stunnel.
  Regardless of this option, home directories are made inaccessible and the rest of the file system is mostly read-only.
  Optional.
* `ldap_client_server`  
  The LDAP server host name or IP address to proxy to.
  The system needs to support the LDAPS protocol; STARTTLS is not sufficient.
  Mandatory.
* `ldap_client_trusted_ca`  
  Path to a file containing the PEM-encoded X.509 certificate of the LDAP server's issuing CA.
  If not set, the system's certificate store is used.
* `ldap_client_tls_cert`  
  Path to a PEM-encoded X.509 client certificate for stunnel to use.
  The file needs to exist and be readable by the stunnel user.
  Mandatory.
* `ldap_client_tls_cert_key`  
  Path to the PEM-encoded private key file for the certificate.
  The file needs to exist and be readable by the stunnel user.
  Mandatory.
* `ldap_client_tls13_only`  
  If set to `true` this role attempt enforcing TLSv1.3 only.
  If the target system's OpenSSL version does not support TLSv1.3 or if `ldap_client_tls13_only` is `false`, TLSv1.2 is enforced as the minimal supported protocol version.
  Defaults to `false`.

Example Playbook
----------------

```yaml
- hosts: servers
  roles:
    - role: ldap-client
      become: true
      ldap_client_servers:
        - ldap.example.org
      ldap_client_tls_cert: /etc/ssl/private/client.pem
      ldap_client_tls_cert_key: /etc/ssl/private/client.key
```

License
-------

MIT
