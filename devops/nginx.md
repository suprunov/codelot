Конфиг nginx

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
    
    error_page 404 /404.html;  # Определяет страницу ошибки 404 и перенаправляет к /404.html.
    error_page 503 @maintenance;  # Определяет страницу ошибки 503 и перенаправляет к @maintenance.
    
    location @maintenance {  # Определяет блок для обработки страницы обслуживания.
        rewrite ^(.*)$ /maintenance.html break;  # Перенаправляет запросы на maintenance.html.
    }
    
    location ^~ / {  # Второй блок для обработки запросов к корневому каталогу (с приоритетом).
        try_files $uri $uri/ /index.php?$args;  # Пытается найти файлы и перенаправляет к index.php в случае отсутствия.
    }
        
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

Этот обновленный комментированный конфигурационный файл включает все параметры `fastcgi_param` и объясняет, на что они влияют и какие значения они могут принимать. Если у вас есть дополнительные предложения для улучшения конфигурации, пожалуйста, уточните, и я помогу внести соответствующие изменения.