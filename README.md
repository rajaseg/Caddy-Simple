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

## DNS
Please note that **DNS** changes could take up to 24 hours to propagate. In practice, it's a lot faster though (~1 minute or so in our test). In DNS setup, we usually use domain with a trailing dot (```.```) at the end to to force using absolute domain.

### MX Record
Create a **MX Record** that points ```example.com``` to ```app.example.com.``` with priority 10.

### A Record
An **A Record** that points ```app.example.com.``` to your server IP. If you are using CloudFlare, please ```disable``` the ```Proxy``` option.

### DKIM
Set up ```DKIM``` by adding a ```TXT Record``` for ```dkim._domainkey.example.com.``` with the following value:

```v=DKIM1; k=rsa; p=PUBLIC_KEY```

with ```PUBLIC_KEY``` being your ```dkim.pub.key``` but

- Remove the ```-----BEGIN PUBLIC KEY-----``` and ```-----END PUBLIC KEY-----```
- Join all the lines on a single line

For example, if your ```dkim.pub.key``` is

```
-----BEGIN PUBLIC KEY-----
ab
cd
ef
gh
-----END PUBLIC KEY-----
```
then the ```PUBLIC_KEY``` would be ```abcdefgh```.

### SPF
Similar to ```DKIM```, setting up ```SPF``` is highly recommended. Add a ```TXT Record``` for ```example.com.``` with the value:

```v=spf1 mx -all```

### DMARC
Setting up ```DMARC``` is also recommended. Add a ```TXT Record``` for ```_dmarc.example.com.``` with the following value
```v=DMARC1; p=quarantine; adkim=r; aspf=r```

This is a ```relaxed``` DMARC policy. You can also use a more strict policy with ```v=DMARC1; p=reject; adkim=s; aspf=s``` value.
