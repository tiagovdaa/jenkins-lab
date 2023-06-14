IMAGE_NAME = "debian/bookworm64"
NAME = "jenkins-lab"
CPU = 2
MEM = 4096
HOSTNAME = "jenkins-lab"
CONNECTION = 'qemu:///system'

Vagrant.configure("2") do |config|
    config.vm.box = IMAGE_NAME
    config.ssh.insert_key = false

    config.vm.define HOSTNAME do |vm|
      vm.vm.hostname = HOSTNAME
      vm.vm.network :private_network, 
                    :libvirt__network_name => 'isolated', 
                    :libvirt__network_autostart => false
      vm.vm.provider :libvirt do |libvirt|
        libvirt.driver = "kvm"
        libvirt.host = HOSTNAME
        libvirt.uri = CONNECTION
        libvirt.memory = MEM
        libvirt.cpus = CPU
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
