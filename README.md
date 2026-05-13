# erthiscan-talos

Infrastructure-as-code (IaC) configuration for the Erthiscan Kubernetes cluster, running on Talos Linux and hosted on Hetzner.

## Overview

Follows the [reproducible machine configuration](https://docs.siderolabs.com/talos/v1.13/configure-your-talos-cluster/system-configuration/reproducible-machine-configuration.md) pattern — patches instead of full configs, SOPS for secrets, direnv for decryption to tmpfs.

## Repository Structure

| File | Purpose |
|------|---------|
| `secrets.yaml` | Encrypted Talos secrets bundle (PKI, tokens). Generated via `talosctl gen secrets`. |
| `env.yaml` | Encrypted environment variables (`CONTROL_PLANE_IP`, `CLUSTER_NAME`). |
| `talosconfig` | Encrypted Talos client configuration. |
| `patches/` | Strategic merge patches with only our customizations (network, certSANs, Hetzner installer). |
| `.envrc` | `direnv` — decrypts to tmpfs, exports `TALOSCONFIG`, `KUBECONFIG`, `PATCH_CP`, `PATCH_WK`. |
| `.sops.yaml` | SOPS encryption key. |

## Usage

### Connect

```bash
cd hetzner-talos   # direnv decrypts everything to tmpfs
kubectl get nodes
```

### Upgrade Talos

```bash
talosctl upgrade -n $CONTROL_PLANE_IP --image factory.talos.dev/hcloud-installer/<schematic>:v<version>
```

### Upgrade Kubernetes

```bash
talosctl upgrade-k8s --to <version>
```

### Edit encrypted files

```bash
sops secrets.yaml
sops env.yaml
sops patches/controlplane.yaml
sops patches/worker.yaml
sops talosconfig
```

## Security

- All sensitive files encrypted at rest with SOPS + age.
- `age` key in `pass` store: `pass show talos-key`.
- `direnv` decrypts to `tmpfs` — plaintext never touches disk.
