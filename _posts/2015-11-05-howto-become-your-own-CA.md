---
layout: post
title: "HOWTO become your own certificate authority (CA)"
description: "My notes (that I never remember) on creating a self-signed root CA"
keywords: "howto openssl root ca self-signed certificate authority"
---
My notes (that I never remember) on creating a self-signed root certificate authority (CA).

# Create the CA

First, setup the standard directory structure

```
mkdir ca
cd ca
mkdir certs newcerts crl requests
mkdir -m 0700 private
```

> the rest of this document presumes your working directory is ca!!!

## Copy openssl.cnf

while not necessary since the openssl.cnf has the CA sections separate from the user sections, i like to get my ca setup self contained in a directory

* cygwin
```
cp /usr/ssl/openssl.cnf ./
```
* RedHat/CentOS
```
cp /etc/pki/tls/openssl.cnf ./
```
* Ubuntu/Debian
```
cp /etc/ssl/openssl.cnf ./
```

* MacOSX (El Capitan)
```
cp /System/Library/OpenSSL/openssl.cnf ./
```

## Set openssl.cnf permissions

```
chmod 0600 openssl.cnf
```

## Edit openssl.cnf

```
[CA_default]
dir = .
certificate = $dir/certs/cryp7.net.ca.crt
private_key = $dir/private/cryp7.net.ca.key
[req_distinguished_name]
countryName_default = US
stateOrProvinceName_default = california
localityName_default = los angeles
0.organizationName_default = cryp7.net
organizationalUnitName_default = information assurance directorate
commonName_default = cryp7.net certificate authority
emailAddress_default = root@cryp7.net
# Extension copying option: use with caution.
copy_extensions = copy # allow SAN CSR cert generation (review v3 extensions when signing with caution!!! especially with policy_anything!)
default_md = sha512 # which message digest to use.
[v3_ca]
keyUsage = cRLSign, keyCertSign
nsCertType = sslCA,emailCA,objCA
```

Setup db and serial files

```
touch index.txt
echo 01 > serial
```

## Generate the CA keypair

```
openssl req -config openssl.cnf -newkey rsa:4096 -sha512 -x509 -extensions v3_ca -keyout private/cryp7.net.ca.key -out certs/cryp7.net.ca.crt -days 1825
```

# Signing a request

When a request is received place the .csr file in the requests directory then execute:

```
openssl ca -config openssl.cnf -policy policy_anything -infiles requests/server.csr
```

There are two default policies defined in a default openssl.cnf: 

1. policy_match (default) — which requires that the country, state, and organizational unit all matches and common name is supplied 
2. policy_anything — common name is supplied

*all other fields are optional*

> to specify a policy on the commandline use -policy policy_name

#### what this does:

* shows you the request and asks if you would like to sign it
* backup database index file (index.txt) to *.old
* stores signing in database index file
* creates database index attributes file (index.txt.attr)
* asks for commit
* backup serial to serial.old
* increments the serial
* stores signed certificate in newcert/NN.pem (this is the filethat should be sent back to the requester)

> using -out certs/server.crt, above, can optionally be used to pick the output path/filename

# Signing an intermediate CA request

While the CSR portion is the same as a regular request, signing is a little different. you must sign a CA as a CA (of course); for example:

```
openssl ca -extensions v3_ca -policy policy_anything -config openssl.cnf -infiles requests/range.cryp7.net.ca.csr
```

# Fingerprinting

* sha-1 (default)
```
openssl x509 -noout -in certs/cryp7ca.crt -fingerprint
```
* md5
```
openssl x509 -noout -in certs/cryp7ca.crt -fingerprint -md5
```
* sha256
```
openssl x509 -noout -in certs/cryp7ca.crt -fingerprint -sha256
```
* sha512
```
openssl x509 -noout -in certs/cryp7ca.crt -fingerprint -sha512
```

#### Generating Diffie-Hellman pairs (e.g. for use with an OpenVPN server)

```
openssl dhparam -out private/dh-params.pem 2048
```

> In the previous example the integer **2048** is the key size and may be modified. See dhparam(1).

> performing this step is equivalent to running `./build-dh` in section “Generate Diffie Hellman parameters” of [Setting up your own Certificate Authority (CA) and generating certificates and keys for an OpenVPN server and multiple clients](http://www.openvpn.net/index.php/open-source/documentation/howto.html#pki).

# File extension definitions

* key — private key
* csr — certificate signing request
* crt — certificate
* crl — certificate revocation list
* pem — base64 encoded x.509 certificate

# See Also

* [HOWTO generate a CSR](https://cryp7.net/2015/howto-generate-a-CSR/)

# References

* <http://www.tldp.org/HOWTO/SSL-Certificates-HOWTO/index.html>
* <http://www.g-loaded.eu/2005/11/10/be-your-own-ca/>
* <http://sial.org/howto/openssl/>
* <http://samat.org/cheat_sheets/openssl>
* [OpenVPN HOWTO](http://www.openvpn.net/index.php/open-source/documentation/howto.html)

# Changelog

* 20151105 — migrated to medium
* 20170321 — migrated from sha1 to sha2 (sha512)
* 20170518 - migrated to github pages
