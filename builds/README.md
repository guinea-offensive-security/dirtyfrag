# DirtyFrag Builds

Built on: 2026-05-08

Source: `exp.c`

Compiler:

```text
gcc (Debian 15.2.0-16) 15.2.0
```

Host arch:

```text
x86_64
```

## Artifacts

### `exp-x86_64-glibc-dynamic`

Build command:

```bash
gcc -O0 -Wall -o builds/exp-x86_64-glibc-dynamic exp.c -lutil
```

File:

```text
ELF 64-bit LSB pie executable, x86-64, dynamically linked, interpreter /lib64/ld-linux-x86-64.so.2, for GNU/Linux 3.2.0, not stripped
```

SHA256:

```text
5f7df650e14ddbf8a58a48cf835ef4f722ef1072240ac82063e3220e743f15b4
```

### `exp-x86_64-glibc-static`

Build command:

```bash
gcc -O0 -Wall -static -o builds/exp-x86_64-glibc-static exp.c -lutil
```

File:

```text
ELF 64-bit LSB executable, x86-64, statically linked, for GNU/Linux 3.2.0, not stripped
```

SHA256:

```text
bd19acab9fc0be3cd144aa591a6d139f4c9b5f8711393697fff0480914fc7dec
```

## Missing Toolchains

The following compilers were not installed on the build host:

```text
musl-gcc
aarch64-linux-gnu-gcc
arm-linux-gnueabihf-gcc
x86_64-linux-musl-gcc
```

Only x86_64 glibc builds were produced in this pass.

## Docker Compatibility Check

The exploit binary was not executed during these Docker checks. The checks only
validated architecture, dynamic loader compatibility, and shared-library
resolution. Running the exploit inside Docker would still exercise the host
kernel, not an isolated container kernel.

| Image | Arch | Dynamic build | Static build |
| --- | --- | --- | --- |
| `debian:11` | `x86_64` | Fails: requires newer glibc symbols including `GLIBC_2.33`, `GLIBC_2.34`, `GLIBC_2.38` | OK: not a dynamic executable |
| `debian:12` | `x86_64` | Fails: requires `GLIBC_2.38` | OK: not a dynamic executable |
| `ubuntu:20.04` | `x86_64` | Fails: requires newer glibc symbols including `GLIBC_2.33`, `GLIBC_2.34`, `GLIBC_2.38` | OK: not a dynamic executable |
| `ubuntu:22.04` | `x86_64` | Fails: requires `GLIBC_2.38` | OK: not a dynamic executable |
| `ubuntu:24.04` | `x86_64` | OK: resolves with container glibc | OK: not a dynamic executable |

Recommendation:

- Use `exp-x86_64-glibc-static` for broad x86_64 Debian/Ubuntu targets.
- Use `exp-x86_64-glibc-dynamic` only on systems with a new enough glibc, verified on `ubuntu:24.04`.
