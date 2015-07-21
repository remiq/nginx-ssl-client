# Cert generation

This document shows how to set up simple SSL test environment.
Based on: http://nategood.com/client-side-certificate-authentication-in-ngi

## ca.crt

    openssl genrsa -des3 -out ca.key 4096

Requires setting new password. Ie. "test"
Returns `ca.key`, key used by Certificate Authority to sign client/server keys.

    openssl req -new -x509 -days 365 -key ca.key -out ca.crt

Requires a lot of data about our fake CA. Fill with random data.
Returns `ca.crt`, certificate used by nginx to check if other certificates are valid.

## server.crt

    openssl genrsa -des3 -out server.key 1024

Creating new key. Same as with ca.key.

    openssl req -new -key server.key -out server.csr

Creating request to sign our key. Normally, *.csr file would be sent to CA, but...

    openssl x509 -req -days 365 -in server.csr -CA ca.crt -CAkey ca.key -set_serial 01 -out server.crt

We sign it ourselves and we get `server.crt`. This file will be used by nginx.

## client.crt

    openssl genrsa -des3 -out client.key 1024

Creating new key. Same as with server.key. This step is done by a client.

    openssl req -new -key client.key -out client.csr

Client generates request, sends it to his CA.

    openssl x509 -req -days 365 -in client.csr -CA ca.crt -CAkey ca.key -set_serial 01 -out client.crt

Client's request is signed by CA using ca private key. Client recieves it and uses it in browser.

## Test it with cURL

    $ curl -v -s -k --key client.key --cert client.crt --cacert ca.crt https://localhost:8443/

* successfully set certificate verify locations:
*   CAfile: ca.crt
  CApath: /etc/ssl/certs
(...)
* SSL connection using ECDHE-RSA-AES256-GCM-SHA384
* Server certificate:
*        subject: C=io; ST=invalid; L=Test; O=Server Inc; OU=server; CN=localhost; emailAddress=test@localhost
*        start date: 2015-07-20 15:47:38 GMT
*        expire date: 2016-07-19 15:47:38 GMT
*        issuer: C=io; ST=test; L=Test; O=Fake Inc.; OU=Test; CN=test.invalid; emailAddress=test@test.invalid
*        SSL certificate verify ok.



## client.p12 - this does not work

To use certificate in Firefox, we need to export it to pkcs12

    openssl pkcs12 -export -inkey client.key -in client.crt -out client.p12

We recieve .p12 file, that we can import in browser.
