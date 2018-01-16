# What is it?

This Ansible role configures basic IPsec IKEv2 VPN server. With default values, connections from most devices (Android and Apple phones, macOS, Windows and Linux) work without any extra configuration on client side, only username, password and CA certificate is required. You definitely want to override `vpn_ikev2_client_list`, `vpn_ikev2_server_common_name`, `vpn_ikev2_server_certificate` and `vpn_ikev2_server_private_key`. Example on how to create self-signed CA and server certificate goes below. Running this role on fresh Ubuntu installation will make you working VPN server at no additional cost.

# Prerequisites

* Ansible 2.4+
* Ubuntu Xenial is only supported target host operating system for the moment.

# Example: IPsec VPN key generation

Generate CA RSA key pair:

`ipsec pki --gen --outform pem > ca.key`

Generate self-signed CA (adjust DN):

`ipsec pki --self --in ca.key --dn "C=DE, O=Acme Corp, CN=Acme Corp VPN Server CA" --ca --outform pem > ca.crt`

Generate server RSA key:

`ipsec pki --gen --outform pem > server.key`

Generate VPN server certificate (adjust DN):

`ipsec pki --pub --in server.key | ipsec pki --issue --cacert ca.crt --cakey ca.key --dn "C=DE, O=Acme Corp, CN=vpn-server.example.com" --outform pem --san vpn-server.example.com > server.crt`

Install `ca.crt` to trusted storage on client devices. Use `server.crt` and `server.key` with this role (see `defaults/main.yml`)

Refer to strongSwan [documentation](https://wiki.strongswan.org/projects/strongswan/wiki/SimpleCA) for more information on certificates and keys.