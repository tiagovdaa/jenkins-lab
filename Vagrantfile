IMAGE_NAME = "ubuntu/jammy64"
NAME = "jenkins-lab"
CPU = 2
MEM = 4096
HOSTNAME = "jenkins-lab"

VAGRANT_DISABLE_VBOXSYMLINKCREATE=1

$DEFAULT_NETWORK_INTERFACE = `ip route | awk '/^default/ {printf "%s", $5; exit 0}'`

Vagrant.configure("2") do |config|
    config.vm.box = IMAGE_NAME
    config.ssh.insert_key = false

    config.vm.define HOSTNAME do |vm|
      vm.vm.hostname = HOSTNAME
      vm.vm.network "public_network", bridge: $DEFAULT_NETWORK_INTERFACE
      vm.vm.provider :virtualbox do |vb|
        vb.name = HOSTNAME
        vb.cpus = CPU
        vb.memory = MEM
      end

      vm.vm.provision "ansible" do |ansible|
        ansible.playbook = "ansible/playbook.yaml"
        ansible.extra_vars = {
          username: "#{ENV['USERNAME'] || `whoami`}",
        }
      end

      $script = <<-SCRIPT
      echo "configured networks: \n"
      ip -br a |grep -i up|awk '{print "INTERFACE:",$1,"ADDRESS:",$3}'
      SCRIPT
      vm.vm.provision "shell" do |shell|
        shell.inline = $script
      end
    end

    config.vm.synced_folder ".", "/vagrant", disabled: true

end
