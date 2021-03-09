## About App
Local run
```bash
docker-compose build -no--cache
docker-compose up -d
docker-compose exec php-fpm sh -c "composer install"
docker-compose exec php-fpm sh -c "composer dump-autoload"
docker-compose exec php-fpm sh -c "npm install"
```
