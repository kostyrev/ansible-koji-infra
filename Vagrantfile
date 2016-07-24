# -*- mode: ruby -*-
# vi: set ft=ruby :

# All Vagrant configuration is done below. The "2" in Vagrant.configure
# configures the configuration version (we support older styles for
# backwards compatibility). Please don't change it unless you know what
# you're doing.
Vagrant.configure(2) do |config|

  config.vm.box = "bento/centos-6.8"
  config.vm.box_check_update = false

  config.vm.define "koji.example.org" do |koji|
    koji.vm.network "public_network"
    koji.vm.host_name = 'koji.example.org'
    koji.vm.provision "ansible" do |ansible|
      ansible.sudo = true
      ansible.raw_arguments = ["--diff"]
#      ansible.raw_arguments = ["-vvvv"]
      ansible.groups = {
        "koji_db" => ["koji.example.org"],
        "koji_ca" => ["koji.example.org"],
        "koji_hub" => ["koji.example.org"],
        "koji_web" => ["koji.example.org"],
        "koji_builder" => ["koji.example.org"]
      }
      ansible.playbook = "site.yml"
    end
  end
end
