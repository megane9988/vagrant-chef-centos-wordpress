# encoding: utf-8
# -*- mode: ruby -*-
# vi: set ft=ruby :

VAGRANTFILE_API_VERSION = "2"


# Configuration for the WordPress

WP_VERSION         = 'latest' # latest or 3.4 or later
WP_LANG            = "ja" # WordPress locale

WP_HOSTNAME        = "wordpress.local" # e.g example.com
WP_DIR             = '' # e.g. /wp or wp or other

WP_TITLE           = "Welcome to the Vagrant" # title
WP_ADMIN_USER      = "admin" # default user
WP_ADMIN_PASS      = "admin" # default user's password

WP_DB_PREFIX       = 'wp_' # Database prefix

WP_DEFAULT_PLUGINS = %w(theme-check plugin-check hotfix) # default plugins
WP_DEFAULT_THEME   = '' # e.g. twentythirteen

WP_IS_MULTISITE    = false # enable multisite when true
WP_FORCE_SSL_ADMIN = false # enable force ssl admin when true

WP_ALWAYS_RESET    = true # always reset database
WP_IP              = "192.168.33.10" # host ip address

# end configuration

Vagrant.configure(VAGRANTFILE_API_VERSION) do |config|

  config.vm.box = "cent64_minimal_i386"
  config.vm.box_url = "http://developer.nrel.gov/downloads/vagrant-boxes/CentOS-6.4-i386-v20130427.box"

  config.vm.hostname = WP_HOSTNAME
  config.vm.network :private_network, ip: WP_IP

  config.vm.synced_folder "www/", "/var/www", :create => "true"

  # for CentOS ipv6 issue
  config.vm.provider :virtualbox do |vb|
    vb.customize ["modifyvm", :id, "--natdnsproxy1", "off"]
    vb.customize ["modifyvm", :id, "--natdnshostresolver1", "off"]
  end

  config.vm.provision :chef_solo do |chef|

    chef.cookbooks_path = ["cookbooks", "site-cookbooks"]

    chef.json = {
      :apache => {
        :docroot_dir => '/var/www/wordpress',
        :user => 'vagrant',
        :group => 'vagrant',
        :listen_ports => ["80", "443"]
      },
      :php => {
        :packages => %w(php php-cli php-devel php-mbstring php-gd php-xml php-mysql)
      },
      :mysql => {
        :server_debian_password => "wordpress",
        :server_root_password => "wordpress",
        :server_repl_password => "wordpress"
      },
      :"wp-install" => {
        :wp_version => WP_VERSION,
        :url => "http://" << File.join(WP_HOSTNAME, WP_DIR),
        :wpdir => File.join('/var/www/wordpress', WP_DIR),
        :locale => WP_LANG,
        :admin_user => WP_ADMIN_USER,
        :admin_password => WP_ADMIN_PASS,
        :dbprefix => WP_DB_PREFIX,
        :default_plugins => WP_DEFAULT_PLUGINS,
        :default_theme => WP_DEFAULT_THEME,
        :title => WP_TITLE,
        :is_multisite => WP_IS_MULTISITE,
        :force_ssl_admin => WP_FORCE_SSL_ADMIN,
        :always_reset => WP_ALWAYS_RESET
      }
    }

    chef.add_recipe "yum::epel"
    chef.add_recipe "iptables"
    chef.add_recipe "apache2"
    chef.add_recipe "apache2::mod_php5"
    chef.add_recipe "apache2::mod_ssl"
    chef.add_recipe "mysql::server"
    chef.add_recipe "mysql::ruby"
    chef.add_recipe "php::package"
    chef.add_recipe "wp-cli"
    chef.add_recipe "wp-install"

  end

end
