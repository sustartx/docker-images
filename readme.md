### Build

```shell
cd php-fpm-84 && docker build -t sustartx/php-fpm-84 .
cd php-fpm-84 && docker buildx build --platform linux/amd64,linux/arm64 -t sustartx/php-fpm-84 .
```
