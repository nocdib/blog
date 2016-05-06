+++
Categories = ["Tech"]
Description = ""
Tags = ["Development"]
date = "2016-04-25T06:13:52-04:00"
menu = "main"
title = "If You Use Vagrant Then You Should Give Otto a Try"

+++

I was first introduced to <a href="https://www.vagrantup.com/">Vagrant</a> while working on a backend development project last year. If you aren't familiar with Vagrant it's basically a configuration wrapper around your preferred virtual machine software that permits you to customize the setup of a VM using a text file called Vagrantfile along with any desired  provisioning files (Chef, Salt, Ansible). Since the configuration is managed with text files it's easy to make changes without manually removing and installing software on the VM and the settings are easily shareable among different users using version control. The alternatives would be to either manually configure a VM and distribute the large image file or to develop the application on individual workstations. With the latter you are bound to encounter the issue of inconsistent behavior due to variances in machine setups among the team. Vagrant is  mature and has been widely adopted by the tech industry, so much so that it spawned <a href="http://www.hashicorp.com">a company</a> that focuses on open source and commercial dev ops software offerings. I'm not sure whether it was deliberate or consequential but each of the products after Vagrant were interoperable and formed a suite of distributed systems management solutions. In a clever and resourceful move, Hashicorp combined the VM management of Vagrant with some of their other offerings into a single solution that not only allows you to develop your standalone applications but also to deploy them to the cloud without ever needing to leave your command-line. This all-in-one solution is called <a href="https://www.hashicorp.com/blog/otto.html">Otto.</a>

Otto's level of automation is steps beyond what even Vagrant does to the point where it can detect the file types of an existing project and automatically configure a development environment for it. For example, run the "otto compile" command in a folder with Golang .go source files and you get an environment that already has a Gopath configured to the directory  the command was run from. For building workstation environments Otto leverages Vagrant under the hood. If you only need it for local development then you can use it for just that. Want to deploy your project to Amazon Web Services? Otto will automatically download <a href="http://www.packer.io/">Packer</a> to build your desired image and then use <a href="http://www.terraform.io">Terraform</a> to deploy it without you having to touch the AWS console.

Otto is in the early zero-point version stages so it's probably a good idea to give it some more time before implementing it in a mission-critical production setting. If you are on a small team that can afford to audit new technology then Otto is well worth it. For my personal project use it's more than sufficient so I've decided to stick with it instead of standalone Vagrant because it's bound to be the full successor by the time it's production certified.

<i><b>**NOTE:**</b> This is an honest and unbiased assessment. I am in no way affiliated with Hashicorp or its employees. I did meet <a href="http://twitter.com/mitchellh">Mitchell Hashimoto</a> once, though.</i>
