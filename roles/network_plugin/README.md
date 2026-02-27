network_plugin
==============

Installs a pod network (CNI) plugin on a Kubernetes control-plane node. Currently supports Flannel, applied via `kubectl apply` against the official upstream manifest.

Requirements
------------

- A running Kubernetes control plane.
- `kubectl` available on the control-plane host with a valid `KUBECONFIG`.
- The control-plane host must be a member of the `kube_master` inventory group.

Role Variables
--------------

| Variable | Required | Description |
| --- | --- | --- |
| `network_plugin` | yes | Name of the CNI plugin to install. Currently only `flannel` is supported; additional plugins will be added in future. |
| `flannel_version` | yes | Flannel release tag to deploy (e.g. `v0.25.1`). Used to select the upstream manifest URL. |

Dependencies
------------

None.

Example Playbook
----------------

```yaml
- name: Install pod network plugin
  hosts: kube_master
  become: true
  roles:
    - role: network_plugin
      vars:
        network_plugin: flannel
        flannel_version: "v0.25.1"
```

TODO
----

- Add support for Calico
- Add support for Cilium
- Add support for Weave Net

License
-------

MIT

Author Information
------------------

admin
