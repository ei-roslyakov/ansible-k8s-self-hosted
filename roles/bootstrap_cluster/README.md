bootstrap_cluster
=================

Bootstraps a Kubernetes cluster on Debian/Ubuntu hosts. Adds the official Kubernetes apt repository, installs and holds `kubeadm`, `kubelet`, and `kubectl` at a specified version, and initialises the control plane with `kubeadm init`.

Requirements
------------

- Debian/Ubuntu target hosts with `apt`.
- `kubeadm`, `kubelet`, and `kubectl` must not conflict with existing installations.
- The control-plane host must be a member of the `kube_master` inventory group.

Role Variables
--------------

| Variable | Required | Description |
| --- | --- | --- |
| `kube_version` | yes | Kubernetes version to install (e.g. `1.30.0`). Used to pin the apt repository and package versions. |
| `pod_network_cidr` | yes | CIDR range for the pod network (e.g. `10.244.0.0/16`). |
| `service_cidr` | yes | CIDR range for Kubernetes services (e.g. `10.96.0.0/12`). |
| `cluster_name` | yes | Control-plane endpoint hostname or IP passed to `--control-plane-endpoint`. |
| `ansible_user` | yes | OS user whose `~/.kube/config` will be populated after init. |

Dependencies
------------

None.

Example Playbook
----------------

```yaml
- name: Bootstrap Kubernetes cluster
  hosts: kube_master
  become: true
  roles:
    - role: bootstrap_cluster
      vars:
        kube_version: "1.30.0"
        pod_network_cidr: "10.244.0.0/16"
        service_cidr: "10.96.0.0/12"
        cluster_name: "k8s-control-plane.example.com"
```

License
-------

MIT

Author Information
------------------

admin
