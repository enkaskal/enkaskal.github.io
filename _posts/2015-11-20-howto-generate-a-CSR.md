---
layout: post
title: "HOWTO generate a certificate signing request (CSR)"
description: "HOWTO generate a certificate signing request (CSR)"
keywords: "howto openssl csr certificate signing request"
---
# <center><span style="color:red">subjectAltName must always be used (RFC 2818 4.2.1.7, 1. paragraph)</span></center>

# <center><span style="color:red">If you set subjectAltName, you have to use it for all host names, email addresses, etc.<br>NOT JUST THE “ADDITIONAL” ENTRIES.</span></center>

> CN is only evaluated if subjectAltName is not present and only for compatibility with old, non-compliant software.

> As of chrome 58+ this is why you will have problems and should follow these instructions :)

# DoWorkSon

Enough with the warnings...let’s get shit done!

# Copy openssl.cnf

while not necessary since the openssl.cnf has the CA sections separate from the user sections, I like to get my CA setup self-contained in a directory.

> your cwd needs to be wherever you’re gonna generate. if you don’t know what that means quit now!

* Ubuntu/Debian
```
cp /etc/ssl/openssl.cnf openssl-san.cnf
```
* RedHat/CentOS
```
cp /etc/pki/tls/openssl.cnf openssl-san.cnf
```
* Cygwin (MinGW?)
```
cp /usr/ssl/openssl.cnf openssl-san.cnf
```
* MacOSX (El Capitan)
```
cp /System/Library/OpenSSL/openssl.cnf openssl-san.cnf
```

# Edit openssl-san.cnf

```
...
[req]
req_extensions = v3_req
[v3_req]
# Extensions to add to a certificate request
basicConstraints = CA:FALSE
keyUsage = nonRepudiation, digitalSignature, keyEncipherment
subjectAltName = @alt_names
[alt_names]
DNS.1 = server1.yourdomain.tld
DNS.2 = mail.yourdomain.tld
DNS.3 = www.yourdomain.tld
DNS.4 = www.sub.yourdomain.tld
DNS.5 = mx.yourdomain.tld
DNS.6 = support.yourdomain.tld
```

# Generate the CSR

My notes on generating private keys and CSR’s. (that I never remember)

Used with, for example, a new HTTPS server.

## Simultaneous key and request generation

```
openssl req -newkey rsa:4096 -nodes -keyout cryp7.net.key -out cryp7.net.csr -days 365
```

> `-nodes` places an *unencrypted* copy of the private key in the server’s key file. This is typically used so that the sysadmin doesn’t need to type in the password when restarting apache; for example.

> **!!!You should secure this with restrictive permissions at a minimum!!!**

Answer the prompts noting:

1. CN (A.K.A Common Name) section is the name of the server (e.g. cryp7.net) 
2. extra attributes (namely challenge password and optional company name) can be ignored by using the enter (return) key

### Setting permissions on the new key

```
chown root:root cryp7.net.key
chmod 0400 cryp7.net.key
```

## Generate a CSR using an existing key

Used, for example, to sign your own intermediate CA certificate

```
openssl req -new -config openssl.cnf -key private/vpn.ca.key -out vpn.ca.csr -days 1825
```

> since you’re specifying the openssl.cnf, all prompts should all be defaulted as desired.

# Request signing

Now send off the server.csr to your CA.

> once sent, the CSR is no longer needed.

# Prepare a PKCS#12 file

Combines the public and private key in an encrypted format (symmetrical) for use with email clients, etc.

* Before 2011–02–19 (needs the CA cert)
```
openssl pkcs12 -export -in my.crt -inkey my.key -in root.pem -out my.p12
```
* On or after 2011–02–19 (doesn’t need CA cert)
```
openssl pkcs12 -export -in my.crt -inkey my.key -out my.p12
```

# See Also

* [HOWTO become your own CA](https://cryp7.net/2015/howto-become-your-own-CA/)

# References

* <http://www.g-loaded.eu/2005/11/10/be-your-own-ca/>
* <http://wiki.cacert.org/EmailCertificates>
* <http://wiki.cacert.org/FAQ/subjectAltName>
