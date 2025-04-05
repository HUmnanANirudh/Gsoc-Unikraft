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

### curl
- Attempted to run using `curlimages/curl` base image
- Encountered runtime errors and unresolved dependencies
- Logged build-time errors and stack traces

### htop
- Similar issues as curl, including missing runtime and library linkage
- Recorded logs for debugging and future improvements

---

## Key Learnings

- Unikraft’s binary mode requires static binaries or full rootfs
- Missing symbols trigger `weak main()` fallback in Unikraft
- Dynamic binaries must copy all required shared libraries + linkers
- Dockerfiles + `Kraftfile` combo work well for app-level rootfs
- Trial and error is essential; lots of low-level debugging involved

---

## 🔧 Tools & Environment

- **Unikraft:** v0.18 (Helene)
- **KraftKit CLI**
- **Docker**
- **QEMU** (x86_64)
- **Linux Host:** Ubuntu system