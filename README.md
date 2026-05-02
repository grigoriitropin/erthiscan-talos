# erthiscan-talos

Infrastructure-as-code (IaC) configuration for the Erthiscan Kubernetes cluster, running on Talos Linux and hosted on Hetzner.

## Overview

This repository contains the declarative configuration for the Talos Linux nodes. It utilizes `sops` and `direnv` to manage encrypted secrets and configurations at rest, decrypting them directly into a `tmpfs` (`/run/user/$UID/`) memory space during active development.

## Repository Structure

- `controlplane.yaml`: Encrypted Talos machine configuration for control plane nodes.
- `worker.yaml`: Encrypted Talos machine configuration for worker nodes.
- `secret.yaml`: Encrypted environment variables and shared secrets.
- `talosconfig`: Encrypted Talos client configuration.
- `.envrc`: `direnv` configuration that handles on-the-fly decryption to memory.
- `.sops.yaml`: SOPS encryption key definitions.

## Security Model

- All sensitive files are encrypted at rest using SOPS and `age`.
- The `age` key must be available in your local `pass` store under the name `talos-key`.
- When entering the directory, `.envrc` automatically retrieves the key and decrypts the necessary configuration files to volatile memory. Standard environment variables (`TALOSCONFIG`, `KUBECONFIG`, `CONTROLPLANE_CONF`, `WORKER_CONF`) are then exported to point to these temporary, decrypted files.

## Editing Configuration

Modifications to configuration files should be performed using SOPS to maintain encryption:

```bash
sops <file>
```
