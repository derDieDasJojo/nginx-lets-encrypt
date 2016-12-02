# nginx-lets-encrypt reverseproxy

this is based on [lerenn/lets-encrypt-reverse-proxy](https://github.com/lerenn/docker-images/lets-encrypt-reverse-proxy)

Docker image for a https-safe reverse proxy, powered by nginx and lets encrypt.

**IMPORTANT**: Please read informations about Let's Encrypt at the end of this document.

## Configure

configure the environment variables for the certification in the secrets.env file

    cp secrets.default.env secrets.env env
    vim secrets.env

## Build

To create the image `derdiedasjojo/lets-encrypt-reverse-proxy`, execute the following command on the reverse-proxy project folder:

    docker build -t derdiedasjojo/lets-encrypt-reverse-proxy .

## Run

If you want to use a reverse-proxy on HTTP and/or HTTPS websites, here is the basic command :

    docker run -d -p 80:80 -p 443:443 -v /path/in/host:/etc/letsencrypt -v /path/in/host:/etc/nginx/sites-enabled -e CERT1="example.com;subdomain.example.com" derdiedasjojo/nginx-reverse-proxy

## Arguments

### Volumes

* **/etc/nginx/sites-enabled** : [Mandatory] Should contains nginx configurations for redirections to websites/web apps.
* **/etc/letsencrypt** : [Optional] Should contains lets encrypt. Mount it should avoid regeneration of certifcates.

### Ports

* **80**: [Optional] HTTP port.
* **443**: [Mandatory] HTTPS port.

### Environment variables

* **CERT[X]**: [Mandatory] domain and subdomains, separated by `;` that will be generated by Let's Encrypt ([X] should be replaced by a number). Example: `-e CERT1="example.com;subdom.example.com;subdom2.example.com -e CERT2="example2.com;subdom.example2.com"`
* **LETSENCRYPT_EMAIL**: [Mandatory] E-mail that will be given to lets encrypt in order to generate certificates. Defaults to `none`.
* **RSA_KEY_SIZE**: [Optional] Size of RSA keys that will be generated or renewed. Defaults to `4096`.
* **STARTUP_WAIT**: [Optional] Seconds to wait before launching LetsEncrypt and then Nginx. Defaults to `0`.

### Links

[Mandatory] Add links to other container with `--link source-container-name:alias-container-name` as an argument.
Then, you'll have to add `http://alias-container-name` into your nginx website/webapp configuration to redirect flux.

## Lets Encrypt

![Logo](https://letsencrypt.org/images/letsencrypt-logo-horizontal.svg)

### /!\ Warnings /!\

There is some limits to certificates generation. At the time of writing, it's **20 certificates by week**.

To **avoid certificate regeneration** at each container creation, please consider **mounting volume `/etc/letsencrypt`** in the container.

For more informations: https://letsencrypt.org/docs/rate-limits/.

### Use of certificates

After the generation of the certificates, you can find them at `/etc/letsencrypt/live/<domain>`.
The certificate will be generated as `fullchain.pem` and the private key as `privkey.pem`.
Every subdomain is registered within the same certificate.

For `example.com`, you'll find the :
* Certificate at `/etc/letsencrypt/live/example.com/fullchain.pem`
* Private key at `/etc/letsencrypt/live/example.com/privkey.pem`

### Support

If you like Let's Encrypt, please consider making a donation at https://letsencrypt.org/donate/.

> We're making it possible for everyone to experience a secure and privacy-respecting Web.

> We make it easy to get certificates for HTTPS, because ease of use is critical for adoption.

> We provide certificates free of charge, because cost excludes people.

> Our certificates are available in every country in the world, because the secure Web is for everyone.

> We strive to be open and transparent, because these values are essential for trust.

> Your support makes this possible.

> [LetsEncrypt.org]