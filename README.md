# wordpress-very-clean-theme

## If you use Vagrant

This is my Vagrant file, it uses the gbarbieru / xenial virtual machine.
```
boxes = [
    {
        :name => "develop",
        :eth1 => "192.168.118.30",
        :mem => "512",
        :cpu => "1",
        :local_folder => "code",
        :remote_folder => "/var/www/html"
    },
]

VAGRANTFILE_API_VERSION = "2"

Vagrant.configure(VAGRANTFILE_API_VERSION) do |config|

  config.vm.box = "gbarbieru/xenial"

  config.vm.provider "virtualbox" do |v, override|
    override.vm.box = "gbarbieru/xenial"
  end

  boxes.each do |opts|
    config.vm.define opts[:name] do |config|
      config.vm.hostname = opts[:name]
      config.vm.synced_folder opts[:local_folder], opts[:remote_folder], group: "www-data", mount_options: ['dmode=777','fmode=777']
      config.vm.synced_folder "~", "/vagrant", owner: "vagrant", group: "vagrant"

      config.vm.provider "virtualbox" do |v|
        v.customize ["modifyvm", :id, "--memory", opts[:mem]]
        v.customize ["modifyvm", :id, "--cpus", opts[:cpu]]
      end

      config.vm.network :private_network, ip: opts[:eth1]
      config.vm.provision :shell, path: "setup.sh"
    end
  end
end
```
