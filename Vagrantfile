Vagrant.configure("2") do |config|
    config.vm.box = "debian-7.3"
    config.vm.box_url = "http://puppet-vagrant-boxes.puppetlabs.com/debian-73-x64-virtualbox-nocm.box"

    config.vm.define "gw" do |gw|
        gw.vm.hostname = "gw"
        gw.vm.provision :shell, :inline => $gwprov

        gw.vm.provider :virtualbox do |vb, override|
            vb.customize ["modifyvm", :id, "--memory", "256"]
            override.vm.network :private_network, :ip => "172.21.21.21", :netmask => "255.255.255.0"
        end
    end

    config.vm.define "shell" do |shell|
        shell.vm.hostname = "shell"
        shell.vm.provision :shell, :inline => $shprov

        shell.vm.provider :virtualbox do |vb, override|
            vb.customize ["modifyvm", :id, "--memory", "256"]
            override.vm.network :private_network, :ip => "172.21.21.172", :netmask => "255.255.255.0"
        end
    end
end

$gwprov = <<SCRIPT
    function install_packages {
        export DEBIAN_FRONTEND=noninteractive
        apt-get update
        apt-get upgrade -y
        apt-get install -y build-essential pkg-config cmake libssl-dev libnspr4-dev libnss3-dev screen tor

        cd /tmp; wget https://badvpn.googlecode.com/files/badvpn-1.999.128.tar.bz2
        bunzip2 -c badvpn-1.999.128.tar.bz2 | tar xf -
        mkdir badvpn-1.999.128/build; cd badvpn-1.999.128/build
        cmake .. && make
    }

    function setup_gw {
        echo "DNSPort 53" >> /etc/tor/torrc
        echo "DNSListenAddress 172.21.21.21" >> /etc/tor/torrc
        service tor restart

        ip tuntap add dev tun0 mode tun
        ip addr add 172.21.22.1/24 dev tun0
        ip link set dev tun0 up
        screen -S tun2socks -d -m bash -c '/tmp/badvpn-1.999.128/build/tun2socks/badvpn-tun2socks --tundev tun0 --netif-ipaddr 172.21.22.2 --netif-netmask 255.255.255.0 --socks-server-addr 127.0.0.1:9050'

        ip route add default via 172.21.22.2 dev tun0 table 1000
        ip rule add fwmark 2 table 1000
        iptables -t mangle -A PREROUTING -i eth1 -j MARK --set-mark 2
        iptables -t nat -A POSTROUTING -s 172.21.21.172/32 -o tun0 -m mark --mark 0x2 -j SNAT --to-source 172.21.22.1
        iptables -A FORWARD -i eth1 -o eth0 -j REJECT
        echo 2 > /proc/sys/net/ipv4/conf/tun0/rp_filter
        echo 1 > /proc/sys/net/ipv4/ip_forward
    }

    echo "Installing packages..."; install_packages > /dev/null 2>&1
    echo "Configuring the gateway..."; setup_gw > /dev/null 2>&1
SCRIPT

$shprov = <<SCRIPT
    function install_packages {
        apt-get update
        apt-get install -y vim curl
    }

    function setup_networking {
        echo "nameserver 172.21.21.21" > /etc/resolv.conf
        ip route del default
        ip route add default via 172.21.21.21
    }

    echo "Installing packages..."; install_packages > /dev/null 2>&1
    echo "Setting up networking..."; setup_networking > /dev/null 2>&1
SCRIPT
