---
title: Установка LocomotiveCMS Wagon на Windows 8.1
permalink: /ru/install-locomotivecms-wagon-on-windows-8-1
categories: ['Ruby', 'Туториалы']
tags: ['ruby', 'locomotivecms', 'windows']

---

Сегодня я расскажу как легко и непринуженно установить LocomotiveCMS Wagon на Windows 8.1
<!--more-->

### 1. Установка RubyInstaller

Переходим сюда: http://rubyinstaller.org/downloads/, выбираем версию и устанавливаем. При установке не забудьте поставить все галочки которые попросит инсталлятор.

### 2. Устаноквка DevKit

По ссылке выше находим ссылки для скачивания DevKit. Не забудьте скачать версию kit именно для вашей версии и разрядности интерпретатора Ruby.

Распаковываем в любое место. Я распаковал сюда: `C:\DevKit` Далее, запустим консоль «Start Command Prompt with Ruby» из меню приложений Windows.  
Переходим в папку с DevKit

```bash
> cd c:\DevKit
```

И собственно, производим установку DevKit

```bash
> ruby dk.rb init
> ruby dk.rb review
> ruby dk.rb install
```

Если ошибок не произошло, то движемся дальше.

* * *

### 3. Установка нужных гемов

Если Ruby и DevKit установлены, то запускаем консоль.

#### Bundler

```bash
> gem install bundler
```

#### Rake

```bash
> gem install rake
```

* * *

### 4. Установка LocomotiveCMS Wagon

```bash
> gem install locomotivecms_wagon
```

Проверяем версию:

```bash
> wagon version
1.4.0
```

Отлично. Давайте создадим тестовый проект и запустим сервер.

```bash
> wagon init testapp
....
....
Do you prefer HAML templates ? n
....
....
```

Перейдем в приложение, установим недостающие гемы и запустим сервер:

```bash
> cd testapp
> bundle install
> bundle exec wagon serve
```

### Исправление ошибок

Если вы получили ошибку:

```
Can't get the absolute path for the passed directory: ''!
```

то запускайте сервер с указанием абсолютного пути к папке:

```bash
> bundle exec wagon serve с:/testapp
```

Либо отредактируйте файл wagon(C:\Ruby200\bin\wagon) добавив в самое начало файла строку: «ARGV << Dir.pwd»

Также вы можете получать ошибку перед запуском сервера:

```
C:/Ruby200/bin/wagon: No such file or directory - which convert
Thin web server (v1.5.1 codename Straight Ra
Maximum connections set to 1024
Listening on 0.0.0.0:3333, CTRL+C to stop
```

Чтобы избавиться от ошибки поиска ImageMagic заменим строчку в файле C:\Ruby200\lib\ruby\gems\2.0.0\gems\locomotivecms_wagon-1.4.0\lib\locomotive\wagon\misc\dragonfly.rb

```ruby
convert = `which convert`.strip.presence || '/usr/bin/env convert' 
```

на

```ruby
convert = `where convert`.strip.presence
```

 [1]: https://gist.github.com/istickz/9475935#development-kit
 [2]: https://gist.github.com/istickz/9475935#Установка-нужных-гемов
 [3]: https://gist.github.com/istickz/9475935#bundler
 [4]: https://gist.github.com/istickz/9475935#rake
 [5]: https://gist.github.com/istickz/9475935#Установка--locomotivecms-wagon
 [6]: https://gist.github.com/istickz/9475935#Исправление-ошибок
