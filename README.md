# Ansible-koji-infra

An all in one setup for Koji build system

## Prereqs
./bootstrap-ansible.sh

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

* Vagrant
Task `koji-hub : Add builders` will fail the first time with error:
```shell
DatabaseError: ERROR: duplicate key value violates unique constraint "users_pkey"
```

