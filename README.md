# Document Signing with custom certificate chain

## Generate Certificate chain
### Create Root Key

**Attention:** this is the key used to sign the certificate requests, anyone holding this can sign certificates on your behalf. So keep it in a safe place!

```bash
openssl genrsa -des3 -out rootCA.key 4096
```

#### Create Root Certificate
```bash
openssl req -x509 -new -nodes -key rootCA.key -sha256 -days 1024 -out rootCA.crt
```

Here we used our root key to create the root certificate that needs to be distributed in all the computers that have to trust us.

### Create a Certificate

#### Create the certificate key

```
openssl genrsa -out mydomain.com.key 2048
```

### Create public key
```
openssl rsa -in mydomain.com.key -pubout > mydomain.com.pub
```

#### Create the signing request

**Important:** Please mind that while creating the signign request is important to specify the `Common Name` providing the IP address or URL for the service, otherwise the certificate
cannot be verified

```
openssl req -new -key mydomain.com.key -out mydomain.com.csr
```

#### Generate the certificate using the `mydomain` csr and key along with the CA Root key

```
openssl x509 -req -in mydomain.com.csr -CA rootCA.crt -CAkey rootCA.key -CAcreateserial -out mydomain.com.crt -days 500 -sha256
```
at `-days 500`, you can specify your certificate's life

## Signing a Document with your created Certificate

### Signing
```
openssl dgst -sha256 -sign mydomain.com.key -out data.txt.sha256 data.txt 
```
## Verifying a Document
```
openssl dgst -sha256 -verify mydomain.com.pub -signature data.txt.sha256 data.txt
```