# Caddy-Simple
Deploy Simple-Login App Behind Caddy 2.3.0 using Docker Compose

## DKIM
First you need to generate a private and public key for DKIM:

```
openssl genrsa -out dkim.key 1024
openssl rsa -in dkim.key -pubout -out dkim.pub.key
```
We will need the files ``dkim.key`` and ``dkim.pub.key``` for the next steps.

For email gurus, we have chosen 1024 key length instead of 2048 for DNS simplicity as some registrars don't play well with long TXT record.
