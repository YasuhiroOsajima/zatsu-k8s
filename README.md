# 雑に k8s を立てて雑に k8s を使うためのファイル群

## 1. 1 台で使い始める

とりあえず 1 台、CentOS7 のサーバを用意する。  
yum が使えてメモリ 4G くらいあること。  
**この Playbook の中で firewalld が止まるので、VPS などで試す場合はセキュリティグループなどで別途対応が必要**

どこか Ansible を実行できる Linux から下記を実行。

```yaml
# vim inventory.yml
all:
  hosts:
    zatsu-k8s1:
      ansible_host: 192.168.1.1
```

これで ansible 実行すると、とりあえず k8s を立てる準備ができます。

```bash
# ansible-playbook -i inventory.yml playbook.yml --ask-pass
```

SSH で対象サーバにログインして下記を実行すると rke で 1 台構成の Kubernetes が構築されます。

```yaml
$ ssh root@x.x.x.x

# cd ~/
# wget https://github.com/rancher/rke/releases/download/v1.1.4/rke_linux-amd64
# mv rke_linux-amd64 rke
# chmod +x rke
# mv ./rke /usr/local/bin/
# vim cluster.yml
nodes:
  - address: zatsu-k8s1
    user: rancher
    role: [controlplane, worker, etcd]

dns:
  provider: coredns
  upstreamnameservers:
  - 8.8.8.8

network:
    plugin: calico

# rke up
```

kubectl を準備して使い始めます。

```bash
# mkdir ~/.kube
# cp kube_config_cluster.yml ~/.kube/config

# curl -LO https://storage.googleapis.com/kubernetes-release/release/`curl -s https://storage.googleapis.com/kubernetes-release/release/stable.txt`/bin/linux/amd64/kubectl
# chmod +x kubectl
# mv ./kubectl /usr/local/bin/

# kubectl get node
NAME         STATUS   ROLES                      AGE     VERSION
zatsu-k8s1   Ready    controlplane,etcd,worker   5m32s   v1.16.13
```
