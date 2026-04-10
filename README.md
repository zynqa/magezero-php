# MageZero PHP Base Images

MageZero's official base Docker images for PHP-FPM with all Adobe Commerce required extensions pre-installed. These images provide a consistent, production-ready PHP runtime for all Magento 2 environments provisioned by the MageZero platform.

## Available Images

- `ghcr.io/zynqa/magezero-php:7.4-fpm` — legacy support for Magento 2.3.x / early 2.4.x
- `ghcr.io/zynqa/magezero-php:8.1-fpm`
- `ghcr.io/zynqa/magezero-php:8.2-fpm`
- `ghcr.io/zynqa/magezero-php:8.3-fpm`

All images are built on the official `php:{version}-fpm-alpine` base and include Composer 2.x installed globally at `/usr/bin/composer`.

## Included PHP Extensions

The following extensions, required by Adobe Commerce, are installed and enabled in every image:

- bcmath
- ctype
- curl
- dom
- gd
- hash
- iconv
- intl
- mbstring
- openssl
- pdo_mysql
- simplexml
- soap
- xsl
- zip
- sockets
- sodium
- xml
- xmlwriter
- zlib

Some of these (hash, iconv, openssl, pdo_mysql, xml, xmlwriter, zlib, dom, ctype, mbstring, simplexml, curl, tokenizer) are bundled with PHP by default in the official base images and are not explicitly re-installed.

## PHP 7.4 EOL Notice

> **Warning:** PHP 7.4 reached end-of-life on 28 November 2022 and no longer receives security fixes from the PHP project. The `7.4-fpm` image is included **solely** for legacy support of Magento 2.3.x and early Magento 2.4.x client migrations. Do not use PHP 7.4 for new projects. Plan migration to PHP 8.2 or 8.3 as soon as possible.

## How to Extend

To build a project-specific image on top of a MageZero base, create a `Dockerfile` like this:

```dockerfile
FROM ghcr.io/zynqa/magezero-php:8.3-fpm

# Add custom PHP configuration
COPY ./php.ini /usr/local/etc/php/conf.d/custom.ini
COPY ./www.conf /usr/local/etc/php-fpm.d/www.conf

# Copy application code
COPY . /var/www/html

# Install Composer dependencies
RUN composer install --no-dev --optimize-autoloader

# Fix permissions
RUN chown -R www-data:www-data /var/www/html
```

## Building Locally

To build any of the images locally (requires Docker):

```bash
docker build -f Dockerfile.8.3 -t magezero-php:8.3-fpm .
```

Replace `8.3` with `7.4`, `8.1`, or `8.2` to build a different version.

## Releasing

Images are built and pushed automatically by the GitHub Actions workflow in `.github/workflows/build.yml` when a version tag is pushed:

```bash
git tag v1.0.0
git push origin v1.0.0
```

This triggers the workflow which builds all four PHP versions in parallel and pushes them to `ghcr.io/zynqa/magezero-php` with both the floating `:{version}-fpm` tag and an immutable `:{version}-fpm-v1.0.0` tag.

A manual build can also be triggered via the `workflow_dispatch` event from the GitHub Actions UI.
