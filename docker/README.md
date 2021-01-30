Запуск проекта
=====================
Запуск контейнеров для проекта

```
$ docker-compose up -d
```

По умолчанию nginx отвечает на домен **fast-system.local** 

Для корректной работы необходимо прописать домен в **/etc/hosts**
```
127.0.0.1 fast-system.local
127.0.0.1 api.fast-system.local
127.0.0.1 v2.fast-system.local
127.0.0.1 tm.fast-system.local
127.0.0.1 insurance.fast-system.local
127.0.0.1 mmc.fast-system.local
127.0.0.1 pma.fast-system.local
```

Установка composer пакетов
```
docker exec -it fast-system-php composer install -d /var/www/fast-system
```
Применение миграций
```
docker exec -it fast-system-php php /var/www/fast-system/yii migrate
```

Подключение к mysql
===================
**Host:** 127.0.0.1 (если с локального компа)  
**Host:** fast-system-mysql (если из php приложения в docker)  
**User:** fast-system  
**Pass:** fast-system  
**Db:** fast-system

Настройка проекта
=====================
### Настройка модулей debug и gii

Для корректной работы debug и gii модуля необходимо разрешить подключение к ним с ip контейнера nginx. Для этого в файле **backend/config/main-local.php** добавляем маску IP
```
$config['bootstrap'][] = 'debug';
$config['modules']['debug'] = [
    'class' => 'yii\debug\Module',
    'allowedIPs' => ['192.168.220.*']
];

$config['bootstrap'][] = 'gii';
$config['modules']['gii'] = [
    'class' => 'yii\gii\Module',
    'allowedIPs' => ['192.168.220.*']
];
```

### Настройка подключения к БД

Для настройки подключение к БД внести корректные параметры mysql в **common/config/main-local.php**

В качестве host для mysql указать fast-system-mysql
```
mysql:host=fast-system-mysql
```
Настройка среды разработки
==================

[Конфигурация php интерпретатора PHPStorm](https://blog.jetbrains.com/phpstorm/2017/07/new-docker-compose-support-in-phpstorm-2017-2/ "New Docker-Compose Support in PhpStorm 2017.2")

[Конфигурация php интерпретатора VSCode](https://marketplace.visualstudio.com/items?itemName=ms-vscode-remote.vscode-remote-extensionpack)

Пример VSCode:
* Запустить контейнеры
* ctrl+shift+p - Remote-Containers: Attach to Running Container

Разное
======
Как пересобрать php контейнер
```
$ docker-compose stop php
$ docker-compose build php
$ docker-compose up -d php
```

Тесты
=====
```
// Инициализация
docker exec -it fast-system-php sh -c "cd /var/www/fast-system && vendor/bin/codecept bootstrap"
// Запуск
docker exec -it fast-system-php sh -c "cd /var/www/fast-system && vendor/bin/codecept run"
```

Webpack
=======
- Установлен в контейнер `fast-system-php`.
- Для приложение `backend` находится в `backend/webpack`
- Файл конфига `webpack.mix.js` в директории `webpack`
- Доп информация по настройке webpack: http://laravel.su/docs/5.4/mix
```
// Слежение за кодом
docker exec -it fast-system-php sh -c "cd /var/www/fast-system/backend/webpack && npm run watch --poll"
```

Расчет V2
=========
```
// запуск очереди на V2 для расчетов
docker exec -it fast-system-php php /var/www/fast-system/yii queuev2/run
```