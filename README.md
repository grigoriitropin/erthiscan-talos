# erthiscan-talos

Talos Linux configuration for the Erthiscan Kubernetes cluster on Hetzner.

## Approach

Machine configs are **not stored** — they drift after every `upgrade-k8s` or `upgrade`. Instead, only **patches** with our customizations are kept. Full configs are regenerated on demand via `talosctl gen config`.

## Structure

| Path | What |
|------|------|
| `patches/` | Strategic merge patches — factory installer, certSANs, network, endpoint |
| `secrets.yaml` | Talos PKI bundle — cluster CA, etcd CA, tokens |
| `env.yaml` | `CONTROL_PLANE_IP` and `CLUSTER_NAME` |
| `talosconfig` | Encrypted client config for `talosctl` |
| `.envrc` | Decrypts everything to tmpfs, sets `TALOSCONFIG`, `KUBECONFIG`, `PATCH_CP`, `PATCH_WK` |

## Security

SOPS + age. Encryption key lives in `pass` under `talos-key`. Plaintext only exists in tmpfs — never on disk.