# 雑に k8s を立てて雑に k8s を使うためのファイル群

## 1. 1 台で使い始める

とりあえず 1 台、CentOS7 のサーバを用意する。  
yum が使えてメモリ 4G くらいあること。  
**この Playbook の中で firewalld が止まるので、VPS などで試す場合はセキュリティグループなどで別途対応が必要**

対象のサーバで下記を実行。

Ansible インストール。

```bash
# yum install ansible
```

対象サーバの IP アドレスに更新。

```yaml
# vim inventory.yml
all:
  hosts:
    zatsu-k8s1:
      ansible_host: 192.168.1.1
```

これで ansible 実行すると、とりあえず k8s を立てる準備ができます。

```bash
# ansible-playbook -i inventory.yml playbook/prepare_node.yml --ask-pass
再起動がかかる

# ansible-playbook -i inventory.yml playbook/install_docker.yml --ask-pass
```

下記の Playbook を実行すると rke で 1 台構成の Kubernetes が構築されます。

```bash
# ansible-playbook -i inventory.yml playbook/exec_rke.yml --ask-pass
```

kubectl を準備して使い始めます。

```bash
# ansible-playbook -i inventory.yml playbook/install_kubectl.yml --ask-pass

# kubectl get node
NAME         STATUS   ROLES                      AGE     VERSION
zatsu-k8s1   Ready    controlplane,etcd,worker   5m32s   v1.16.13
```
