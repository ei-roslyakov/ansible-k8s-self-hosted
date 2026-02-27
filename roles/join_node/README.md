join_node
=========

Joins Debian/Ubuntu worker nodes to an existing Kubernetes cluster. Generates a `kubeadm` join token on the control plane, distributes the join command to worker nodes, and executes it only for nodes not already members of the cluster.

Requirements
------------

- A running Kubernetes control plane reachable from worker nodes.
- Hosts must be members of the `kube_master` or `kube_nodes` inventory groups.
- `kubectl` must be available on the control-plane host.

Role Variables
--------------

| Variable | Required | Description |
| --- | --- | --- |
| `kube_version` | yes | Kubernetes version to install on worker nodes (e.g. `1.30.0`). Must match the control plane version. |

Dependencies
------------

None.

Example Playbook
----------------

```yaml
- name: Join worker nodes to the cluster
  hosts: kube_master:kube_nodes
  become: true
  roles:
    - role: join_node
      vars:
        kube_version: "1.30.0"
```

License
-------

MIT

Author Information
------------------

admin
