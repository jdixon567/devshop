# -*- mode: ruby -*-
# vi: set ft=ruby :

VAGRANTFILE_API_VERSION = "2"
Vagrant.require_version ">= 1.5"

Vagrant.configure(VAGRANTFILE_API_VERSION) do |config|

  # Load Variables
  require 'yaml'
  settings = YAML.load_file(File.dirname(__FILE__) + "/vars.yml")

  # Base Box & Config
  config.vm.box = "ubuntu/trusty64"

  config.vm.provider "virtualbox" do |v|
    v.memory = 2048
  end

  # Prepare host for development
  if (settings["vagrant_development"])
    system('bash ' + File.dirname(__FILE__) + '/vagrant-prepare-host.sh ' + File.dirname(__FILE__) + ' ' + settings["devmaster_version"])
  end

  # Uncomment to test with other types of boxes.
  # config.vm.box = "hashicorp/precise64"
  # config.vm.box = "chef/centos-6.5"
  # config.vm.box = "chef/centos-7.0"

  # DevShop Master
  # Set to be the default machine.
  # Use `vagrant up` to launch.
  config.vm.define "devmaster", primary: true do |devmaster|
    devmaster.vm.hostname = settings["server_hostname"]
    devmaster.vm.network "private_network", ip: settings["vagrant_private_network_ip"]

    # Set SH as our provisioner
    devmaster.vm.provision "shell",
      path: settings["vagrant_install_script"],
      args: "/vagrant"

    # Prepare development environment
    if (settings["vagrant_development"])

      devmaster.vm.synced_folder "source/devmaster-" + settings["devmaster_version"], "/var/aegir/devmaster-" + settings["devmaster_version"],
          mount_options: ["uid=12345,gid=12345"]

      devmaster.vm.synced_folder "source/drush", "/var/aegir/.drush/commands",
          mount_options: ["uid=12345,gid=12345"]

      # config.vm.synced_folder "source/projects", "/var/aegir/projects",
      #    mount_options: ["uid=12345,gid=12345"]

      devmaster.vm.provision "shell",
        path: 'vagrant-prepare-guest.sh'
    end
  end

  # DevShop Remote
  # Does not start automatically on vagrant up.
  # Use `vagrant up remote` to launch.
  config.vm.define "remote", autostart: false do |remote|
    remote.vm.hostname = settings["remote_server_hostname"]
    remote.vm.network "private_network", ip: settings["remote_vagrant_private_network_ip"]
    remote.vm.provider "virtualbox" do |v|
      v.memory = 1024
    end
  end
  config.vm.define "remote2", autostart: false do |remote|
    remote.vm.hostname = settings["remote2_server_hostname"]
    remote.vm.network "private_network", ip: settings["remote2_vagrant_private_network_ip"]
    remote.vm.provider "virtualbox" do |v|
      v.memory = 1024
    end
  end
end

class NoSettingsException < Vagrant::Errors::VagrantError
  error_message('Project settings file not found. Create attributes.json file then try again.')
end
