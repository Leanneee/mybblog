---
title: Установка LocomotiveCMS Wagon на Windows 8.1
permalink: /ru/install-locomotivecms-wagon-on-windows-8-1
categories: ['Ruby', 'Туториалы']
tags: ['ruby', 'locomotivecms', 'windows']

---
### Step 1st

#### Installing Virtualbox

Virtualbox is useful for us to run a virtual machine with a Ruby on Rails environment for development.

Installation:
Go to the address:
https://www.virtualbox.org/wiki/Downloads
Choose
"VirtualBox 5.0 for Windows hosts x86 / amd64"
Download, install.

* * *

### Step 2

#### Installing Vagrant

Vagrant is a very cool thing that seriously simplifies working with virtual machines. You can read more about it on the off site: [www.vagrantup.com/ [AO]

### 3. Установка нужных гемов

Если Ruby и DevKit установлены, то запускаем консоль.

#### Bundler

```bash
> gem install bundler
```

Now we look at the availability of sample.txt in the project folder in Windows. If the file is present, then we have done the 5th step correctly! We have the last step, having made that, we can already see the rails application in our browser.

* * *

### Step 6th

#### Installing Rails and Creating the First Application

Being in PuTTY install rails:

`` bash
$ gem install rails
`` `

Next, go to the projects folder, create a new application and start the server:

### Error correction

If you get an error:

`` `
Can't get the directory path: ''!
`` `

then start the server with the absolute path to the folder:

`` bash
> bundle exec wagon serve with: / testapp
`` `

Or, edit the wagon file (C: \ Ruby200 \ bin \ wagon) by adding the line to the very beginning of the file: "ARGV << Dir.pwd"

You may also receive an error before starting the server:

```
C:/Ruby200/bin/wagon: No such file or directory - which convert
Thin web server (v1.5.1 codename Straight Ra
Maximum connections set to 1024
Listening on 0.0.0.0:3333, CTRL+C to stop
```

To eliminate the ImageMagic search error, replace the line in the file C: \ Ruby200 \ lib \ ruby ​​\ gems \ 2.0.0 \ gems \ locomotivecms_wagon-1.4.0 \ lib \ locomotive \ wagon \ misc \ dragonfly.rb

`` ruby
convert = `which convert`.strip.presence || '/ usr / bin / env convert'
`` `

on

`` ruby
convert = `where convert`.strip.presence
`` `

 [1]: https://gist.github.com/istickz/9475935#development-kit
 [2]: https://gist.github.com/istickz/9475935#Install- of the necessary-hemes
 [3]: https://gist.github.com/istickz/9475935#bundler
 [4]: https://gist.github.com/istickz/9475935#rake
 [5]: https://gist.github.com/istickz/9475935#Installation--locomotivecms-wagon
 [6]: https://gist.github.com/istickz/9475935#Error bug
