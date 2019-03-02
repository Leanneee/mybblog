---
title: Depla Rails Application on DigitalOcean
permalink: / en / deploy-rails-app /
categories: ['Ruby', 'Tutorials']
tags: ['ruby', 'ruby-on-rails', deploy, 'vps']

---
Long nights you wrote the code of your super application on Ruby on Rails. Are you ready for the whole world to see it? It's time for you to exit the development mode and finally try out the production version of your application.

Today we will set up a freshly purchased VPS to the needs of our Rails applications and deploy a test application to it.
<! - more ->
The article will consist of 4 parts:

 1. Preliminary preparation
    1.1 Creating an SSH key
    1.2 Adding a key to a profile on DO
  2. Create a droplet
  3. Droplet setting
    3.1 Security
    3.2 Installing the Russian locale
    3.3 System Update
    3.4 Installing RVM and Ruby
    3.5 Installing MySQL
    3.6 Installing Node.js
    3.7 Installing NGINX
  4. Depla Rails Applications
    4.1 Creating a test application
    4.2 of our application

In the article, I will work with VPS purchased from [DigitalOcean] [1].

[DigitalOcean] [1] offers the most affordable prices for virtual servers with good ping, even from Russia. By the way, guys often give out promotional codes to get funds for payment for services.

## 1. Pre-training

Before buying a droplet, I recommend that you create an SSH key for a bunch of our computer and a droplet to be bought.
### 1.1 Создание SSH ключа

Go to the directory of SSH keys

`` bash
$ cd ~ / .ssh
`` `

Create a new key for DigitalOcean

`` bash
$ ssh-keygen -t rsa -C "your_email@example.com"
Generating public / private rsa key pair.
`` `

In the generation process, you need to specify the name of the key:

`` `
Enter the key (/home/username/.ssh/id_rsa):digital_ocean_rsa
`` `

Next, you need to enter passwords for keys, but we will safely skip them by pressing Enter.

`` `
Enter passphrase (empty for no passphrase):
Enter same passphrase again:
`` `

Key generation should end with this message:
```
Your identification has been saved in digital_ocean_rsa.
Your public key has been saved in digital_ocean_rsa.pub.
The key fingerprint is:
5e:72:rd:01:c4:36:84:23:ef:45:5b:ef:5z:e2:72:74 
your_email@example.com
The key's randomart image is:
+--[ RSA 2058]----+
|           pp.   |
|          . x.+  |
|           = =.p |
|        . R.-,+. |
|        S+d=.,.. |
|       ..++ .    |
|        .p       |
|                 |
|                 |
+-----------------+
```

### 1.2 Adding a key to a DO profile

Copy the generated key:

`` bash
$ xclip -sel clip <~ / .ssh / digital_ocean_rsa.pub
`` `

All key in the clipboard. You can safely go to the [SSH Keys] [3] section of the DO control panel and add our key. At this pre-training can be considered complete.

* * *

## 2. Create a droplet

Go to Droplets -> [Create Droplet] [4]

Select options for droplet:

  * Droplet name;
  * tariff option ($ 5 will be fine for us);
  * the region where the droplet will be located;
  * distribution kit linux;
  * Select the imported SSH key.
 

After selecting the necessary items, feel free to press the Create Droplet button and after a while we will be thrown into this nice droplet control panel.
After creating a droplet, root access to the machine will be possible only with the ssh key, since we connected it when creating a droplet. But soon we will disable root access for security reasons.

* * *

## 3. Droplet setting

So, we have a droplet, it's time to connect with it.
The IP of your server can be viewed at the very top of the droplet control panel.

`` bash
ssh root@xx.xx.xx.xx
`` `

If everything is good, then we see something like this:

`` bash
root @ Jarvis: ~ #
`` `

Great, now you need to take care of droplet safety.

### 3.1 Security

#### Add user
```bash
$ adduser username
```

We answer questions, confirm the information and the user is ready.

Now we give him normal rights.

`` bash
$ visudo
`` `

