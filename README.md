## AnonSH

An instant anonymous shell behind Tor, using Vagrant. All TCP traffic from the shell is routed through Tor; other traffic is dropped.


### Prerequisites

Install [Vagrant](http://downloads.vagrantup.com/) and [VirtualBox](https://www.virtualbox.org/wiki/Downloads).


### Usage

Clone the repository and run `vagrant up`. Once deployed, run `vagrant ssh shell`.


### Acknowledgements

AnonSH uses [BadVPN](https://code.google.com/p/badvpn/)'s excellent [Tun2socks](https://code.google.com/p/badvpn/wiki/tun2socks) to setup a TCP-to-Socks [Tor](https://www.torproject.org/) gateway in front of the shell box.


### License

BSD 2-Clause. See the LICENSE file for details.
