# -*- mode: ruby -*-
# vi: set ft=ruby :

# WARNING: This Vagrantfile is for development purposes only. It is intended to
# bootstrap required services - such as mysql and rabbit - into a reliably
# accessible VM, rather than forcing the engineer to install and manage these
# services manually. This Vagrantfile is not intended to assist in provisioning
# Ironic. For that, please use the bifrost project.

VAGRANTFILE_API_VERSION = '2'

#Set the default provider to libvirt in the case they forget --provider=libvirt or if someone destroys a machine it reverts to virtualbox
ENV['VAGRANT_DEFAULT_PROVIDER'] = 'libvirt'

# Check required plugins
REQUIRED_PLUGINS_LIBVIRT = %w(vagrant-libvirt vagrant-mutate)
exit unless REQUIRED_PLUGINS_LIBVIRT.all? do |plugin|
  Vagrant.has_plugin?(plugin) || (
    puts "The #{plugin} plugin is required. Please install it with:"
    puts "$ vagrant plugin install #{plugin}"
    false
  )
end

Vagrant.require_version ">= 2.0.2"

Vagrant.configure(VAGRANTFILE_API_VERSION) do |config|

  ## config.vm.box = 'ubuntu/trusty64'
  config.vm.box = 'ubuntu-trusty64'

  config.ssh.username = "vagrant"

  config.vm.define 'ironic' do |ironic|
    #ironic.vm.provider :virtualbox do |vb|
    #  vb.customize ['modifyvm', :id, '--memory', '512', '--cpuexecutioncap', '25']
    #  ##
    #  vb.gui = false
    #end
    ironic.vm.provider :libvirt do |libvirt|
      libvirt.cpus = 2
      ##libvirt.cputopology :sockets => '2', :cores => '2', :threads => '1'
      libvirt.memory = 2048
      libvirt.nested = true
      libvirt.volume_cache = 'none'
      ##libvirt.driver = 'qemu'
    end

    ##ironic.vm.network 'private_network', ip: '192.168.99.11' # It goes to 11.
    ironic.vm.network :private_network, :ip => "192.168.99.11"

    ## ironic.vm.provision 'ansible' do |ansible|
    ironic.vm.provision 'ansible_local' do |ansible|
      ansible.verbose = 'vvv'
      ansible.playbook = 'vagrant.yaml'
      ansible.extra_vars = {
          ip: '192.168.99.11'
      }
    end
  end

  # Public Network
  config.vm.define :ironic do |ironic|
    ironic.vm.network :public_network,
         :dev => "virbr0",
         :mode => "bridge",
         :type => "bridge"
  end
end


