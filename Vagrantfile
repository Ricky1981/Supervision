# -*- mode: ruby -*-
# vi: set ft=ruby :

node_ip_address = "192.168.10.10"
node_hostname = 'projet10'

# subdomains = ['gitlab.devops.oc','pelican.devops.oc']

Vagrant.configure("2") do |config|
   # Utilisation de la boxe que nous avons crées via VirtualBox 
   config.vm.box = "vagrant-debian10.6.0"
   # Le paramètre config.vm.box_url est à utiliser si vous souhaiter mettre l'image ailleurs que dans le repertoire courant
   config.vm.box_url = "file:///home/seb/Documents/ownCloud/OpenClassRoom/Projet_03_RYKALA_Sebastien/vagrantBox/vagrant-debian10.6.0.box"
 
   #config.vm.provision "shell", path: "scripts/setup_ssh.sh"

   # Configuration SSH
   # Nous mettons le paramètre config.ssh.insert_key à false pour que Vagrant n'ajoute pas une clé de façon automatique (par défaut à true)
   config.ssh.insert_key = false
   # config.ssh.keys_only = true
   

   # Le paramètre config.vm.boot_timeout permet de changer la valeur du timeout (défaut 300 secondes) pour rendre les tests plus rapide en cas de problème
   config.vm.boot_timeout = 40

   config.vm.define "nodemanager" do |nodemanager|
      nodemanager.vm.hostname = node_hostname
      # Configuration SSH
      # Nous mettons le paramètre config.ssh.insert_key à false pour que Vagrant n'ajoute pas une clé de façon automatique (par défaut à true)
      nodemanager.ssh.insert_key = false
  
      # Nous créons un réseau privé pour permettre à notre machine de trouver et pouvoir communiquer avec notre VM 
      nodemanager.vm.network "private_network", ip: node_ip_address  
       
      # Copie du docker-compose.yml
      nodemanager.vm.provision "file" do |file|
         file.source      = '~/Documents/ownCloud/OpenClassRoom/Projet_10/docker-compose.yml'
         file.destination = '/home/vagrant/docker-compose.yml'
      end

      # # Copie des playbooks Ansible 
      # nodemanager.vm.provision "file" do |file|
      #    file.source      = '~/Documents/ownCloud/OpenClassRoom/Projet_07/ansible.zip'
      #    file.destination = '/home/vagrant/ansible.zip'
      # end
      
      # Etant donné que le plugin vbguest a été ajouter, nous devons indiquer à vagrant de ne pas tenter l'update 
      if Vagrant.has_plugin?("vagrant-vbguest")
         nodemanager.vbguest.auto_update = false  
      end

      # Ajout de ce plugin qui permet de rajouter dans le fichier hosts les informations necessaires aux différents domaines
      if Vagrant.has_plugin?('vagrant-hostsupdater')
         nodemanager.hostsupdater.aliases = node_hostname
         # Ci-dessous, je ne souhaite pas que mes entrées dans mon hosts soient supprimés lors d'un vagrant halt ou suspend --> https://github.com/agiledivider/vagrant-hostsupdater
         nodemanager.hostsupdater.remove_on_suspend = false
      end
  
      # Nous provisionnons notre VM avec les outils dont nous avons besoin pour travailler
      nodemanager.vm.provision "shell", inline: <<-SHELL
         BASHRCFILE='/home/vagrant/.bashrc'
         DEPOTFILE='/etc/apt/sources.list'

         # Ajout Editeur Texte   
         apt-get update
         apt-get install -y vim zip
         
         # Ajout Docker
         apt-get install -y apt-transport-https ca-certificates curl gnupg gnupg-agent software-properties-common lsb-release
         curl -fsSL https://download.docker.com/linux/debian/gpg | sudo apt-key add -
         add-apt-repository "deb [arch=amd64] https://download.docker.com/linux/debian $(lsb_release -cs) stable"
         apt-get update && apt-get install -y docker-ce docker-ce-cli containerd.io
         usermod -aG docker vagrant	

         # Ajout Docker Compose
         curl -Ls "https://github.com/docker/compose/releases/download/1.27.4/docker-compose-$(uname -s)-$(uname -m)" -o /usr/local/bin/docker-compose 
         chmod +x /usr/local/bin/docker-compose
            
      SHELL
   end

   
end