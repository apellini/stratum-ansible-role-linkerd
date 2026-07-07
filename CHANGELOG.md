# Changelog

## [0.1.1] - 2026-07-07

### Fixed

- Add explicit `PATH: /usr/local/bin:/usr/local/sbin:/usr/bin:/usr/sbin:/bin:/sbin` to
  the `environment:` block on all three shell/command tasks ("Apply Linkerd CRDs",
  "Apply Linkerd control plane", "Wait for Linkerd control plane health check").
  `sudo` resets `PATH` to a minimal safe value via `env_reset` + `secure_path`,
  stripping `/usr/local/bin` where both `linkerd` and `kubectl` live, causing
  `rc=127` ("command not found") on every task that invoked those binaries.

## [0.1.0] - 2026-07-03

### Added

- Initial release: download Linkerd CLI to `/usr/local/bin/linkerd`, apply CRDs and
  control plane via kubectl, wait for `linkerd check`, annotate namespaces for
  automatic mTLS injection (D-INFRA-19, D-ENV-13).
