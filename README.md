# GSoC 2025 – Unikraft Binary Compatibility Experiments

This repository showcases my preparation and experimentation for my Google Summer of Code 2025 proposal with **Unikraft**:  
**"Expanding the Unikraft Software Support Ecosystem"**

The focus here is on evaluating and testing Unikraft’s **binary compatibility support** by attempting to run real-world ELF binaries like Redis, curl, and htop using various techniques.

---

## Objective

To explore how existing Linux ELF binaries can be run on Unikraft unikernels without modifying their source code, using:

- Docker-based rootfs with `Kraftfile`
- Static and dynamic binary experiments
- Observing runtime behavior, library linking, and syscall issues

---
## Base Repository
I started by cloning the official Unikraft app catalog repository:

```bash
git clone https://github.com/unikraft/catalog.git
```

## Folder Structure

### `README.md`
The main readme file that explains the catalog purpose, setup instructions, and links to various examples and libraries.

### `examples/`
Sample applications in various languages showing how to build with Unikraft.

### `library/`
Pre-built and supported application packages and middleware. Each folder here contains a specific version of a software or runtime.

### `native/`
Used for native Linux testing environments or utilities needed by the build system. Sometimes contains helper tools or stubs.

### `rootfs/`
Root filesystem support for apps needing external dependencies.

### `tests/`
This folder contains deeper testing infrastructure for Unikraft:

##### `os-demo/`
A **treasure trove of low-level OS-level demos and test cases** that simulate real OS functionality or demonstrate Unikraft features

##### `synthetic/`
Synthetic and microbenchmark tests

---
## What Works (and What Didn’t)

### Hello World (C & CPP)
- A basic static hello world binary runs successfully on Unikraft
- Fails with: weak main() called Indicates Unikraft couldn't locate a valid main().

### Redis
- Attempted to run Redis binary directly.Fails with weak main() error — Unikraft fallback when no entry symbol found.
- Works with custom Dockerfile that includes:
    - Redis binary
    - Required libraries (libssl, libcrypto, ld-linux, libc)
    - Config file (redis.conf)
- Warnings shown for background jobs and fork attempts, but server runs and listens on the correct port.

---

## Key Learnings

- Unikraft’s binary mode requires static binaries or full rootfs
- Missing symbols trigger `weak main()` fallback in Unikraft
- Dynamic binaries must copy all required shared libraries + linkers
- Dockerfiles + `Kraftfile` combo work well for app-level rootfs

---

## Tools & Environment

- **Unikraft:** v0.18 (Helene)
- **KraftKit CLI**
- **Docker**
- **QEMU** (x86_64)
- **Linux Host:** Ubuntu system
