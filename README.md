# Ansible-koji-infra

An all-in-one setup for Koji build system

## Prereqs
`./bootstrap-ansible.sh`

## Using docker
Please take a look at [Koji Dojo](https://github.com/release-engineering/koji-dojo)

## Using vagrant

```shell
vagrant up
vagrant provision
```

## Post ansible tasks

* Basic Koji configuration

```shell
sudo su - kojiadmin
koji moshimoshi
koji add-tag dist-centos6
koji add-tag --parent dist-centos6 --arches "x86_64" dist-centos6-build
koji add-external-repo -t dist-centos6-build dist-centos6-repo http://mirror.yandex.ru/centos/6/os/\$arch/
koji add-external-repo -t dist-centos6-build dist-epel6-repo http://mirror.yandex.ru/epel/6/\$arch/
koji add-target dist-centos6 dist-centos6-build
koji add-group dist-centos6-build build
koji add-group dist-centos6-build srpm-build
koji add-group-pkg dist-centos6-build build centos-release bash bzip2 coreutils cpio diffutils findutils gawk gcc grep sed gcc-c++ gzip info patch redhat-rpm-config rpm-build shadow-utils tar unzip util-linux-ng which make
koji add-group-pkg dist-centos6-build srpm-build bash gnupg make redhat-rpm-config rpm-build shadow-utils wget rpmdevtools
koji regen-repo dist-centos6-build
```

* Test build

```shell
wget http://mirror.yandex.ru/epel/6/SRPMS/nginx-1.0.15-12.el6.src.rpm
koji build --scratch dist-centos6 nginx*
```

## Koji Web
You can reach Koji web UI at http://127.0.0.1:8080/koji

## How to incorporate this repo into your ansible setup
- cd to your ansible repo path
- add koji-infra to your .gitignore
- `git clone https://github.com/kostyrevaa/ansible-koji-infra.git koji-infra`

My ansible layout has all the group_vars, host_vars and inventory files under inventories dir:
```
inventories/
inventories/group_vars
inventories/host_vars
```
[Ansible docs](http://docs.ansible.com/ansible/playbooks_variables.html#variable-precedence-where-should-i-put-a-variable) states that starting with Ansible 2.0 there can be multiple group_vars and host_vars:  
**inventory** `group_vars` and `host_vars` override **playbook** `group_vars` and `host_vars.`  

So if need be override koji defaults that come with this repo's `host_vars` and `group_vars` in your **inventory** `host_vars` and `group_vars.`  
Test overrides with  
`ansible-playbook -i inventories/infra -l koji.infra.example.org koji-infra/site_koji.yml --diff --check`
