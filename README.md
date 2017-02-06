
This Vagrantfile will spawn a virtual topology corresponding to the Juniper Day One Guide
for BGP Multicast VPNs using vSRX running 12.1X47-D15.4

http://forums.juniper.net/jnet/attachments/jnet/Day1Books/87/3/TW_BGP_MVPNs.pdf

 ` # Topology
  #
  #  vsrx-CE1 -- dot1q -- vsrx-PE1  ----   vsrx-PE2 --dot1q -- vsrx-CE2
  #                                 \ /            
  #                               vsrx-P1 (RR)
  #                                 /  \            
  #  vsrx-CE3 -- dot1q -- vsrx-PE3  ----   vsrx-PE4 --dot1q -- vsrx-CE4`


* IS-IS is configured as the IGP with AS65000 for iBGP route exchange between PE's.
* P1 serves as the BGP-RR for this topology.
* 8x LSPs are configured between each PE, configured with auto-bandwidth and node-link protection.
* One vsrx-CE has 4x interfaces connected to each PE using VLANs simulating a customer CE with a single VM.
* Each vsrx when loaded consume ~8% CPU on my quad core macbook pro Intel Core i7 with ~1G of RAM per VM.

# System Requirements
### Resources per vSRX
 - RAM : 1G
 - CPU : 2 vCPU

### Resources per srv
  - RAM : 512MB
  - CPU : 1 vCPU

### Total Resources
  - RAM : 7G
  - CPU : 14 vCPU

This topology has been tested on a MacBook Pro (Retina, 15-inch, Mid 2014) running
Yosemite with 16GB of RAM.

### Tools
 - Ansible for provisioning (except for windows). Version 2.0.1.0 tested Apr2016.
    * I installed manually, but a slightly older verion is also available via homebrew.
 - Junos module for Ansible installed
    * for the lazy run "ansible-galaxy install Juniper.junos" to install or check
      the link below to install
    * https://github.com/Juniper/ansible-junos-stdlib
 - Vagrant 1.7.2
 - Virtualbox 5.0.16

 - Vagrant Atlas Public Boxes
   * https://vagrantcloud.com/robwc/boxes/minitrusty64/ (UbuntuServer)
   * https://vagrantcloud.com/juniper/boxes/ffp-12.1X47-D15.4-packetmode


# Provisioning / Configuration

Ansible is used to preconfigure all vSRX with an IP addresses on their interfaces
Both servers are preconfigured with an IP address and a route to their respective
network.

### To Bring Up
1. Clone https://git.juniper.net/barnesry/6vsrx-2srv-rsvp-te into a local directory
2. cd 6vsrx-2srv-rsvp-te
3. vagrant up
4. vagrant status (if you care to check before you connect)
5. vagrant ssh <host>


### Troubleshooting
If you run into issues launching for the first time, ensure you have the required Vagrant plugins
installed on your system. I've run into this multiple times, and it always gets me.

`$ vagrant plugin install vagrant-junos`
`$ vagrant plugin install vagrant-host-shell`

### Device Console Login
If 'vagrant ssh <device>' doesn't work and you need to root in via console for some reason.
  * vSRX User : root
  * vSRX Pass : Juniper1