Find the following lines:

`` `
# User privilege specification
root ALL = (ALL: ALL) ALL
`` `

And right under the root add our user with the same rights

`` `
username ALL = (ALL: ALL) ALL
`` `

Save and exit the editor.


#### SSH port change

`` bash
$ nano / etc / ssh / sshd_config
`` `

Find the string:

```
Port 22
```

And “22” will be changed for some other port at will (1025..65536):

`` `
Port 6629
`` `

#### Disable root login by user

In the same file we find the line PermitRootLogin and set the value значение no ’to it:

`` `
PermitRootLogin no
`` `

And at the end of the file add lines

`` `
UseDNS no
AllowUsers username
`` `

Save and exit the editor.

Reboot SSH
```bash
$ reload ssh
```

Теперь, создадим новую вкладку в терминале и попробуем соединиться

```bash
$ ssh -p 6629 username@123.123.123.123
```

Завершаем ssh соединение, чтобы скопировать ssh ключ с локального компьютера.

```bash
$ cat ~/.ssh/igital_ocean_rsa.pub | ssh -p 6629 username@xxx.xxx.xxx.xxx "mkdir -p ~/.ssh && cat >>  ~/.ssh/authorized_keys"
```

Если все прошло успешно, то пробуем заново залогиниться на сервере:

```bash
$ ssh -p 6629 username@95.85.27.37 
```

Если система не спрашивает у нас пароль то проедыдущий шаг мы сделали правильно.

На этом настройки безопасности не завершены, но к ним мы вернемся в конце этой статьи.

## 3.2 Установка русской локали 

Если вам неспокойно от того что время от времени могут появляться сообщения такого рода:

```
locale: Cannot set LC_CTYPE to default locale: No such file or directory
locale: Cannot set LC_MESSAGES to default locale: No such file or directory
locale: Cannot set LC_ALL to default locale: No such file or directory
```

то рекомендую поставить русскую локаль.

```bash
$ localedef ru_RU.UTF-8 -i ru_RU -fUTF-8
```

после этого вы на всегда забудете об ожибках локалейи и вам будет доступен ввод уириллицы из консоли.

## 3.3 Обновление системы 

Обновление системы рекомендуется делать сразу же после первой авторизации на сервере, но и сейчас не поздно сделать это.

```bash
$ sudo apt-get update
$ sudo apt-get upgrade
```

### 3.4 Установка RVM и Ruby 

#### RVM 

```bash
$ \curl -sSL https://get.rvm.io | bash -s stable
$ source /home/username/.rvm/scripts/rvm
```

#### Установка всех зависимостей, которые могут пригодиться:

```
$ rvm requirements
```

во время выполнения этой команды установятся следующие пакеты: `gawk, g++, gcc, make, libc6-dev, libreadline6-dev, zlib1g-dev, libssl-dev, libyaml-dev, libsqlite3-dev, sqlite3, autoconf, libgdbm-dev, libncurses5-dev, automake, libtool, bison, pkg-config, libffi-dev`

#### Ruby 2.0.0: 

```
rvm install 2.0.0 && rvm use 2.0.0 --default
```

#### Установка последней версии RubyGems

```
$ rvm rubygems current
```

### 3.5 Установка MySQL 

```
$ sudo apt-get install mysql-server mysql-client libmysqlclient-dev
```

### 3.6 Установка Node.js 

Установку Node.js будем проводить с помощью NVM

```bash
$ curl -o- https://raw.githubusercontent.com/creationix/nvm/v0.31.0/install.sh | bash
$ nvm install node
$ node --version
v6.0.0
```

```bash
$ n=$(which node);n=${n%/bin/node}; chmod -R 755 $n/bin/*; sudo cp -r $n/{bin,lib,share} /usr/local

```

### 3.7 Установка NGINX 

```
$ sudo apt-get install nginx
$ sudo service nginx start
```

Все, nginx запущен, проверить работу nginx можно набрав IP адрес нащего дроплета в браузере.

Настройка сервера закончена пора приступать к разворачиванию приложения.

* * *

## 4. Деплой Rails приложения 

### 4.1 Создание тестового приложения 

Отсоединимся от сервера и в директории с нашими разработками создадим тестовое приложение

```
$ rails new testapp -d mysql
```

```
$ git init
$ git add .
$ git commit -m "initial commit"
$ git remote add origin https://github.com/istickz/testapp.git
```

#### Изменим конфигурацию доступа к MySQL и удалим секцию production. 

testapp/config/database.yml

```yaml
development:
  adapter: mysql2
  encoding: utf8
  database: testapp_development
  pool: 5
  username: root
  password: 121212
  socket: /var/run/mysqld/mysqld.sock

test:
  adapter: mysql2
  encoding: utf8
  database: testapp_test
  pool: 5
  username: root
  password: 121212
  socket: /var/run/mysqld/mysqld.sock
```

Секцию production я предлагаю вынести в отдельный файл:

config/production_database.yml

```yaml
production:
  adapter: mysql2
  encoding: utf8
  database: testapp
  pool: 5
  username: someuser
  password: somepassword
  socket: /var/run/mysqld/mysqld.sock
```   

и добавить в файл .gitignore

```
$ echo "config/production_database.yml"  .gitignore
$ git add .gitignore 
$ git commit -m "ignore database configuration for production" 
```

После чего, занесем изменения в коммит.

```
$ git add /config/database.yml
$ git commit -m "configure only for test and development"
```

#### Добавим немного фунциональности нашему приложению. 

Пусть наше приложение будет блогом:

```
$ rails g scaffold Post title:string description:text
```

Далее добавим страницу открываемую по умолчанию:

testapp/config/routes.rb

```ruby
Testapp::Application.routes.draw do
  resources :posts

  root 'posts#index'
end
```

Создадим базу данных и накатим миграции:

```
$ rake db:create
$ rake db:migrate
```

Пока изменений не накопилось достаточно много, нужно их закоммитить.

```
$ git add config/routes.rb 
$ git commit -m "now posts is a root page"
$ git add .
$ git commit -m "add posts"
$ git push origin master
```

### 4.2 Деплой нашего приложения 

#### Capistrano

Разворачиванием нашего приложения будет заниматься [Capistrano][6]

В Gemfile добавим следующие строки:

```ruby
group :development do
  gem 'rvm-capistrano'
  gem 'capistrano'
end

group :production do
  gem 'unicorn'
end
```

Сapistrano будет отвечать за выполнение команд, а unicorn у нас будет в качестве сервера приложения.

Делаем

```
$ bundle install
$ git commit -am "add unicorn and capistrano gems"
```

И создаем файлы конфигурации для Capistrano:

```bash
$ capify .
[add] writing './Capfile'
[add] writing './config/deploy.rb'
[done] capified!
$ echo "/config/deploy.rb"  .gitignore
$ git commit -am "ignore deploy configuration"
```

Теперь нужно создать еще несколько конфигурационных файлов:

```
$ touch config/nginx.conf
$ touch config/unicorn.rb
$ touch config/unicorn_init.sh
```

* * *

#### Редактирование конфигурационных файлов 

/Capfile

```ruby    
load 'deploy'
load 'deploy/assets'
load 'config/deploy'
```
    

/config/deploy.rb

```ruby
require "rvm/capistrano"
require "bundler/capistrano"

set :application, "testapp"
set :shared_children, shared_children
set :repository,  "git@github.com:istickz/testapp.git"
set :deploy_to, "/var/www/testapp"
set :scm, :git
set :branch, "master"
set :user, "username"
set :group, "username"
set :use_sudo, false
set :rails_env, "production"
set :deploy_via, :copy
set :ssh_options, { :forward_agent => true, :port => 6629 }
set :keep_releases, 5
default_run_options[:pty] = true
server "XX.XX.XX.XX", :app, :web, :db, :primary => true

after "deploy", "deploy:cleanup"

namespace :deploy do
  %w[start stop restart].each do |command|
    desc "#{command} unicorn server"
    task command, roles: :app, except: {no_release: true} do
      run "/etc/init.d/unicorn_#{application} #{command}"
    end
  end

  task :setup_config, roles: :app do
    sudo "ln -nfs #{current_path}/config/nginx.conf /etc/nginx/sites-enabled/#{application}"
    sudo "ln -nfs #{current_path}/config/unicorn_init.sh /etc/init.d/unicorn_#{application}"
    run "mkdir -p #{shared_path}/config"
    put File.read("config/production_database.yml"), "#{shared_path}/config/database.yml"
    puts "Теперь вы можете отредактировать файлы в  #{shared_path}."
  end
  after "deploy:setup", "deploy:setup_config"

  task :symlink_config, roles: :app do
    run "ln -nfs #{shared_path}/config/database.yml #{release_path}/config/database.yml"
  end
  after "deploy:finalize_update", "deploy:symlink_config"

  desc "Make sure local git is in sync with remote."
  task :check_revision, roles: :web do
    unless `git rev-parse HEAD` == `git rev-parse origin/master`
      puts "WARNING: HEAD is not the same as origin/master"
      puts "Run `git push` to sync changes."
      exit
    end
  end
  before "deploy", "deploy:check_revision"

end
    
```

config/nginx.conf

```    
    upstream unicorn {
      server unix:/tmp/unicorn.testapp.sock fail_timeout=0;
    }
    
    server {
      listen 80 default deferred;
      server_name testapp.dev.istickz.ru;
      root /var/www/testapp/current/public;
    
      location ^~ /assets/ {
        gzip_static on;
        expires max;
        add_header Cache-Control public;
      }
    
    
      try_files $uri/index.html $uri @unicorn;
      location @unicorn {
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
        proxy_set_header Host $http_host;
        proxy_redirect off;
        proxy_pass http://unicorn;
      }
    
      error_page 500 502 503 504 /500.html;
      client_max_body_size 4G;
      keepalive_timeout 10;
    }
```    
    

config/unicorn.rb

```ruby
    root = "/var/www/testapp/current"
    working_directory root
    pid "#{root}/tmp/pids/unicorn.pid"
    stderr_path "#{root}/log/unicorn.log"
    stdout_path "#{root}/log/unicorn.log"
    
    listen "/tmp/unicorn.testapp.sock"
    worker_processes 2
    timeout 30
    
    # Force the bundler gemfile environment variable to
    # reference the capistrano "current" symlink
    before_exec do |_|
      ENV["BUNDLE_GEMFILE"] = File.join(root, 'Gemfile')
    end
```
    

config/unicorn_init.sh

```bash
    #!/bin/sh
    ### BEGIN INIT INFO
    # Provides:          unicorn
    # Required-Start:    $remote_fs $syslog
    # Required-Stop:     $remote_fs $syslog
    # Default-Start:     2 3 4 5
    # Default-Stop:      0 1 6
    # Short-Description: Manage unicorn server
    # Description:       Start, stop, restart unicorn server for a specific application.
    ### END INIT INFO
    set -e
    
    # Feel free to change any of the following variables for your app:
    TIMEOUT=${TIMEOUT-60}
    APP_ROOT=/var/www/testapp/current
    PID=$APP_ROOT/tmp/pids/unicorn.pid
    CMD="cd $APP_ROOT; bundle exec unicorn -D -c $APP_ROOT/config/unicorn.rb -E production"
    AS_USER=username
    set -u
    
    OLD_PIN="$PID.oldbin"
    
    sig () {
      test -s "$PID" && kill -$1 `cat $PID`
    }
    
    oldsig () {
      test -s $OLD_PIN && kill -$1 `cat $OLD_PIN`
    }
    
    run () {
      if [ "$(id -un)" = "$AS_USER" ]; then
        eval $1
      else
        su -c "$1" - $AS_USER
      fi
    }
    
    case "$1" in
    start)
      sig 0 && echo >&2 "Already running" && exit 0
      run "$CMD"
      ;;
    stop)
      sig QUIT && exit 0
      echo >&2 "Not running"
      ;;
    force-stop)
      sig TERM && exit 0
      echo >&2 "Not running"
      ;;
    restart|reload)
      sig HUP && echo reloaded OK && exit 0
      echo >&2 "Couldn't reload, starting '$CMD' instead"
      run "$CMD"
      ;;
    upgrade)
      if sig USR2 && sleep 2 && sig 0 && oldsig QUIT
      then
        n=$TIMEOUT
        while test -s $OLD_PIN && test $n -ge 0
        do
          printf '.' && sleep 1 && n=$(( $n - 1 ))
        done
        echo
    
        if test $n -lt 0 && test -s $OLD_PIN
        then
          echo >&2 "$OLD_PIN still exists after $TIMEOUT seconds"
          exit 1
        fi
        exit 0
      fi
      echo >&2 "Couldn't upgrade, starting '$CMD' instead"
      run "$CMD"
      ;;
    reopen-logs)
      sig USR1
      ;;
    *)
      echo >&2 "Usage: $0 <start|stop|restart|upgrade|force-stop|reopen-logs>"
      exit 1
      ;;
    esac
```

Не забываем сделать файл исполняемым.

```
$ chmod +x config/unicorn_init.sh 
```

Запушим конфигурационные файлы в репозиторий:

```
$ git add .
$ git commit "Add configuration files"
$ git push origin master
```

* * *

Конфигурационные файлы готовы, пора приступать к заливке приложения, но прежде давайте заново авторизуемся на сервере и выставим права на папку /var/www/ для пользователя username:

```
$ sudo chown -R username:username /var/www
$ sudo chmod -R g+w /var/www
```

Теперь можно спокойно сказать Capistrano, чтобы подготовило необходимую структуру папок на сервере и закинуло конфигурационные файлы куда нужно.

```
$ cap deploy:setup
```

Редактировать мы будем только доступы к базе данных. Сначала создадим базу данных и пользователя для нее.

```
$ mysql -u root -p
mysql> CREATE DATABASE `testapp` CHARACTER SET utf8 COLLATE utf8_general_ci;
mysql> GRANT ALL PRIVILEGES ON testapp.* TO testapp@localhost IDENTIFIED BY 'password' WITH GRANT OPTION;
```

Теперь редактируем доступы к БД.

```
$ cd /var/www/testapp/shared/config
$ nano database.yml 
```

And enter the user and password.

Well, now we will flood our application on the server and compile the assets:

`` `
$ cap deploy: cold
`` `

Done! Now you can watch your application from the browser at http://yoursuperrailsapp.com

It remains to run the unicorn server every time the droplet is restarted.

`` `
$ sudo update-rc.d -f unicorn_testapp defaults
`` `

Now, if you change the application code, all you have to do is push the changes to the git repository and do:

`` `
$ cap deploy
```

после чего все изменения уже будут на вашем сервере. На этом все. Исходный код приложения можно взять тут: [Github][7], а рабочий пример тут: http://testapp.dev.istickz.ru/

 [1]: https://www.digitalocean.com/
 [2]: https://pbs.twimg.com/media/BdJoINTCcAA6ha0.jpg ""
 [3]: https://cloud.digitalocean.com/ssh_keys
 [4]: https://cloud.digitalocean.com/droplets/new
 [5]: http://i2.wp.com/istickz.ru/wp-content/uploads/2014/02/do-panel.png
 [6]: https://github.com/capistrano/capistrano
 [7]: https://github.com/istickz/testapp
