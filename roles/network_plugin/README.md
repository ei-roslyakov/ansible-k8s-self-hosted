network_plugin
==============

Installs a pod network (CNI) plugin on a Kubernetes control-plane node. Supports:

- **Flannel** — applied via `kubectl apply` against the official upstream manifest, and upgraded when `flannel_version` is newer than the installed one.
- **Cilium** — installed with Helm (Helm itself is installed on the control-plane node if missing). This is a **bootstrap-only** install: if a `cilium` DaemonSet already exists the role does nothing, so upgrades and config changes belong to whatever manages Cilium afterwards (e.g. an Argo CD Application). Feed that same chart version and values into `cilium_version` / `cilium_values` so the first sync doesn't reconfigure the CNI.

Requirements
------------

- A running Kubernetes control plane.
- `kubectl` available on the control-plane host with a valid `KUBECONFIG`.
- The control-plane host must be a member of the `kube_master` inventory group.

Role Variables
--------------

| Variable | Required | Description |
| --- | --- | --- |
| `network_plugin` | yes | CNI plugin to install: `flannel` or `cilium`. Any other value fails the play. |
| `flannel_version` | flannel | Flannel release tag to deploy (e.g. `v0.25.1`). Used to select the upstream manifest URL. |
| `cilium_version` | cilium | Cilium Helm chart version (default `1.20.3`). |
| `cilium_values` | cilium | Helm values dict for the bootstrap install (default: Kubernetes IPAM, VXLAN tunnel, kube-proxy kept, exclusive CNI config). |
| `helm_version` | cilium | Helm release installed on the control-plane node when missing (default `v4.3.1`). |

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

Cilium (bootstrap install; later changes belong to whatever manages Cilium afterwards):

```yaml
- name: Install pod network plugin
  hosts: kube_master
  become: true
  roles:
    - role: network_plugin
      vars:
        network_plugin: cilium
        cilium_version: "1.20.3"
        cilium_values:
          ipam:
            mode: kubernetes
          routingMode: tunnel
          tunnelProtocol: vxlan
```

TODO
----

- Add support for Calico
- Add support for Weave Net

License
-------

MIT

Author Information
------------------

admin
