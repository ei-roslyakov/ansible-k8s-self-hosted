# Changelog

All notable changes to this project will be documented in this file.

The format is based on [Keep a Changelog](https://keepachangelog.com/en/1.0.0/).

## [Unreleased]

## [v1.0.0] - 2026-02-27

### Added

- **`bootstrap_os` role** — prepares Debian/Ubuntu nodes for Kubernetes:
  - Disables swap permanently
  - Sets hostname and configures `/etc/hosts`
  - Loads required kernel modules (`overlay`, `br_netfilter`)
  - Applies sysctl settings for container networking
  - Installs and configures containerd as the container runtime
  - Installs `kubeadm`, `kubelet`, and `kubectl` at the version specified by `kube_version`

- **`bootstrap_cluster` role** — initialises the Kubernetes control plane:
  - Runs `kubeadm init` with configurable `pod_network_cidr`, `service_cidr`, and `cluster_name`
  - Copies kubeconfig to the admin user's home directory

- **`network_plugin` role** — deploys a pod network (CNI) plugin:
  - Flannel support (`network_plugin: flannel`, default version `v0.27.4`)

- **`join_node` role** — adds worker nodes to an existing cluster:
  - Generates a join token on the control plane
  - Runs `kubeadm join` on nodes not yet part of the cluster
  - Idempotent — skips nodes that are already joined

- **`upgrade_cluster` role** — upgrades an existing cluster:
  - Upgrades `kubeadm`, `kubelet`, and `kubectl` on all nodes
  - Applies `kubeadm upgrade apply` on the control plane
  - Applies `kubeadm upgrade node` on worker nodes

- **Playbooks**:
  - `bootstrap-cluster.yml` — full cluster provisioning (OS prep + control plane init + CNI)
  - `join-node.yml` — join one or more worker nodes
  - `upgrade-cluster.yml` — rolling cluster upgrade

- **Configuration**:
  - `group_vars/all.yml` with variables: `kube_version`, `pod_network_cidr`, `service_cidr`, `cluster_name`, `network_plugin`, `flannel_version`
  - `ansible.cfg` and `inventory.ini` are git-ignored; `.example` files provided as templates

- **Requirements**: `community.general` and `ansible.posix` Ansible collections; Python 3 on the control machine

[Unreleased]: https://github.com/yevheniirosliakov/ansible-k8s-self-hosted/compare/v1.0.0...HEAD
[1.0.0]: https://github.com/yevheniirosliakov/ansible-k8s-self-hosted/releases/tag/v1.0.0
