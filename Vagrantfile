# -*- mode: ruby -*-
# vi: set ft=ruby :

VAGRANT_API_VERSION = "2"
BRIDGE_LINK = "wlp3s0"

vms=[
  {
    :define => 		"vmname",
    :hostname => 	"vmname",
    :os => 		"centos/7",
    :ram => 		512,
    :cpu =>		1,
  },

#  {
#    :define =>          "vmname",
#    :hostname =>        "vmname",
#    :os =>              "centos/7",
#    # Type can be rsync or nfs
#    :synced_folders => [
#                        {:host_dir => ".", :mount_dir => "/vagrant", :type => "rsync"},
#                       ],
#    :networks =>       [ 
#                        {:net_type => "private_network", :ip => "10.0.0.2"},
#                        {:net_type => "private_network", :type => "dhcp"},
#                        {:net_type => "public_network", :bridge => BRIDGE_LINK}
#                        {:net_type => "forwarded_type", :guest => 80, :host => 8080, :protocol => "tcp", :guest_ip => "", :host_ip => ""}
#                       ],
#    :ram =>            512,
#    :cpu =>            1,
#    # Remove it manually from /var/lib/libvirt/images/ after use
#    :disks =>          [ 
#                        {:path => "testdisk2.qcow2", :allow_existing => true, :size => "20G", :type => "qcow2"},
#                       ],
#  },
]

Vagrant.configure(VAGRANT_API_VERSION) do |config|
  config.vm.synced_folder ".", "/vagrant", disabled: true
  vms.each do |vm|
    config.vm.define vm[:define] do |config|

      config.vm.hostname = vm[:hostname]
      if (!vm[:os].nil?)
        config.vm.box = vm[:os]
      else
        config.vm.box = "centos/7"
      end
      # Synced folders
      if (!vm[:synced_folders].nil?)
        vm[:synced_folders].each do |synced_folder|
          config.vm.synced_folder synced_folder[:host_dir], synced_folder[:mount_dir], type: synced_folder[:type]
        end
      end
      # Network
      if (!vm[:networks].nil?)
        vm[:networks].each do |network|
          if (network[:net_type]=="private_network")
            if (!network[:ip].nil?)
              config.vm.network network[:net_type], ip: network[:ip]
            elsif (!network[:type].nil?)
              config.vm.network network[:net_type], type: network[:type]
            end
          elsif (network[:net_type]=="public_network")
            config.vm.network network[:net_type], bridge: network[:bridge]
          elsif (network[:net_type]=="forwarded_type")
            config.vm.network network[:net_type], guest: network[:guest], host: network[:host], protocol: network[:protocol], guest_ip: network[:guest_ip], host_ip: network[:host_ip]
          end
        end
      end
      config.vm.provider "libvirt" do |libvirt|
        libvirt.memory = vm[:ram]
        libvirt.cpus = vm[:cpu]
        libvirt.cputopology :sockets => '1', :cores => vm[:cpu], :threads => '1'
        libvirt.graphics_type = 'none'
        libvirt.machine_arch = 'x86_64'
        # Disks
        if (!vm[:disks].nil?)
          vm[:disks].each do |disk|
            libvirt.storage :file, :path => disk[:path], :size => disk[:size], :allow_existing => disk[:allow_existing], :type => disk[:type], allow_existing => true
          end
        end
      end

    end
  end
end
