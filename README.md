Prerequisites:

sudo systemctl stop docker

sudo ip link set dev docker0 down

sudo ip addr del 10.0.42.1/16 dev docker0

sudo docker -d --bip=172.17.42.1/16 --dns=172.17.42.1

If you use Vagrant instead of Docker:

  vagrant up
  vagrant provision

To start from scratch:

  docker-compose kill

  docker-compose rm -f

  docker-compose up -d

  ansible-galaxy install -r Ansiblefile.yml --force

  ansible-playbook site.yml --diff

  rm -f ${HOME}/kojiadmin_browser_cert.p12
  rm -f ${HOME}/koji_ca_cert.crt

  docker cp ansiblekojiinfra_koji_1:/etc/pki/koji/webcerts/kojiadmin_browser_cert.p12 ${HOME}/
  docker cp ansiblekojiinfra_koji_1:/etc/pki/koji/koji_ca_cert.crt ${HOME}/

  To test inside container:

  ssh root@ansiblekojiinfra_koji_1.centos.dev.example.org

  su kojiadmin -c "koji call getLoggedInUser"

  Import kojiadmin_browser_cert.p12 and koji_ca_cert.crt to your browser and login to http://ansiblekojiinfra_koji_1.centos.dev.example.org/koji


To resume debugging:

  docker-compose start skydns

  docker-compose start skydock

  sleep 20s

  docker-compose start db

  docker-compose start koji

  ansible-playbook site.yml --diff


Post ansible test configuration

Kojid (Koji-Builder) configuration

  su kojiadmin -c "koji add-host-to-channel ansiblekojiinfra_koji_1.centos.dev.example.org createrepo"

Kojira configuration

  su kojiadmin -c "koji grant-permission repo kojira"


Koji RPM Build System Configuration

su - kojiadmin
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

Test build
yumdownloader --source nginx
koji build --scratch dist-centos6 nginx*
