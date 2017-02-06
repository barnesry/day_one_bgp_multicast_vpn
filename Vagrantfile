# -*- mode: ruby -*-
# vi: set ft=ruby :

# Topology
#
#  vsrx-CE1 -- dot1q -- vsrx-PE1  ----   vsrx-PE2 --dot1q -- vsrx-CE2
#                                 \ /            
#                               vsrx-P1 (RR)
#                                 /  \            
#  vsrx-CE3 -- dot1q -- vsrx-PE3  ----   vsrx-PE4 --dot1q -- vsrx-CE4


VAGRANTFILE_API_VERSION = "2"
Vagrant.configure(VAGRANTFILE_API_VERSION) do |config|


    config.ssh.insert_key = false

    # (1..2).each do |id|

    #     srv_name  = ( "srv" + id.to_s ).to_sym

    #     ##########################
    #     ## Server          #######
    #     ##########################
    #     config.vm.define srv_name do |srv|
    #         srv.vm.box = "robwc/minitrusty64"
    #         srv.vm.hostname = "server#{id}"

    #         srv.vm.provider "virtualbox" do |v|
    #             v.name = srv.vm.hostname
    #             v.memory = 512
    #         end

    #         srv.vm.network 'private_network', ip: "10.10.#{id}.2", virtualbox__intnet: "server_vsrx#{id}"
    #         srv.ssh.insert_key = true
	#     srv.ssh.shell = "bash -c 'BASH_ENV=/etc/profile exec bash'"
            
	#     srv.vm.provision "shell",
    #            inline: "sudo route add -net 10.10.0.0 netmask 255.255.0.0 gw 10.10.#{id}.1
	#        		sudo apt-get -y update
	#        		sudo apt-get -y install traceroute curl telnet apache2
	# 	        sudo wget -progress=dot:giga -O /var/www/html/10MB.zip http://download.thinkbroadband.com/10MB.zip
	# 	        echo 'for i in \$(seq 1 10); do wget --progress=dot:giga -O 10MB.zip http://10.10.#{id}.2/10MB.zip; done' > wget_files.sh
	# 		sudo chmod +x wget_files.sh"
    #    end
    # end

    ## Core Left PE Routers ##
    [1,3].each do |id|

        re_name  = ( "vsrx-PE" + id.to_s ).to_sym

        ##########################
        ## Routing Engine  #######
        ##########################
        config.vm.define re_name do |vsrx|
            # vsrx.vm.hostname = "vsrx-P#{id}"
            vsrx.vm.hostname = re_name.to_s
            # vsrx.vm.box = 'juniper/ffp-12.1X47-D20.7'
            vsrx.vm.box = 'juniper/ffp-12.1X47-D15.4-packetmode'

            vsrx.vm.provider "virtualbox" do |v|
                v.name = "vagrant-" + vsrx.vm.hostname
                v.memory = 1024
                v.cpus = 2
            end

            # DO NOT REMOVE / NO VMtools installed
            vsrx.vm.synced_folder '.', '/vagrant', disabled: true

            # Management port in vboxnet0 ip is irrelevant but needed to create the interface
            # define the actual IP address in the Ansibe .provisioning files
            vsrx.vm.network 'private_network', auto_config: false, nic_type: 'virtio', ip: "192.168.56.20#{id}"

            # Dataplane ports
            vsrx.vm.network 'private_network', auto_config: false, nic_type: 'virtio', virtualbox__intnet: "ce#{id}-pe#{id}"
            vsrx.vm.network 'private_network', auto_config: false, nic_type: 'virtio', virtualbox__intnet: "pe#{id}-pe#{id+1}"
            vsrx.vm.network 'private_network', auto_config: false, nic_type: 'virtio', virtualbox__intnet: "pe#{id}-p1"

        end
    end


    ## Core Right PE Routers ##
    [2,4].each do |id|

        re_name  = ( "vsrx-PE" + id.to_s ).to_sym

        ##########################
        ## Routing Engine  #######
        ##########################
        config.vm.define re_name do |vsrx|
            # vsrx.vm.hostname = "vsrx-P#{id}"
            vsrx.vm.hostname = re_name.to_s
            # vsrx.vm.box = 'juniper/ffp-12.1X47-D20.7'
            vsrx.vm.box = 'juniper/ffp-12.1X47-D15.4-packetmode'

            vsrx.vm.provider "virtualbox" do |v|
                v.name = "vagrant-" + vsrx.vm.hostname
                v.memory = 1024
                v.cpus = 2
            end

            # DO NOT REMOVE / NO VMtools installed
            vsrx.vm.synced_folder '.', '/vagrant', disabled: true

            # Management port in vboxnet0
            vsrx.vm.network 'private_network', auto_config: false, nic_type: 'virtio', ip: "192.168.56.20#{id}"

            # Dataplane ports
            vsrx.vm.network 'private_network', auto_config: false, nic_type: 'virtio', virtualbox__intnet: "ce#{id}-pe#{id}"
            vsrx.vm.network 'private_network', auto_config: false, nic_type: 'virtio', virtualbox__intnet: "pe#{id-1}-pe#{id}"
            vsrx.vm.network 'private_network', auto_config: false, nic_type: 'virtio', virtualbox__intnet: "pe#{id}-p1"

        end
    end


    ## P Router ##
    [1].each do |id|

        re_name  = ( "vsrx-P" + id.to_s ).to_sym

        ##########################
        ## Routing Engine  #######
        ##########################
        config.vm.define re_name do |vsrx|
            # vsrx.vm.hostname = "vsrx-PE#{id}"
            vsrx.vm.hostname = re_name.to_s
            # vsrx.vm.box = 'juniper/ffp-12.1X47-D20.7'
            vsrx.vm.box = 'juniper/ffp-12.1X47-D15.4-packetmode'

            vsrx.vm.provider "virtualbox" do |v|
                v.name = vsrx.vm.hostname
                v.memory = 1024
                v.cpus = 2
            end

            # DO NOT REMOVE / NO VMtools installed
            vsrx.vm.synced_folder '.', '/vagrant', disabled: true

            # Management port
            #vsrx.vm.network 'private_network', auto_config: false, nic_type: 'virtio', virtualbox__intnet: "vboxnet0"
            vsrx.vm.network 'private_network', auto_config: false, nic_type: 'virtio', ip: "192.168.56.150"

            # Dataplane ports
            vsrx.vm.network 'private_network', auto_config: false, nic_type: 'virtio', virtualbox__intnet: "pe1-p1"
            vsrx.vm.network 'private_network', auto_config: false, nic_type: 'virtio', virtualbox__intnet: "pe2-p1"
            vsrx.vm.network 'private_network', auto_config: false, nic_type: 'virtio', virtualbox__intnet: "pe3-p1"
            vsrx.vm.network 'private_network', auto_config: false, nic_type: 'virtio', virtualbox__intnet: "pe4-p1"

        end
    end

    ## CE Routers ##
    [1].each do |id|

        re_name  = ( "vsrx-CE" ).to_sym

        ##########################
        ## Routing Engine  #######
        ##########################
        config.vm.define re_name do |vsrx|
            vsrx.vm.hostname = "vsrx-CE"
            # vsrx.vm.box = 'juniper/ffp-12.1X47-D20.7'
            vsrx.vm.box = 'juniper/ffp-12.1X47-D15.4-packetmode'

            vsrx.vm.provider "virtualbox" do |v|
                v.name = vsrx.vm.hostname
                v.memory = 1024
                v.cpus = 2
            end

            # DO NOT REMOVE / NO VMtools installed
            vsrx.vm.synced_folder '.', '/vagrant', disabled: true

            # Management port
            vsrx.vm.network 'private_network', auto_config: false, nic_type: 'virtio', ip: "192.168.56.205"

            # Dataplane ports facing srv
            vsrx.vm.network 'private_network', auto_config: false, nic_type: 'virtio', virtualbox__intnet: "ce1-pe1"
            vsrx.vm.network 'private_network', auto_config: false, nic_type: 'virtio', virtualbox__intnet: "ce2-pe2"
            vsrx.vm.network 'private_network', auto_config: false, nic_type: 'virtio', virtualbox__intnet: "ce3-pe3"
            vsrx.vm.network 'private_network', auto_config: false, nic_type: 'virtio', virtualbox__intnet: "ce4-pe4"

        end
    end


    ##############################
    ## Box provisioning    #######
    ##############################
    if !Vagrant::Util::Platform.windows?
        config.vm.provision "ansible" do |ansible|
            ansible.groups = {
                "vsrx-pe" => [ "vsrx-PE1", "vsrx-PE2", "vsrx-PE3", "vsrx-PE4" ],
                "vsrx-p" => [ "vsrx-P1" ],
                "vsrx-ce" => [ "vsrx-CE" ],
                "server"  => [ "server1", "server2" ],
                "all:children" => [ "vsrx-pe", "vsrx-p", "vsrx-ce", "server" ]
            }
            ansible.playbook = "provisioning/deploy-config.p.yaml"
        end
    end
end
