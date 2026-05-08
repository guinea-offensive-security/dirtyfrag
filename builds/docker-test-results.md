# Docker Build Test Results

Date: 2026-05-08

Scope:

- Images tested: `debian:11`, `debian:12`, `ubuntu:20.04`, `ubuntu:22.04`, `ubuntu:24.04`
- Builds tested:
  - `exp-x86_64-glibc-dynamic`
  - `exp-x86_64-glibc-static`

Safety note:

The exploit binary was not executed. These checks validate binary compatibility
only: architecture, dynamic loader verification, and `ldd` dependency
resolution. Running the exploit in Docker still targets the host kernel.

## Summary

| Image | OS | Dynamic | Static |
| --- | --- | --- | --- |
| `debian:11` | Debian GNU/Linux 11 (bullseye) | FAIL: missing glibc symbol versions `GLIBC_2.33`, `GLIBC_2.34`, `GLIBC_2.38` | PASS |
| `debian:12` | Debian GNU/Linux 12 (bookworm) | FAIL: missing `GLIBC_2.38` | PASS |
| `ubuntu:20.04` | Ubuntu 20.04.6 LTS | FAIL: missing glibc symbol versions `GLIBC_2.33`, `GLIBC_2.34`, `GLIBC_2.38` | PASS |
| `ubuntu:22.04` | Ubuntu 22.04.5 LTS | FAIL: missing `GLIBC_2.38` | PASS |
| `ubuntu:24.04` | Ubuntu 24.04.4 LTS | PASS | PASS |

## Result Details

### Debian 11

```text
OS=Debian GNU/Linux 11 (bullseye)
ARCH=x86_64

exp-x86_64-glibc-dynamic:
RESULT=FAIL_DYNAMIC_DEPS
Missing versions: GLIBC_2.33, GLIBC_2.34, GLIBC_2.38

exp-x86_64-glibc-static:
RESULT=PASS_STATIC
ldd: not a dynamic executable
```

### Debian 12

```text
OS=Debian GNU/Linux 12 (bookworm)
ARCH=x86_64

exp-x86_64-glibc-dynamic:
RESULT=FAIL_DYNAMIC_DEPS
Missing version: GLIBC_2.38

exp-x86_64-glibc-static:
RESULT=PASS_STATIC
ldd: not a dynamic executable
```

### Ubuntu 20.04

```text
OS=Ubuntu 20.04.6 LTS
ARCH=x86_64

exp-x86_64-glibc-dynamic:
RESULT=FAIL_DYNAMIC_DEPS
Missing versions: GLIBC_2.33, GLIBC_2.34, GLIBC_2.38

exp-x86_64-glibc-static:
RESULT=PASS_STATIC
ldd: not a dynamic executable
```

### Ubuntu 22.04

```text
OS=Ubuntu 22.04.5 LTS
ARCH=x86_64

exp-x86_64-glibc-dynamic:
RESULT=FAIL_DYNAMIC_DEPS
Missing version: GLIBC_2.38

exp-x86_64-glibc-static:
RESULT=PASS_STATIC
ldd: not a dynamic executable
```

### Ubuntu 24.04

```text
OS=Ubuntu 24.04.4 LTS
ARCH=x86_64

exp-x86_64-glibc-dynamic:
RESULT=PASS_DYNAMIC_DEPS

exp-x86_64-glibc-static:
RESULT=PASS_STATIC
ldd: not a dynamic executable
```

## Recommendation

Use `exp-x86_64-glibc-static` by default for x86_64 Debian/Ubuntu targets.
Use `exp-x86_64-glibc-dynamic` only on systems with glibc new enough to satisfy
the required symbol versions; in this test set, only `ubuntu:24.04` passed.
