Vagrant.require_version ">= 2.0.0"

boxes = [
    {
        :name => "Manager",
        :eth1 => "192.168.205.10",
        :mem => "4096",
        :cpu => "2"
    },
    {
        :name => "worker1",
        :eth1 => "192.168.205.11",
        :mem => "2048",
        :cpu => "1"
    },
    {
        :name => "worker2",
        :eth1 => "192.168.205.12",
        :mem => "2048",
        :cpu => "1"
    }

]

Vagrant.configure(2) do |config|
  config.vm.box = "generic/ubuntu2204"

  boxes.each do |opts|
      config.vm.define opts[:name] do |config|
        config.vm.hostname = opts[:name]

        config.vm.provider "vmware_fusion" do |v|
          v.vmx["memsize"] = opts[:mem]
          v.vmx["numvcpus"] = opts[:cpu]
          v.customize ["modifyvm", :id, "--uart1", "0x3F8", "4"]
          v.customize ["modifyvm", :id, "--uartmode1", "file", File::NULL]
        end

        config.vm.provider "virtualbox" do |v|
          v.customize ["modifyvm", :id, "--memory", opts[:mem]]
          v.customize ["modifyvm", :id, "--cpus", opts[:cpu]]
        end
        config.vm.network :private_network, ip: opts[:eth1]
      end
  end

  config.vm.provision "shell", inline: <<-SHELL
    sudo apt update
    sudo apt install -y apt-transport-https ca-certificates curl software-properties-common git gcc tree openjdk-17-jdk maven mariadb-client

    curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo apt-key add -
    sudo add-apt-repository "deb [arch=amd64] https://download.docker.com/linux/ubuntu bionic stable"

    wget -qO - https://aquasecurity.github.io/trivy-repo/deb/public.key | sudo apt-key add -
    echo "deb https://aquasecurity.github.io/trivy-repo/deb $(lsb_release -sc) main" | sudo tee -a /etc/apt/sources.list.d/trivy.list

    sudo apt update
    sudo apt install -y docker-ce docker-compose trivy
    sudo usermod -aG docker vagrant
  SHELL
end
