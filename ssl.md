# Secure Sockets Layer (SSL)

## OpenSSL

### Creating a self-signed certificate

```bash
openssl req \
  -x509 \
  -sha256 \
  -nodes \
  -days 365 \
  -newkey rsa:2048 \
  -keyout privateKey.key \
  -out certificate.crt \
  -subj "/C=SG/ST=Singapore/L=Singapore/O=Organisation/OU=OrganisationUnit/CN=www.example.com"
```