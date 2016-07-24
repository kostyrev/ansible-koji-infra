# Ansible-koji-infra

An all in one setup for Koji build system

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
su - kojiadmin
koji moshimoshi
koji add-tag dist-centos6
koji add-tag --parent dist-centos6 --arches "x86_64" dist-centos6-build
koji add-external-repo -t dist-centos6-build dist-centos6-repo http://mirror.yandex.ru/centos/6/os/\$arch/
koji add-external-repo -t dist-centos6-build dist-epel6-repo http://mirror.yandex.ru/epel/6/\$arch/
koji add-target dist-centos6 dist-centos6-build
koji add-group dist-centos6-build build
koji add-group dist-centos6-build srpm-build
koji add-group-pkg dist-centos6-build build bash bzip2 coreutils cpio diffutils findutils gawk gcc grep sed gcc-c++ gzip info patch redhat-rpm-config rpm-build shadow-utils tar unzip util-linux-ng which make
koji add-group-pkg dist-centos6-build srpm-build bash gnupg make redhat-rpm-config rpm-build shadow-utils wget rpmdevtools
koji regen-repo dist-centos6-build
```

* Test build

```shell
yumdownloader --source nginx
koji build --scratch dist-centos6 nginx*
```

## Known issues

* CentOS 7.2 and EPEL. Currently installing koji-builder fails with error:
```shell
Error: Package: koji-builder-1.10.1-8.el7.noarch (epel)
           Requires: python2-multilib
 You could try using --skip-broken to work around the problem
 You could try running: rpm -Va --nofiles --nodigest
```
For now, ansible installs from CentOS Extras.

* CentOS 7.2 and Koji. Currently, `koji regen-repo dist-centos6-build` fails with error:
```shell
[kojiadmin@koji ~]$ koji regen-repo dist-centos6-build
Regenerating repo for tag dist-centos6-build
Watching tasks (this may be safely interrupted)...
1 newRepo (dist-centos6-build): open (koji.example.org)
TypeError: 'int' object is not iterable
```
For now use CentOS 6.8:
- Uncomment [CentOS 6.8](https://github.com/kostyrevaa/ansible-koji-infra/blob/master/Vagrantfile#L16)
- Comment out [CentOS Extras](https://github.com/kostyrevaa/ansible-koji-infra/blob/master/Vagrantfile#L27)