Example Below from earlier derived topology.
--------------------------------------

    barnesry-mbp:day_one_bgp_mvpn barnesry$ vagrant status
    Current machine states:

    srv1                      not created (virtualbox)
    srv2                      not created (virtualbox)
    vsrx-PE1                  running (virtualbox)
    vsrx-PE3                  running (virtualbox)
    vsrx-PE2                  running (virtualbox)
    vsrx-PE4                  running (virtualbox)
    vsrx-P1                   running (virtualbox)
    vsrx-CE                   running (virtualbox)

    This environment represents multiple VMs. The VMs are all listed
    above with their current state. For more information about a specific
    VM, run `vagrant status NAME`.

    
    barnesry-mbp:Desktop barnesry$ git clone git@git.juniper.net:barnesry/6vsrx-2srv-rsvp-te.git
    Cloning into '6vsrx-2srv-rsvp-te'...
    remote: Counting objects: 32, done.
    remote: Compressing objects: 100% (29/29), done.
    remote: Total 32 (delta 10), reused 0 (delta 0)
    Receiving objects: 100% (32/32), 8.83 KiB | 0 bytes/s, done.
    Resolving deltas: 100% (10/10), done.
    Checking connectivity... done.
    
    barnesry-mbp:Desktop barnesry$ cd 6vsrx-2srv-rsvp-te/
    
    barnesry-mbp:6vsrx-2srv-rsvp-te barnesry$ ls
    README.md	Vagrantfile	provisioning
    
    barnesry-mbp:6vsrx-2srv-rsvp-te barnesry$ vagrant up
    Bringing machine 'srv1' up with 'virtualbox' provider...
    Bringing machine 'srv2' up with 'virtualbox' provider...
    Bringing machine 'vsrx2' up with 'virtualbox' provider...
    Bringing machine 'vsrx3' up with 'virtualbox' provider...
    Bringing machine 'vsrx4' up with 'virtualbox' provider...
    Bringing machine 'vsrx5' up with 'virtualbox' provider...
    Bringing machine 'vsrx1' up with 'virtualbox' provider...
    Bringing machine 'vsrx6' up with 'virtualbox' provider...
    
    Bringing machine 'vsrx1' up with 'virtualbox' provider...
    ==> vsrx1: Importing base box 'juniper/ffp-12.1X47-D15.4-packetmode'...
    ==> vsrx1: Matching MAC address for NAT networking...
    ==> vsrx1: Checking if box 'juniper/ffp-12.1X47-D15.4-packetmode' is up to date...
    ==> vsrx1: Setting the name of the VM: vsrx-PE1
    ==> vsrx1: Fixed port collision for 22 => 2222. Now on port 2202.
    ==> vsrx1: Clearing any previously set network interfaces...
    ==> vsrx1: Preparing network interfaces based on configuration...
        vsrx1: Adapter 1: nat
        vsrx1: Adapter 2: intnet
        vsrx1: Adapter 3: intnet
        vsrx1: Adapter 4: intnet
        vsrx1: Adapter 5: intnet
    ==> vsrx1: Forwarding ports...
        vsrx1: 22 => 2202 (adapter 1)
    ==> vsrx1: Running 'pre-boot' VM customizations...
    ==> vsrx1: Booting VM...
    ==> vsrx1: Waiting for machine to boot. This may take a few minutes...
        vsrx1: SSH address: 127.0.0.1:2202
        vsrx1: SSH username: root
        vsrx1: SSH auth method: private key
        vsrx1: Warning: Connection timeout. Retrying...
        vsrx1: Warning: Connection timeout. Retrying...
        vsrx1: Warning: Connection timeout. Retrying...
        vsrx1: Warning: Connection timeout. Retrying...
        vsrx1: Warning: Connection timeout. Retrying...
    ==> vsrx1: Machine booted and ready!
    ==> vsrx1: Checking for guest additions in VM...
        vsrx1: No guest additions were detected on the base box for this VM! Guest
        vsrx1: additions are required for forwarded ports, shared folders, host only
        vsrx1: networking, and more. If SSH fails on this machine, please install
        vsrx1: the guest additions and repackage the box to continue.
        vsrx1:
        vsrx1: This is not an error message; everything may continue to work properly,
        vsrx1: in which case you may ignore this message.
    ==> vsrx1: Setting hostname...
    ==> vsrx1: Running provisioner: ansible...
    PYTHONUNBUFFERED=1 ANSIBLE_FORCE_COLOR=true ANSIBLE_HOST_KEY_CHECKING=false ANSIBLE_SSH_ARGS='-o UserKnownHostsFile=/dev/null -o     ControlMaster=auto -o ControlPersist=60s' ansible-playbook --private-key=/Users/barnesry/.vagrant.d/insecure_private_key --    user=root --connection=ssh --limit='vsrx1' --inventory-file=/Users/barnesry/Desktop/6vsrx-2srv-rsvp-te/.vagrant/provisioners/ansible    /inventory provisioning/deploy-config.p.yaml
     [WARNING]: Not prompting as we are not in interactive mode
    
    
    PLAY [Generate and Deploy Configuration to P Routers] **************************
    skipping: no hosts matched
    
    PLAY [Generate and Deploy Configuration to PE Routers] *************************
    
    TASK [Build configuration] *****************************************************
    ok: [vsrx1]
    
    TASK [Deploy config to device ... please wait] *********************************
    changed: [vsrx1]
    
    TASK [Checking NETCONF connectivity] *******************************************
    ok: [vsrx1]
    
    PLAY RECAP *********************************************************************
    vsrx1                      : ok=3    changed=1    unreachable=0    failed=0
    
    
    <snip>
