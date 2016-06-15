# OpenSSL

## Prep

```
mkdir ~/ssl
cd ~/ssl
echo '01' > serial
touch index.txt
```

copy openssl/openssl.cnf to ~/ssl and adjust it to your needs.

## CA setup

```
mkdir CA
openssl req -new -x509 -days 3650 -extensions v3_ca -keyout CA/ca.key -out CA/ca.crt -config openssl.cnf
```

* Choose a strong pass phrase to protect your CA. This pass phrase is required for all actions arround your CA, eg. signing new certificates
* Choose a name for 'Organizaton Name' which identifies your CA, eg. your name or company's name

## Webserver Certificate

If you already have a certificate for your webserver, you can skip this section. The used certificate does not have to be signed by the same CA.

Create a new certificate request for the webserver:

```
openssl req -new -nodes -out reqs/serverdomain.req -keyout private/serverdomain.key -config openssl.cnf
```

* At 'Common Name' type in the domain your service will be available at.

Create a new certificate from the certificate request:

```
openssl ca -config openssl.cnf -out certs/serverdomain.crt -infiles serverdomain.req
```

## Client Certificates

Create a certificate request for the client:

```
openssl req -keyout private/client1.key -new -out reqs/client1.req -config openssl.cnf
```

* Leave the PEM password blank if you are going to export and distribute it as pkcs12 file
* Use a user or client name for 'Common Name'

Create a new certificate for the certificate request:

```
openssl ca -config openssl.cnf -out certs/client1.crt -infiles reqs/client1.req
```

Export client cert and key as pkcs12 file:

```
openssl pkcs12 -export -inkey private/client1.key -in certs/client1.crt -name client1 -out client1.p12
```

* use a strong passphrase for exporting
* the created p12 file can be imported into your browser or OS

### Webserver Configuration

Copy the following files to your webserver: (eg. /etc/apache2/ssl)

* CA/ca.crt
* certs/serverdomain.crt, private/serverdomain.key (or use your existing certificates)

You do not need to make any other files accessible by the webserver!
For security reasons it's a good idea to manage your CA on an other machine than your webserver.

Add config to Apache VirtualHost config:

```
SSLEngine on
SSLCertificateFile      /etc/apache2/ssl/serverdomain.crt
SSLCertificateKeyFile /etc/apache2/ssl/serverdomain.key
SSLCACertificateFile /etc/apache2/ssl/ca.crt
SSLVerifyClient require
SSLVerifyDepth 1
```

Restart apache

et voila, access to the vhost should only be possible with installed client certificate

njoy!

### Certificate revocation

TODO
