###1. Устанавливаем percona mysql сервер

  1. gpg --keyserver hkp://keys.gnupg.net --recv-keys 1C4CBDCDCD2EFD2A
  2. gpg -a --export CD2EFD2A | sudo apt-key add -
  3. открываем файл /etc/apt/sources.list, предварительно поменяв в нем права на запись. Добавляем туда строки:
      deb http://repo.percona.com/apt precise main
      deb-src http://repo.percona.com/apt precise main
  4. apt-get update
  5. apt-get install percona-server-server-5.5 percona-server-client-5.5

###2. Устанавливаем nginx
  apt-get install nginx

###3. Устанавливаем php5-fpm
  apt-get install php5-fpm

###4. Устанавливаем phpmyadmin
  apt-get install phpmyadmin

###5. КОНФИГУРИРУЕМ САЙТЫ
  1. Создаем конфиг и даем ему ОБЯЗАТЕЛЬНО имя server_name, которое пропишем потом в конфиге 
    Например: /etc/nginx/sites-avaliable/phpmyadmin       (для phpmyadmin)
       /etc/nginx/sites-avaliable/kcl.com ( для kcl.com )
  2. Печатаем туда: 
    server {
      listen 80;//default line
      root /usr/share/phpmyadmin; //где лежат файлы проекта
      index index.php;
      server_name phpmyadmin; //or any other we like, name, for which the site will be available from the browser
      
      access_log /var/log/phpmyadmin.access_log;
      error_log /var/log/phpmyadmin.error_log;
      
      location / {
        try files $uri $uri/ /index.php;
      }
      
      location ~ \.php$ {
         fastcgi_pass 127.0.0.1:9000;
         fastcgi_index index.php;
         fastcgi_read_timeout 120; //если сайт очень большой и не отвечает (504 ошибку дает если величина маленькая)
         include fastcgi_params;    
      }
  }
  3. Создаем символическую ссылку на наш виртуальный сервер ( у нас он phpmyadmin):
     ln -s /etc/nginx/sites-available/phpmyadmin /etc/nginx/sites-enabled/phpmyadmin
  
  4. Прописываем наш хост в виртуальных хостах  /etc/hosts   
     127.0.0.1 phpmyadmin
     
  5. перезагружаем сервер
     sudo service nginx start / restart
      
     внимательно при этом читаем консоль, потому что могут быть ошибки и сервер не запустится
  
  Если мы все правильно сделали и не допустили нигде ошибок, сайт должен быть доступен по адресу http://phpmyadmin из браузера   
     
    БАЗЫ ДАННЫХ ЛЕЖАТ В var/lib/mysql          
    КОНФИГ MYSQL /var/lib/dpkg/info/percona-server-server-5.5.config. 
            
    Здесь прописан путь к mysql config => /etc/mysql/my.cnf. Если этого файла нет по умолчанию, его нужно создать. \
    Можно его создать путем копирования из examples 
            sudo cp /usr/share/mysql/my-huge.cnf /etc/mysql/my.cnf
           
    FILE LOG IN MYSQL /etc/logcheck/ignore.d.server/percona-server-server-5_5
    ВСЕ ЛОГИ:  /var/log/mysql.log
              /var/log/ и т.д.




                          