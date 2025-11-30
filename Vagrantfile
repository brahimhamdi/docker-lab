Vagrant.configure("2") do |config|
  # Configuration de la VM
  config.vm.define "docker" do |docker|
    docker.vm.box = "generic/ubuntu2204"
    docker.vm.hostname = "docker"
    
    # Configuration réseau
    docker.vm.network "private_network", ip: "192.168.205.10"
    
    # Configuration des ressources
    docker.vm.provider "virtualbox" do |vb|
      vb.memory = 4096
      vb.cpus = 2
    end
    
    # Provisioning
    docker.vm.provision "shell", inline: <<-SHELL
      # Configuration de Vim
      echo 'colorscheme ron' >> ~/.vimrc
      echo 'set tabstop=2' >> ~/.vimrc
      echo 'set shiftwidth=2' >> ~/.vimrc
      echo 'set expandtab' >> ~/.vimrc
      
      # Mise à jour et installation des paquets de base
      sudo apt update
      sudo apt install -y apt-transport-https ca-certificates curl software-properties-common git gcc tree openjdk-17-jdk maven mariadb-client
      
      # Installation de Docker
      curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo apt-key add -
      sudo add-apt-repository "deb [arch=amd64] https://download.docker.com/linux/ubuntu $(lsb_release -cs) stable"
      
      # Installation de Trivy
      wget -qO - https://aquasecurity.github.io/trivy-repo/deb/public.key | sudo apt-key add -
      echo "deb https://aquasecurity.github.io/trivy-repo/deb $(lsb_release -sc) main" | sudo tee -a /etc/apt/sources.list.d/trivy.list
      
      # Installation des paquets Docker et Trivy
      sudo apt update
      sudo apt install -y docker-ce trivy
      
      # Ajout de l'utilisateur vagrant au groupe docker
      sudo usermod -aG docker vagrant
      
      # Redémarrage du service Docker (optionnel)
      sudo systemctl enable docker
      sudo systemctl start docker
      
      echo "Provisioning terminé !"
    SHELL
  end
end
