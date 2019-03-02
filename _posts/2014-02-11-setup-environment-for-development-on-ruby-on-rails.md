---
title: Setting Up the Environment for Ruby on Rails Development
layout: post
permalink: / en / setup-environment-for-development-on-ruby-on-rails /
categories: ['Ruby', 'Tutorials']
tags: ['ruby', 'ruby-on-rails']

---
Today I will show how to make a good working machine from freshly installed linux ready to help you develop applications quickly, easily and naturally.
<! - more ->
The article will consist of 5 parts.

  1. Installing Git
  2. Installing ZSH
  3. Install RVM
  4. Installing Ruby, Rails,
    4.1 Ruby
    4.2 Rails
  5. Code Editors
    5.1 Sublime Text 3
    5.2 RubyMine


## 1. Install Git

`` bash
$ sudo apt-get install git-core
`` `

Check the version:

`` bash
$ git --version
git version 1.8.3.2
`` `

Everything is fine, let's configure git.

`` bash
$ git config --global user.name "John Doe"
$ git config --global user.email johndoe@example.com
`` `

* * *

## 2. Install ZSH

`` bash
$ sudo apt-get install zsh
`` `

We proceed to the decoration.

`` bash
$ curl -L http://install.ohmyz.sh | sh
`` `

All decorations and settings zsh ready.
It remains only to re-log and start the console.

Jeeha! Now we have the coolest terminal in the world.
You can change the subject by editing the line `ZSH_THEME` in` ~ / .zshrc`

Topics are looking for here: https://github.com/robbyrussell/oh-my-zsh/wiki/themes

The project itself is here: https://github.com/robbyrussell/oh-my-zsh

* * *

## 3. Install RVM

Well, stomped on?

`` bash
➜ gpg --keyserver hkp: //keys.gnupg.net --recv-keys 409B6B1796C275462A1703113804BB82D39DC0E3


➜ \ curl -sSL https://get.rvm.io | bash -s stable
`` `

If you caught this error:

`` `
* WARNING: Above files contains `PATH =` with no `$ PATH` inside, this can break RVM,
    for details check https://github.com/wayneeseguin/rvm/issues/1351#issuecomment-10939525
    to avoid this warning append #PATH.
```

We treat it by correcting the `.zshrc` file. Namely, the lines containing export PATH = "..." are replaced by export PATH = $ PATH: "...".



That's all, rvm is ready. We can restart the console, or execute:

`` `
➜ source ~ / .zshrc
`` `

* * *

## 4. Install Ruby, Rails, Bundler

### 4.1 Ruby

Previously, we would start running `rvm requirements` to install dependencies and missing packages, but now we don’t need to do this, because rvm itself will understand what packages need to be delivered.

Therefore, feel free to install Ruby 2.0.0

`` `
➜ rvm install 2.1.1
`` `

The installation will be long, so you can see what is happening outside the window or go and prepare yourself a mug of invigorating coffee.

Let's tell the system that ruby ​​2.0.0 will be used by default.

`` `
➜ rvm use 2.1.1 --default
```
And I advise you to do another hook. There is no need to store documentation on our working machine, we can always find it on the Internet. Therefore, in the `.gemrc` file, let us cast the line“ gem: —no-rdoc —no-ri ”.

`` `
➜ echo "gem: --no-rdoc --no-ri" ~ / .gemrc
`` `

### 4.2 Rails

Installing just one command.

`` `
➜ gem install rails
`` `

* * *

## 5. Code Editors

I mainly use Sublime Text 3, but recently switched to RubyMine 6.
Let's install both editors.

Unfortunately, the products are paid and soon, you may have to think about purchasing a license to use these products.

### 5.1 Sublime Text 3

Installation is very simple. Go to the site http://www.sublimetext.com/3 Download the version for your platform. And run the file. We are waiting for the installation to finish and voila, everything is ready.

### 5.2 RubyMine

C RubyMine will have to tinker a bit, but in the end we get a full-featured code editor.

Go to http://www.jetbrains.com/ruby/ and download the latest version of the product.

Unzip the downloaded archive into your home directory, for example, in ~ / apps / RubyMine. Next, just run the rubymine.sh file from the RubyMine / bin / folder and follow the instructions.

After the installation is complete, you can run RubyMine from the application menu. But without Java, nothing will start, so let's install Java. Check version of java.

`` `
$ java -version
`` `

If we see the Open JDK, we immediately remove it from our system.

`` `
$ sudo apt-get remove openjdk *
`` `

Now install Oracle Java 7.

`` `
sudo add-apt-repository ppa: webupd8team / java
sudo apt-get update
sudo apt-get install oracle-java7-installer
`` `

After installation, check the version of Java and Java compiler.

```
➜ java -version
java version "1.7.0_51"
➜ javac -version 
javac 1.7.0_51
```
Great, the versions are the same. It remains only to start RubyMine and start developing applications.

* * *

## The End
Congratulations, we have the latest version of RubyMine in our hands and it is available in our list of applications.

That's all, we can say that we have well configured our machine for developing Ruby on Rails applications. This configuration is enough for me and, I think, this configuration will also work for you. But as they say, no limit to perfection. Try, dare, create!

Sources:
<http://git-scm.com/book/ru/Введение-Установка-Git>

<http://blog.coolaj86.com/articles/zsh-is-to-bash-as-vim-is-to-vi.html>

<https://github.com/zsh-users/zsh/blob/master/INSTALL>

<http://www.linuxrussia.com/2013/04/oracle-java-7-ubuntu-1304-1204-1210.html>
