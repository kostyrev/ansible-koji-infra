Prerequisites:

sudo systemctl stop docker

sudo ip link set dev docker0 down

sudo ip addr del 10.0.42.1/16 dev docker0

sudo docker -d --bip=172.17.42.1/16 --dns=172.17.42.1


To start from scratch:

  docker-compose kill

  docker-compose up -d

  ansible-galaxy install -r Ansiblefile.yml --force

  ansible-playbook site.yml --diff

  docker cp ansiblekojiinfra_koji_1:/etc/pki/koji/webcerts/kojiadmin_browser_cert.p12 $HOME/
  docker cp ansiblekojiinfra_koji_1:/etc/pki/koji/koji_ca_cert.crt $HOME/

  To test inside container:

  ssh root@ansiblekojiinfra_koji_1.centos.dev.example.org

  su - kojiadmin

  koji call getLoggedInUser


To resume:

  docker-compose start skydns

  docker-compose start skydock

  sleep 20s

  docker-compose start db

  docker-compose start koji

  ansible-playbook site.yml -l koji --diff
