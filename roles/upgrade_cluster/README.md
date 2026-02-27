upgrade_cluster
===============

Upgrades an existing Kubernetes cluster on Debian/Ubuntu nodes. Detects the current server version, and if it is lower than the target version, upgrades `kubeadm`, `kubelet`, and `kubectl` on control-plane and worker nodes in sequence using `kubeadm upgrade apply` / `kubeadm upgrade node`.

Requirements
------------

- A running Kubernetes cluster with `kubectl` available on the control-plane host.
- Hosts must be members of the `kube_master` or `kube_nodes` inventory groups.
- `KUBECONFIG` must be readable at `/etc/kubernetes/admin.conf` on the control plane.

Role Variables
--------------

| Variable | Required | Description |
| --- | --- | --- |
| `kube_version` | yes | Target Kubernetes version (e.g. `1.31.0`). The upgrade runs only when the current server version is lower than this value. |

Dependencies
------------

None.

Example Playbook
----------------

```yaml
- name: Upgrade Kubernetes cluster
  hosts: kube_master:kube_nodes
  become: true
  roles:
    - role: upgrade_cluster
      vars:
        kube_version: "1.31.0"
```

License
-------

MIT

Author Information
------------------

admin
