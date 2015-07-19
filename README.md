sudo systemctl stop docker

sudo docker -d --bip=172.17.42.1/16 --dns=172.17.42.1

docker-compose kill

docker-compose up -d

ansible-galaxy install -r Ansiblefile.yml --force

ansible-playbook site.yml --diff


