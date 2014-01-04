Vagrant.configure("2") do |config|
    config.vm.box = "precise64"
    config.vm.box_url = "http://files.vagrantup.com/precise64.box"

    config.vm.define "gw" do |gw|
        gw.vm.hostname = "gw"

        gw.vm.provider :virtualbox do |vb, override|
            vb.customize ["modifyvm", :id, "--memory", "256"]
            override.vm.network :private_network, :ip => "172.21.21.21", :netmask => "255.255.255.0"
        end
    end

    config.vm.define "shell" do |shell|
        shell.vm.hostname = "shell"

        shell.vm.provider :virtualbox do |vb, override|
            vb.customize ["modifyvm", :id, "--memory", "256"]
            override.vm.network :private_network, :ip => "172.21.21.172", :netmask => "255.255.255.0"
        end
    end
end
