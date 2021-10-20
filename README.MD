ansible [core 2.11.6]
  config file = /root/terrafrom/projects/k8s-create-role/ansible.cfg
  configured module search path = ['/root/.ansible/plugins/modules', '/usr/share/ansible/plugins/modules']
  ansible python module location = /usr/local/lib/python3.8/site-packages/ansible
  ansible collection location = /root/.ansible/collections:/usr/share/ansible/collections
  executable location = /usr/local/bin/ansible
  python version = 3.8.12 (default, Oct 18 2021, 17:18:20) [GCC 4.8.5 20150623 (Red Hat 4.8.5-44)]
  jinja version = 3.0.2
  libyaml = True


# на хосте с которго катим ансиблом
pip3.8 install ipaddr netaddr


# Раскатка ключей
ansible-playbook 1-distr-ssh-keys.yaml -u root -k
# Добавление юзера Dockeradm  Установка Docker и python2-pip (нужен для установки плагина openshift - для применения kubernetes playbooks в роли)
ansible-playbook 2-install-docker.yaml
# Настройка docker, файлов hosts, selinux, ipv4.ip_forward=1
ansible-playbook 3-prepere-k8s-install.yaml
# Установка kubelet kubeadm kubectl
ansible-playbook 4-install-k8s-components.yaml
# Настройка Кластера кубернетес
ansible-playbook 5-configure-k8s-cluster.yaml --tags="all, clean-install"
