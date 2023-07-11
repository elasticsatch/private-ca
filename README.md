# private-ca
Ansible playbook to create a self-signed root CA and default TLS server certificate

## Description
This playbook deploys a set of private self-signed root CA artifacts to a non-dockered NON-PRODUCTION server and issue a general TLS server certificate for building SSL/TLS environment. The format is PEM, which you can change to other format (DES, EC2) if you are familiar with Ansible.

## How to Create Private CA
1. Install ansible on your host (or Ansible "master" server)
1. Clone the repo by `git clone https://github.com/satiiza/private-ca.git`
1. Modify configurations in hosts or `roles/ca/vars/main.yml`. Available variables can be found in `roles/ca/defaults/main.yml`.
1. Execute `ansible-playbook --ask-become-pass site.yml`

Once the playbook runs successfully, it generates:

1. A set of private key and public key for the private root CA  
1. A set of private key and public key for the general TLS use

on the target server listed in the `hosts.yml`.

### input:
- `{{ root_ca_conf_file }}`: openssl config file for the Root CA certificate

### output: 
- `{{ root_ca_key_file }}`: The Root CA key to sign CSRs
- `{{ root_ca_crt_file }}`: The Root CA certificate PEM file 
- `{{ tls_certs_dir }}/{{ tls_server_host }}.crt`: A TLS certificate with top-level domain specified in `root_ca_conf_file`.
- `{{ tls_private_dir }}/{{ tls_server_host }}.key`: A paired key file for the certificate above

## Issue Your Own TLS Certificates
You can issue an extra keypair (crt & key) by executing `gen-cert.sh` under `{{ root_ca_dir }}`. Please run the shell with `-h` to see the available options.

## Tips and Tricks

The trick is to use `openssl ca -selfsign` when generating the self-signed CA certificate.  This enables `openssl ca` to sign the CSR, while the one generated by `openssl x509` doesn't, which often introduced on the web in a wrong way.

## Reference and Special Thanks To
- https://pki-tutorial.readthedocs.io/en/latest/simple/
- https://stackoverflow.com/questions/21297139/how-do-you-sign-a-certificate-signing-request-with-your-certification-authority
- https://gist.github.com/Soarez/9688998

