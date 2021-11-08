# Данная роль предназначена для раскатки кластера kubernetes в минимально рабочей и отказоустойчивой конфигурации
## Состав кластера получится следующим:
- kube-vip  - как daemonset испльзуется для создания отказоустойчивости api мастеров кластера ( там есть хитрость которая немного стоит по перек горла при полном выключении кластера)
- ingress nginx - как daemonset испльзуется для доступа к подам
- Calico - как сеть между подами
#### Раскатка ключей
```
ansible-playbook 1-distr-ssh-keys.yaml -u root -k
```
#### Добавление юзера Dockeradm  Установка Docker и python2-pip (нужен для установки плагина openshift - для применения kubernetes playbooks в роли)
```
ansible-playbook 2-install-docker.yaml
```
#### Настройка docker, файлов hosts, selinux, ipv4.ip_forward=1
```
ansible-playbook 3-prepere-k8s-install.yaml
```
#### Установка kubelet kubeadm kubectl bash-completion
```
ansible-playbook 4-install-k8s-components.yaml
```
#### Первичная Настройка Кластера кубернетес
```
ansible-playbook 5-configure-k8s-cluster.yaml --tags="all, clean-install"
```
#### Ваш кластер готов, для проверки на любом из мастеров
```
kubectl get pods -A
kubectl get nodes
kubectl get daemonsets.apps  -A
```
#### Чтобы добавить новые ноды или мастера можно использовать (и игнорировать ошибки текуших нод)
```
ansible-playbook 5-configure-k8s-cluster.yaml --tags="all, add-nodes"
```
#### Чтобы задеплоить проверочное приложение  расположенное по http://worker-ip/app1 используйте:
```
ansible-playbook 5-configure-k8s-cluster.yaml --tags='deploy-sample'
```
#### !!!!!!!ВНИМАНИЕ!!!!!!!!, в такой конфигурации если вы погасите все мастера кластера, то кластер потом не поднимится запустить его можно будет  с помощью скрипта (если рестаровать мастера по 1 то скрипт запусктать не нужно.)

```
./.kube/start-cluster-after-full-shutdown.sh &
```



#### Все эксперементы и написание роли проводились в такой среде
```
ansible [core 2.11.6]
  config file = /root/terrafrom/projects/k8s-create-role/ansible.cfg
  configured module search path = ['/root/.ansible/plugins/modules', '/usr/share/ansible/plugins/modules']
  ansible python module location = /usr/local/lib/python3.8/site-packages/ansible
  ansible collection location = /root/.ansible/collections:/usr/share/ansible/collections
  executable location = /usr/local/bin/ansible
  python version = 3.8.12 (default, Oct 18 2021, 17:18:20) [GCC 4.8.5 20150623 (Red Hat 4.8.5-44)]
  jinja version = 3.0.2
  libyaml = True
```
#### на хосте с которго катим ансиблом
```
pip3.8 install ipaddr netaddr
```

ответственности и поддержку роли не осуществляю, все представлено так как есть.
