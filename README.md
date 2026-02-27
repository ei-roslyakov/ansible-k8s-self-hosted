# Kubernetes Cluster Ansible

Ansible playbooks and roles for provisioning, extending, and upgrading a self-managed Kubernetes cluster on Debian/Ubuntu nodes.

## Roles

| Role | Description |
| --- | --- |
| `bootstrap_os` | OS prerequisites — disables swap, sets hostname, configures `/etc/hosts`, loads kernel modules, applies sysctl settings, installs containerd, and installs `kubeadm`/`kubelet`/`kubectl`. |
| `bootstrap_cluster` | Initialises the control plane with `kubeadm init` and copies the kubeconfig to the admin user's home directory. |
| `join_node` | Generates a join token on the control plane and runs `kubeadm join` on worker nodes that are not yet members of the cluster. |
| `network_plugin` | Installs a pod network (CNI) plugin. Currently supports Flannel. |
| `upgrade_cluster` | Upgrades `kubeadm`, `kubelet`, and `kubectl` on all nodes and applies `kubeadm upgrade apply` / `kubeadm upgrade node`. |

## Requirements

- Debian/Ubuntu target hosts reachable via SSH.
- Ansible collections: `community.general`, `ansible.posix`.
- Python 3 on the control machine.

### Set up a virtual environment

```sh
python3 -m venv .venv
source .venv/bin/activate
pip install -r requirements.txt
ansible-galaxy collection install community.general ansible.posix
```

## Configuration

### Inventory

Copy the example and fill in your host IPs:

```sh
cp inventory.ini.example inventory.ini
```

```ini
[all:vars]
ansible_user=ubuntu
ansible_ssh_private_key_file=~/.ssh/id_ed25519
ansible_python_interpreter=/usr/bin/python3

[kube_master]
infra-k8s-001-master-001 ansible_host=192.168.1.100

[kube_nodes]
infra-k8s-001-node-001 ansible_host=192.168.1.110
infra-k8s-001-node-002 ansible_host=192.168.1.111
infra-k8s-001-node-003 ansible_host=192.168.1.112
```

### Variables

Copy the example and adjust to your needs:

```sh
cp group_vars/all.yml.example group_vars/all.yml
```

| Variable | Default | Description |
| --- | --- | --- |
| `kube_version` | `1.35.0` | Kubernetes version to install / upgrade to. |
| `pod_network_cidr` | `10.244.0.0/16` | CIDR for the pod network. |
| `service_cidr` | `10.96.0.0/12` | CIDR for Kubernetes services. |
| `cluster_name` | `k8s-cluster` | Control-plane endpoint hostname. |
| `network_plugin` | `flannel` | CNI plugin to deploy. |
| `flannel_version` | `v0.27.4` | Flannel release tag. |

> `ansible.cfg` and `inventory.ini` are git-ignored. Use the `.example` files as templates.

## Usage

### 1. Bootstrap a new cluster

Provisions OS prerequisites, initialises the control plane, installs the network plugin, and joins worker nodes:

```sh
ansible-playbook -i inventory.ini bootstrap-cluster.yml
ansible-playbook -i inventory.ini join-node.yml
```

### 2. Add worker nodes later

```sh
ansible-playbook -i inventory.ini join-node.yml
```

### 3. Upgrade the cluster

```sh
ansible-playbook -i inventory.ini upgrade-cluster.yml -e "kube_version=1.33.3"
```

## Post-provisioning

### Copy kubeconfig to your local machine

```sh
scp ubuntu@<KUBE_MASTER_IP>:/home/ubuntu/.kube/config ~/.kube/config
```

### Add the cluster endpoint to your local `/etc/hosts`

```
<KUBE_MASTER_IP>  {{ cluster_name }}
```
