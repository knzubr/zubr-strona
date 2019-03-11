Vagrant - wprowadzenie
======================

Instalacja VM w kvm-qemu
------------------------

.. highlight:: bash

::

   # pobierz obraz ubuntu
   
   # instalacja:
   virt-install --virt-type kvm --name ubuntu --ram 1024 --cdrom=ubuntu.iso --disk ubuntu-disk.qcow2,format=qcow2 --network network=default --graphics vnc,listen=0.0.0.0 --noautoconsole --os-type=linux
    
   # połącz się przez vnc
   vncviewer
    
   # instalacja systemu, konfiguracja ...
   # zarządzanie maszynami wirtualnymi
   virsh ... 

Uruchamianie VM w Vagrancie
-------------------------

::

   vagrant init ubuntu/xenial64
   vagrant up

   vagrant ssh

   #shutdown
   vagrant halt

   #przeładowanie konfiguracji
   vagrant reload
   # lub jeśli mamy provisioning
   vagrant reload --provision

   #usunięcie vm
   vagrant destroy


Konfiguracja VM
---------------

Vagrant używa konfiguracji zapisanej w Vagrantfile.

::

   # obraz vm
   config.vm.box = "hashicorp/precise64"

   # statyczny adres w prywatnej sieci
   config.vm.network "private_network", ip: "<address>"

   # publiczny (bridge)
   config.vm.network "public_network", ip: "<address>"

   # shell provisioning
   config.vm.provision "shell", inline: <<-SHELL
       apt-get install -y python3
   SHELL
   # lub
   Vagrant.configure("2") do |config|
       config.vm.provision "shell", path: "script.sh"
   end   
   # ustawianie cpu i ram dla VirtualBoxa
   config.vm.provider "virtualbox" do |vb|
       vb.cpus = 2
       vb.memory = "4096"
   end

Konfiguracja z wieloma VM
-------------------------

::

   Vagrant.configure("2") do |config|
       config.vm.provision "shell", inline: "echo hi! > hello.txt"

       config.vm.define "web" do |web|
           web.vm.box = "apache"
       end

       config.vm.define "db" do |db|
           db.vm.box = "mysql"
       end
   end


Linki
-----

https://www.vagrantup.com/intro/getting-started/index.html
https://www.vagrantup.com/docs/index.html
https://virt-manager.org/
https://libvirt.org/index.html