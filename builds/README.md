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
