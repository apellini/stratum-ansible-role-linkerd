# stratum-ansible-role-linkerd

Ansible role that installs the [Linkerd](https://linkerd.io/) service mesh at a pinned version,
applies the control plane, and annotates STRATUM Kubernetes namespaces for automatic mTLS injection.
Part of the STRATUM dev-environment bootstrap (Phase 2, D-INFRA-19, D-ENV-13).

Repo type: **public factory role** | Pattern: `apellini/stratum-ansible-role-*`
Released via **git tag** (`v0.1.0`, ...) -- never by branch.

---

## Requirements

- Target OS: Ubuntu 22.04 (Jammy) or 24.04 (Noble)
- Ansible controller: `ansible-core >= 2.16`
- Collections: `kubernetes.core` (install: `ansible-galaxy collection install kubernetes.core`)
- Target host prerequisites: k3s installed (run `stratum-ansible-role-k3s` first); internet access

---

## Role Variables

All variables are **required**. Declare them in the wrapper `group_vars/k3s.yml`.

| Variable | Type | Required | Description | Example |
|----------|------|----------|-------------|---------|
| `linkerd_version` | `str` | yes | Linkerd release tag. Used to download the CLI binary. | `stable-2.14.10` |
| `linkerd_inject_namespaces` | `list[str]` | yes | Namespaces to annotate for automatic mTLS injection. | see below |

```yaml
linkerd_inject_namespaces:
  - stratum-api
  - stratum-control
  - stratum-data
  - stratum-coll
  - stratum-inference
  - stratum-ops
```

---

## Tasks (in order)

1. Download the Linkerd CLI binary to `/usr/local/bin/linkerd` (idempotent -- skipped if already present).
2. Apply Linkerd CRDs (`linkerd install --crds | kubectl apply -f -`).
3. Apply Linkerd control plane (`linkerd install | kubectl apply -f -`).
4. Wait for `linkerd check` to pass (retries every 15 s, max 12 attempts = 3 min).
5. Annotate each namespace in `linkerd_inject_namespaces` with `linkerd.io/inject=enabled`.

---

## Example Playbook

```yaml
- name: Install Linkerd on the STRATUM k3s node
  hosts: k3s
  become: true
  roles:
    - role: stratum_factory.linkerd
```

Wrapper `group_vars/k3s.yml`:

```yaml
linkerd_version: "stable-2.14.10"
linkerd_inject_namespaces:
  - stratum-api
  - stratum-control
  - stratum-data
  - stratum-coll
  - stratum-inference
  - stratum-ops
```

---

## License

MIT
