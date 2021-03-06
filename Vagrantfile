# -*- mode: ruby -*-
# vi: set ft=ruby :

Vagrant::Config.run do |config|
  
  #
  # Name of imported base box. (HINT: Rename this box)
  #
  config.vm.box = "ubuntu-11.10-server-amd64"
  
  #
  # Download url of base box if it has not been previously imported.
  # See http://vagrantbox.es/ for more pre-built base boxes or
  # build your own using https://github.com/jedi4ever/veewee
  #
  config.vm.box_url = "http://dl.dropbox.com/u/56687100/ubuntu-11.10-server-amd64.box"

  #
  # Set the memory size
  #
  config.vm.customize ["modifyvm", :id, "--memory", "1024"]

  #
  # Use port-forwarding. Web site will be at http://localhost:4567
  # Forwards guest port 80 to host port 4567 and name the mapping "web".
  #
  config.vm.forward_port(80, 4567, :auto => true)

  #
  # Use host-only networking. Sets the VM's private IP address.
  # Un-comment this line to use.  Make sure port-forwarding is
  # commented out. Requires you to edit your /etc/hosts file to
  # add the line: "172.21.21.21   local.drupal". Do so at your
  # own risk.  Site will then available at http://local.drupal
  #
  # config.vm.network :hostonly, "172.21.21.21"

  #
  # Create /srv if it doesn't exist and share with VM.
  # The /srv path is owned by www-data so apache can write to it.
  #
  srv_path = File.expand_path(File.dirname(__FILE__)) + "/srv"
  config.vm.share_folder("srv", "/srv", srv_path, :owner => "www-data", :group => "www-data", :create => true)

  #
  # Provision a new VM using chef-solo. The librarian gem controls
  # the "cookbook" folder, do not touch it.  If you need to create
  # site-specific cookbooks, place them in "site-cookbooks".
  #
  config.vm.provision :chef_solo do |chef|

    chef.log_level = :debug if ENV['vdb']

    chef.cookbooks_path = ["cookbooks", "site-cookbooks"]
    chef.add_recipe "xforty"
    chef.add_recipe "drupal"
    chef.add_recipe "initdb"
  
    # Specify custom JSON node attributes:
    chef.json.merge!(
      :drupal => {
        :project_name => "drupal",
        # Comment out server_name if you are using host-only networking.
        :server_name => "localhost"
      },
      :drush => {
        :version => "5.0.0"
      },
      :mysql => {
        :server_root_password => "root"
      },
      :initdb => {
        :mysql => {
          :connection => {
            :username => "root",
            :password => "root",
            :host     => "localhost"
          },
          :databases => {
            "drupal" => {
              :action => :create
            }
          },
          :users => {
            "dbuser" => {
              :action        => :grant,
              :database_name => "drupal",
              :host          => "localhost",
              :password      => "password"
            }
          }
        }
      }
    )
  end
end
