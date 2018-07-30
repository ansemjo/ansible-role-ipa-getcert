# ipa-getcert

Receive a host certificate through `ipa-getcert` on a FreeIPA enrolled host.

## Usage

By default this role will attempt to request a certificate for the host and saves key / certificate
at `/etc/pki/tls/private/{{ ansible_fqdn }}.key` / `/etc/pki/tls/certs/{{ ansible_fqdn }}.crt`.

```yaml
#!/usr/bin/env ansible-playbook
---
- hosts: app.mydomain.com
  gather_facts: true
  become: true

roles:

    - role: ansemjo.ipa-getcert
      tags: [ getcert ]

    - ...
```

### requested hostnames

If you want to change the list of certificates to request use:

```yaml
# list of hostnames to be requested in seperate certificates
ansemjo_ipa_getcert_request_hostnames:
  - "{{ ansible_fqdn }}"
  - "another.example.com"
```

Right now each requested certificate needs to belong to a seperate host entry in FreeIPA. Support
for principal aliases may be added later.

### localhost symlink

The role creates a symlink for the localhost certificates at `/etc/pki/tls/private/localhost.key` /
`/etc/pki/tls/certs/localhost.crt`. The first entry in `_request_hostnames` will be used. If you
want to disable this, set the following to `false`:

```yaml
# symlink the certificates for ansible_fqdn to localhost.{crt,key}?
ansemjo_ipa_getcert_symlink_fqdn: true
```

### chain assembly

For some applications it may be useful to have both certificate and key concatenated in a single PEM
file. In this case use:

```yaml
# assemble crt and key in a single file in ..storage_chain/*.pem?
ansemjo_ipa_getcert_pem_chain: true
ansemjo_ipa_getcert_pem_chain_group: myapp
```

This will assemble said chain in `/etc/pki/tls/chain/{{ fqdn }}.pem` with mode `0640` and `myapp`
group.
