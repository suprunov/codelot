### Удаление файлов по маске

```bash
find . -type f -name ‘*.txt~’ -exec rm -f {} \;
```

```bash
git config --global user.email "sergeysuprunov@gmail.com" 
git config --global user.name "Sergey Suprunov" 
```

```
sudo cp ~/config/mongodb.service /etc/systemd/system/mongodb.service
wget -qO - https://www.mongodb.org/static/pgp/server-4.2.asc | sudo apt-key add -
sudo apt-get install gnupg
wget -qO - https://www.mongodb.org/static/pgp/server-4.2.asc | sudo apt-key add -
echo "deb [ arch=amd64,arm64 ] https://repo.mongodb.org/apt/ubuntu xenial/mongodb-org/4.2 multiverse" | sudo tee /etc/apt/sources.list.d/mongodb-org-4.2.list
sudo apt-get update
sudo apt-get install -y mongodb-org
sudo systemctl start mongod
sudo systemctl enable mongod
```

```
sudo ufw allow OpenSSH
sudo ufw allow 'Nginx HTTPS'
sudo ufw allow 'Nginx HTTP'
sudo ufw enable
```
```
sudo apt-get update
sudo apt-get install default-jdk -y
export JAVA_HOME=/usr/lib/jvm/java-8-openjdk-amd64 
wget https://download.elastic.co/elasticsearch/release/org/elasticsearch/distribution/deb/elasticsearch/2.3.1/elasticsearch-2.3.1.deb
sudo dpkg -i elasticsearch-2.3.1.deb
sudo cp ~/config/elasticsearch.yml /etc/elasticsearch/elasticsearch.yml
sudo systemctl enable elasticsearch.service
```

```
gunzip -c /var/www/sm/db/dump.sql.gz | mysql -usm -hlocalhost -pQazqaz012
```

```
crontab -l > sm.cron
echo "30 23 * * * /var/www/sm/backup/daily" >> sm.cron
crontab sm.cron
rm sm.cron
```

```
sudo apt-get install default-jdk  -y
export JAVA_HOME=/usr/lib/jvm/java-8-openjdk-amd64 
wget https://download.elastic.co/elasticsearch/release/org/elasticsearch/distribution/deb/elasticsearch/2.3.1/elasticsearch-2.3.1.deb 
sudo dpkg -i elasticsearch-2.3.1.deb 
sudo systemctl enable elasticsearch.service 
sudo cp ~/config/elasticsearch.yml /etc/elasticsearch/elasticsearch.yml 
sudo systemctl restart elasticsearch
```

```
passwd
adduser user1
usermod -aG sudo user1
```

```
%sudo ALL=(ALL:ALL) NOPASSWD: ALL
```

```
su - user1
```

```
sudo usermod -aG www-data $USER
sudo chown -R www-data:www-data /var/www
sudo chmod -R 644 /var/www
sudo find /var/www -type d -exec chmod 755 {} \;
```
или
```
sudo usermod -aG www-data $USER
sudo chown -R www-data:www-data /var/www
sudo chmod -R 770 /var/www
```

