# Миграция существующего сайта на WordPress в Docker + Traefik

Данный руководство описывает шаги по миграции существующего сайта на WordPress в Docker + Traefik. Пожалуйста, следуйте инструкциям ниже:

## Шаг 1: Экспорт дампа базы данных
Экспортируйте дамп базы данных с вашего текущего сайта и сохраните его в файле формата .sql.

## Шаг 2: Обновление файла .env
Обновите файл .env соответствующей информацией. Откройте файл и измените следующие переменные:
```sh
ALIAS=my-containers-alias-name
HOST=example.com
PHPMYADMIN_DOMAIN=pma.example.com
MYSQL_ROOT_PASSWORD=N0t%aStR0Ng!PasSwoRd
WORDPRESS_DB_NAME=my_db_name
WORDPRESS_DB_USER=my_db_user
WORDPRESS_DB_PASSWORD=1234
```

## Шаг 3: Копирование существующего содержимого WordPress
Скопируйте существующее содержимое WordPress и вставьте его в папку `www`.

## Шаг 4: Исправление прав доступа
Выполните скрипт `fix_perm.sh`, чтобы исправить права доступа для содержимого в папке `www`.

## Шаг 5: Запуск Docker-контейнера
Запустите Docker-контейнер с помощью `docker-compose`.

## Шаг 6: Импорт дампа базы данных
Откройте phpMyAdmin, перейдя по адресу `pma.example.com` в вашем веб-браузере. Импортируйте ранее экспортированный дамп базы данных из Шага 1.

## Шаг 7: Обновление учетных данных базы данных
Обновите учетные данные базы данных в файле `wp-config.php`, расположенном в папке `www`. Измените следующие строки в соответствии с информацией из файла `.env`:
```php
define( 'DB_NAME', "my_db_name" );
define( 'DB_USER', "my_db_user" );
define( 'DB_PASSWORD', "1234" );
define( 'DB_HOST', "db" );
```

## Шаг 8: (Опционально) Добавление WP_HOME и WP_SITEURL
Для удобного переключения между SSL и без SSL во время тестирования вы можете добавить следующие строки в файл `wp-config.php`:
```php
define('WP_HOME','https://example.com/');
define('WP_SITEURL','https://example.com/');
```
Если возникнут проблемы, измените `https` на `http` в указанных строках.

## Шаг 9: Конфигурация сайта для работы с обратными прок

си и SSL
Для правильной работы сайта с обратными прокси при включенном SSL, добавьте следующий код в файл `wp-config.php`:
```php
/**
 * Обработка обратного прокси SSL
 */
if ($_SERVER['HTTP_X_FORWARDED_PROTO'] == 'https')
    $_SERVER['HTTPS']='on';

if (isset($_SERVER['HTTP_X_FORWARDED_HOST'])) {
    $_SERVER['HTTP_HOST'] = $_SERVER['HTTP_X_FORWARDED_HOST'];
}
```

## Шаг 10: (Опционально) Добавление учетных данных Redis
Если вы используете Redis, добавьте следующие строки в файл `wp-config.php`, предполагая, что `redis` является именем контейнера, указанным в файле `docker-compose`:
```php
define('WP_REDIS_HOST', 'redis');
define('WP_REDIS_PORT', '6379');
```
Необходимо в самом Wordpress установить и активировать планиг Redis Object Cache.

Готово! Вы успешно перенесли ваш существующий сайт на WordPress в Docker + Traefik.