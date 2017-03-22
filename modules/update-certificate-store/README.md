# Update Certificate Store

This folder contains a script for adding a trusted, TLS certificate to an OS's certificate store. This allows 
you to establish TLS connections to services that use that TLS cert without getting x509 certificate errors. This 
script has been tested on the following operating systems:

* Ubuntu 16.04
* Amazon Linux

There is a good chance it will work on other flavors of Debian, CentOS, and RHEL as well.





## Motivation

Let's say you deployed a server (e.g. a Vault server) with a self-signed TLS certificate. If you try to make a request 
to that server using some sort of TLS client (e.g. a Vault client), you will get an error:

```
vault read secret/foo

Error initializing Vault: Get https://127.0.0.1:8200/v1/secret/foo: x509: certificate signed by unknown authority
```

You can get around this error by explicitly telling the client to trust the TLS cert's public key:

```
vault read -ca-cert=/opt/vault/tls/vault.crt.pem secret/foo

Key                 Value
---                 -----
refresh_interval    768h0m0s
value               bar
```

Having to pass the `-ca-cert` argument every time gets tedious. This module offers a way to configure the entire OS
to trust this certificate.




## Quick start

To use the `update-certificate-script`, use `git` to clone this repository at a specific tag (see the 
[releases page](../../../../releases) for all available tags) and run the `update-certificate-script` script:

```
git clone --branch <VERSION> https://github.com/gruntwork-io/vault-aws-blueprint.git
vault-aws-blueprint/modules/update-certificate-script/update-certificate-script --cert-file-path /opt/vault/tls/vault.cert.pem
```

That's it!

Now you can make calls to services that use this TLS cert, and you won't get any errors:

```
vault read secret/foo

Key                 Value
---                 -----
refresh_interval    768h0m0s
value               bar
```

See the [vault-consul-ami example](/examples/vault-consul-ami) for working sample code.





## Command line Arguments

The `run-vault` script accepts the following arguments:

* `--cert-file-path` (required): The path to the TLS certificate public key to add to the OS certificate store.
* `--dest-file-name` (optional): This script will copy `--cert-file-path` to a file with this name in a shared 
  certificate folder on the OS. The default file name is `custom.crt`, but you can use this parameter to customize 
  it. The extension MUST be `.crt` or the OS will ignore the file.

Example:

```
vault-aws-blueprint/modules/update-certificate-script/update-certificate-script --cert-file-path /opt/vault/tls/vault.cert.pem
```
