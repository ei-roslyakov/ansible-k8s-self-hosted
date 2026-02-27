bootstrap_os
============

Prepares Debian/Ubuntu nodes for a Kubernetes cluster. Handles OS-level prerequisites: disables swap, sets the hostname, configures `/etc/hosts`, loads required kernel modules, applies sysctl settings, installs and configures containerd, and installs `kubeadm`, `kubelet`, and `kubectl` at a pinned version.

Requirements
------------

- Debian/Ubuntu target hosts with `apt`.
- Hosts must be members of either the `kube_master` or `kube_nodes` inventory groups.
- Collections: `community.general` (for `modprobe`) and `ansible.posix` (for `sysctl`).

Role Variables
--------------

| Variable | Required | Description |
| --- | --- | --- |
| `kube_version` | yes | Kubernetes version to install (e.g. `1.30.0`). Used to select the apt repository and pin package versions. |

Dependencies
------------

None.

Example Playbook
----------------

```yaml
- name: Bootstrap OS for Kubernetes
  hosts: kube_master:kube_nodes
  become: true
  roles:
    - role: bootstrap_os
      vars:
        kube_version: "1.30.0"
```

License
-------

MIT

Author Information
------------------

admin
