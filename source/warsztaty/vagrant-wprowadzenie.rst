Vagrant - wprowadzenie
======================

Przykładowa instalacja VM w kvm-qemu
------------------------------------

.. highlight:: bash

::

   # pobierz obraz ubuntu
   
   # instalacja:
   virt-install --virt-type kvm --name ubuntu --ram 1024 --cdrom=ubuntu.iso --disk ubuntu-disk.qcow2,format=qcow2 --network network=default --graphics vnc,listen=0.0.0.0 --noautoconsole --os-type=linux
   
   # można również użyć GUI: virt-manager

   # połącz się przez vnc
   vncviewer
   # instalacja systemu, konfiguracja ...
   
   # zarządzanie maszynami wirtualnymi
   virsh ...

Aby stworzyć obraz szybciej, możemy użyć programu *virt-builder*.
Więcej informacji:

::

    man virt-builder

Uruchamianie VM w Vagrancie
---------------------------

Aby uruchomić VM z domyślnym providerem(VirtualBox), musimy zainstalować wcześniej VirtualBox na komputerze.
Informacje o innych providerach jak KVM, VMware, Docker czy HyperV możesz znaleźć w `dokumentacji <https://www.vagrantup.com/docs/index.html>`_.

::

   vagrant init ubuntu/xenial64
   vagrant up

   vagrant ssh

   # shutdown
   vagrant halt

   # przeładowanie konfiguracji
   vagrant reload
   # lub jeśli mamy provisioning
   vagrant reload --provision

   # usunięcie vm
   vagrant destroy


Konfiguracja VM
---------------

Vagrant używa konfiguracji zapisanej w Vagrantfile, domyślny vagrantfile tworzymy instrukcją *vagrant init*.

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

Opcje konfiguracyjne dla domyślnego providera (VirtualBox):

::

   # ustawianie cpu i ram dla VirtualBoxa
   config.vm.provider "virtualbox" do |vb|
       vb.name = "my_vm" # nazwa vm
       vb.cpus = 2
       vb.memory = "4096"
       v.gui = true # tryb graficzny, domyślnie false
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

Przykładowa konfiguracja
------------------------

::

    TODO

Linki
-----

 - https://www.vagrantup.com/intro/getting-started/index.html
 - https://www.vagrantup.com/docs/index.html
 - https://virt-manager.org/
 - https://libvirt.org/index.html