```nginx
server {
    listen 443 ssl;  # Слушает HTTPS-трафик на порту 443 с использованием SSL.

    server_name example.com;  # Обрабатывает запросы для домена example.com.

    ssl_certificate /etc/nginx/ssl/your_certificate.crt;  # Указывает SSL-сертификат.
    ssl_certificate_key /etc/nginx/ssl/your_private_key.key;  # Указывает закрытый ключ SSL.

    root /var/www/example.com/public;  # Устанавливает корневой каталог для статических файлов сайта.

    index index.php;  # Устанавливает индексный файл по умолчанию на index.php.

    access_log /var/log/example.com.access.log combined;  # Определяет путь к журналу доступа с комбинированным форматом.

    error_log /var/log/example.com.error.log;  # Указывает путь к журналу ошибок.

    charset utf-8;  # Устанавливает кодировку UTF-8.

    client_max_body_size 128M;  # Устанавливает максимальный размер тела запроса клиента.

    location ^~ / {  # Определяет блок для обработки запросов к корневому каталогу (с приоритетом).

        try_files /maintenance.html @backend;  # Пытается найти файл maintenance.html, и если его нет, перенаправляет к @backend.
    }

    location @backend {  # Определяет блок для проксирования запросов к бэкенд-серверу.

        proxy_pass http://localhost:7001;  # Проксирует запросы на локальный сервер по порту 7001.

        proxy_set_header Host $host;  # Устанавливает заголовок Host.

        proxy_set_header X-Real-IP $remote_addr;  # Устанавливает заголовок X-Real-IP.

        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;  # Устанавливает заголовок X-Forwarded-For.

        proxy_set_header X-Forwarded-Proto $scheme;  # Устанавливает заголовок X-Forwarded-Proto.
    }

    error_page 503 @maintenance;  # Определяет страницу ошибки 503 и перенаправляет к @maintenance.

    location @maintenance {  # Определяет блок для обработки страницы обслуживания.

        rewrite ^(.*)$ /maintenance.html break;  # Перенаправляет запросы на maintenance.html.
    }

    location ^~ / {  # Второй блок для обработки запросов к корневому каталогу (с приоритетом).

        try_files $uri $uri/ /index.php?$args;  # Пытается найти файлы и перенаправляет к index.php в случае отсутствия.
    }

    error_page 404 /404.html;  # Определяет страницу ошибки 404 и перенаправляет к /404.html.

    location ~* \.(js|json|css|png|jpg|jpeg|gif|ico)$ {  # Определяет блок для обработки запросов к статическим ресурсам.

        expires max;  # Устанавливает заголовок Expires с максимальным значением.

        log_not_found off;  # Отключает логирование "not found".

        add_header Access-Control-Allow-Origin *;  # Добавляет заголовок Access-Control-Allow-Origin для CORS.
    }

    location ~ \.php$ {  # Определяет блок для обработки PHP-скриптов.

        fastcgi_pass unix:/var/run/php/php7.4-fpm.sock;  # Указывает путь к FastCGI-процессу PHP.

        # Параметры FastCGI
        fastcgi_param QUERY_STRING $query_string;  # Строка запроса.
        fastcgi_param REQUEST_METHOD $request_method;  # HTTP-метод (например, GET, POST).
        fastcgi_param CONTENT_TYPE $content_type;  # Тип контента.
        fastcgi_param CONTENT_LENGTH $content_length;  # Размер тела запроса.
        fastcgi_param SCRIPT_FILENAME $request_filename;  # Путь к выполняемому скрипту.
        fastcgi_param SCRIPT_NAME $fastcgi_script_name;  # Имя выполняемого скрипта.
        fastcgi_param REQUEST_URI $request_uri;  # URI запроса.
        fastcgi_param DOCUMENT_URI $document_uri;  # URI текущего документа.
        fastcgi_param DOCUMENT_ROOT $document_root;  # Корневой каталог документа.
        fastcgi_param SERVER_PROTOCOL $server_protocol;  # Протокол сервера (например, HTTP/1.1).
        fastcgi_param GATEWAY_INTERFACE CGI/1.1;  # Интерфейс шлюза.
        fastcgi_param SERVER_SOFTWARE nginx/$nginx_version;  # Имя и версия сервера.
        fastcgi_param REMOTE_ADDR $remote_addr;  # IP-адрес клиента.
        fastcgi_param REMOTE_PORT $remote_port;  # Порт клиента.
        fastcgi_param SERVER_ADDR $server_addr;  # IP-адрес сервера.
        fastcgi_param SERVER_PORT $server_port;  # Порт сервера.
        fastcgi_param SERVER_NAME $server_name;  # Имя сервера.
        fastcgi_param HTTPS $https if_not_empty;  # Передает информацию о SSL-соединении.
        fastcgi_param REDIRECT_STATUS 200;  # Статус перенаправления.
        fastcgi_param SCRIPT_FILENAME $document_root$fastcgi_script_name;  # Путь к выполняемому скрипту.

        try_files $uri =404;  # Пытается найти файл и, в случае отсутствия, перенаправляет к 404.
    }

    location ~ /\.(ht|svn|git) {  # Определяет блок для запрета доступа к скрытым файлам и каталогам.

        deny all;  # Запрещает доступ.
    }
}

server {
    if ($host = example.com) {  # Если хост равен example.com.

        return 301 https://$host$request_uri;  # Перенаправляет на HTTPS.
    }
    listen 80;  # Слушает запросы на порту 80.
    server_name example.com;  # Обрабатывает запросы для домена example.com.
    return 404;  # Возвращает код состояния HTTP 404.
}
```
