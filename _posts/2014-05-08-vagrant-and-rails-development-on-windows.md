---
title: Rails on Vagrant or 6 simple steps to start developing on Rails in Windows
permalink: / en / vagrant-and-rails-development-on-windows /
categories: ['Ruby', 'Tutorials']
tags: ['ruby', 'vagrant']

---
Contrary to all the arguments that the development of Rails applications in Windows is not very comfortable, I decided to write this post, which will help beginners to quickly come to the world of Rails without hurting their psyche by learning linux, setting up the environment for it and a number of other tasks that take a lot of time and effort .
<! - more ->

  
So, if you have a Windows machine and a wild desire to try Rails right now, then I will show only 6 steps, having completed which you will see your first application at <http: // localhost: 3000 />.

* * *

#### Rake

`` bash
> gem install rake
`` `

* * *

### 4. Installing LocomotiveCMS Wagon

`` bash
> gem install locomotivecms_wagon
`` `

Check the version:

`` bash
> wagon version
1.4.0
`` `

Fine. Let's create a test project and start the server.

`` bash
> wagon init testapp
....
....
Do you prefer HAML templates? n
....
....
`` `

Go to the application, install the missing gems and start the server:

`` bash
> cd testapp
> bundle install
> bundle exec wagon serve
`` `
The vagrant features that we will use today are the creation and configuration of a virtual machine, automatic forwarding of ports and the creation of shared folders.

Installation:
Go to the address:
http://www.vagrantup.com/downloads
Select: "WINDOWS Universal (32 and 64-bit)"
Download, install.

* * *

### Step 3rd

#### Downloading customized Rails vagrant-box’s

In the Vagrant community, new builds of virtual machine configurations are constantly coming. Yes, you yourself can make your assembly. I suggest using this build: <https://vagrantcloud.com/leopard/rwprecise64>

Next, create a rails-box folder and initialize our virtual machine.
To do this, open a command prompt and, while in the rails-box folder, execute the following commands:

Virtual machine initialization:

`` bash
> vagrant init leopard / rwprecise64
`` `

After launching this command, the Vagrantfile configuration file will be created in the root of our folder. It is useful to us in the future.

Running a virtual machine:

`` bash
 > vagrant up
`` `

This command checks for the presence of installed virtual machines in Virtualbox and, in their absence, downloads, installs and configures a new virtual machine according to Vagrantfile. After that, the virtual machine starts and runs until you restart your computer.

* * *

### Step 4th

#### ssh machine access
For access and work with a virtual machine, I recommend the client [PuTTY] [2]
This program does not need special configuration, you just need to specify the IP address (127.0.0.1), port (2222) and click Open.
Before you open the console. Login and password that will need to enter: vagrant, vagrant.
At your discretion, you can set up authorization by ssh key, then the password and logins will not have to be entered.

* * *

### Step 5th

#### Shared Folders.

The last but one step that we have left to do is to create a folder in which we will store projects and synchronize it with the virtual machine, in other words - just make a shared folder.

Open the Vagrantfile configuration file and add the line there:

```ruby
config.vm.synced_folder "../projects/", "/home/vagrant/projects"
```
`config.vm.synced_folder \` - takes 2 parameters: the local folder and the folder on the virtual machine to be linked. The path to the local folder is relative, that is, the projects folder is one level higher than the rails-box. After saving the file, restart the virtual machine:

`` bash
> vagrant reload
`` `

Restart PuTTY and check if all this works:

`` bash
$ ls
postinstall.sh projects
`` `

Fine! Go to the projects folder and create an empty file there:

```bash
$ cd projects/
$ touch sample.txt
$ ls
sample.txt
```
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

```bash
$ cd projects
$ rails new sampleapp
$ cd sampleapp
$ rails s
```
After that, in the address bar of your favorite browser, type http: // localhost: 3000 / and see the welcome page of an empty Rails application.

If you see such a page, then I congratulate you - you made 5 difficult steps to get closer to developing on a wonderful Ruby on Rails framework. Now you just have to open the project folder in your favorite editor and start developing your application.


 [1]: http://www.vagrantup.com/
 [2]: http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html
 [3]: http://www.askvg.com/enable-open-command-window-here-option-in-context-menu-in-windows-vista/
