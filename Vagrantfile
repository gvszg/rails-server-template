# -*- mode: ruby -*-
# vi: set ft=ruby :

# All Vagrant configuration is done below. The "2" in Vagrant.configure
# configures the configuration version (we support older styles for
# backwards compatibility). Please don't change it unless you know what
# you're doing.
Vagrant.configure(2) do |config|
  # The most common configuration options are documented and commented below.
  # For a complete reference, please see the online documentation at
  # https://docs.vagrantup.com.

  # Every Vagrant development environment requires a box. You can search for
  # boxes at https://atlas.hashicorp.com/search.
  config.vm.box = "ubuntu/trusty64"

  # This should match the version specified in your
  # Gemfile. You must have the omnibus vagrant plugin
  # installed for this to work.
  config.omnibus.chef_version = "11.16.0"

  # Enable if you want to use the Vagrant Berkshelf plugin to manage
  # Cookbooks.
  config.berkshelf.enabled = true

  # Assumes that the Vagrantfile is in the root of our
  # Chef repository.
  root_dir = File.dirname(File.expand_path(__FILE__))

  # Assumes that the node definitions are in the nodes
  # subfolder
  nodes = Dir[File.join(root_dir,'nodes','*.json')]

  # Iterate over each of the JSON files
  nodes.each do |file|
    node_json = JSON.parse(File.read(file))

    # Only process the node if it has a vagrant section
    if(node_json["vagrant"])
      vagrant_name = node_json["vagrant"]["name"]
      vagrant_ip = node_json["vagrant"]["ip"]

      # Allow us to remove certain items from the run_list if we're
      # using vagrant. Useful for things like networking configuration
      # which may not apply/ may break in the vagrant environment
      if exclusions = node_json["vagrant"]["exclusions"]
        exclusions.each do |exclusion|
          if node_json["run_list"].delete(exclusion)
            puts "removed #{exclusion} from the run list"
          end
        end
      end
      config.vm.define vagrant_name do |vagrant|
        vagrant.vm.hostname = vagrant_name

        # Only use private networking if we specified an
        # IP. Otherwise fallback to DHCP
        if vagrant_ip
          vagrant.vm.network :private_network, ip: vagrant_ip
        end

        vagrant.vm.provision "chef_solo" do |chef|

          # Use berks-cookbooks not cookbooks and remember
          # to explicitly vendor berkshelf cookbooks with
          # berks vendor if not using the berkshelf vagrant plugin
          chef.cookbooks_path = ["berks-cookbooks", "site-cookbooks"]
          chef.data_bags_path = "data_bags"
          chef.roles_path = "roles"

          # Instead of using add_recipe and add_role, just
          # assign the node definition json, this will take
          # care of populating the run_list.
          chef.json = node_json
        end
      end
    end
  end

  # Disable automatic box update checking. If you disable this, then
  # boxes will only be checked for updates when the user runs
  # `vagrant box outdated`. This is not recommended.
  # config.vm.box_check_update = false

  # Create a forwarded port mapping which allows access to a specific port
  # within the machine from a port on the host machine. In the example below,
  # accessing "localhost:8080" will access port 80 on the guest machine.
  config.vm.network "forwarded_port", guest: 80, host: 1234

  # Create a private network, which allows host-only access to the machine
  # using a specific IP.
  config.vm.network "private_network", ip: "33.33.33.10"

  # Create a public network, which generally matched to bridged network.
  # Bridged networks make the machine appear as another physical device on
  # your network.
  # config.vm.network "public_network"

  # Share an additional folder to the guest VM. The first argument is
  # the path on the host to the actual folder. The second argument is
  # the path on the guest to mount the folder. And the optional third
  # argument is a set of non-required options.
  # config.vm.synced_folder "../data", "/vagrant_data"

  # Provider-specific configuration so you can fine-tune various
  # backing providers for Vagrant. These expose provider-specific options.
  # Example for VirtualBox:
  #
  config.vm.provider "virtualbox" do |vb|
    # Display the VirtualBox GUI when booting the machine
    # vb.gui = true
  
    # Customize the amount of memory on the VM:
    vb.memory = "1024"
    vb.cpus = 2
  end
  #
  # View the documentation for the provider you are using for more
  # information on available options.

  # Define a Vagrant Push strategy for pushing to Atlas. Other push strategies
  # such as FTP and Heroku are also available. See the documentation at
  # https://docs.vagrantup.com/v2/push/atlas.html for more information.
  # config.push.define "atlas" do |push|
  #   push.app = "YOUR_ATLAS_USERNAME/YOUR_APPLICATION_NAME"
  # end

  # Enable provisioning with a shell script. Additional provisioners such as
  # Puppet, Chef, Ansible, Salt, and Docker are also available. Please see the
  # documentation for more information about their specific syntax and use.
  # config.vm.provision "shell", inline: <<-SHELL
  #   sudo apt-get update
  #   sudo apt-get install -y apache2
  # SHELL
end
