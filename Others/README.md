# Curl and htop on Unikraft

This folder contains my experiments attempting to run two widely used Linux utilities — **curl** and **htop** — on Unikraft unikernels. These tests were conducted to assess Unikraft's **binary compatibility** and **Docker-based rootfs** support for dynamic user-space applications.

## Objective

- Test Unikraft’s ability to run **dynamically linked** and **manually extracted** user binaries like `curl` and `htop`
- Explore different rootfs setups using Docker and `kraft`
- Investigate runtime failures like `weak main()`, broken syscalls, and missing symbols

---

## htop on Unikraft

### Attempts

- Tried static and dynamic binaries using Alpine and Ubuntu base images
- Built rootfs with manually copied binaries and libraries (e.g. `libtinfo.so`, `libncursesw.so`)
- Kraftfile specified `cmd: ["/usr/bin/htop"]`

### Issues Faced

- **Kraft errors**:  
  - `lib/libc:newlib` or `libc:musl` not found
  - Could not locate `lib/htop:stable` from Unikraft package index

- **Runtime errors**:  
  - `weak main() called. Symbol was not replaced!`
  - Memory region errors from `libukallocbbuddy`
  - App would not start inside the unikernel, likely due to unresolved entrypoint or missing initialization symbols

### Learnings

- `htop` relies heavily on `/proc`, terminal I/O, and system APIs not yet fully supported in Unikraft
- Even with complete library copying, dynamic resolution failed at runtime
- Static compilation with `musl` also failed — entry point (`main`) not detected correctly
- Docker rootfs is useful but doesn't fix missing symbols or custom init logic

---

## curl on Unikraft

### Attempts

- Used the official `curlimages/curl:latest` Docker image
- Created rootfs by copying:
  - `/usr/bin/curl`
  - `/lib`, `/lib64`, `/usr/lib`, `/etc/ssl`
- Set command: `cmd: ["/usr/bin/curl", "https://example.com"]`

### Issues Faced

- Docker error: `COPY --from=build /lib64/ /lib64/` → directory not found
- Later resolved by manually creating and exporting a `rootfs` from container
- **kraft run** booted successfully but ended with:
  - `weak main() called. Symbol was not replaced!`

### Learnings

- Despite successful build, binary mode in Unikraft didn’t recognize `main` symbol from `curl`
- Rootfs was built and detected, but ELF loader failed to locate proper entry point
- Shared libraries and SSL config were correctly copied, but dynamic linking still failed
- Unikraft ELF support still maturing for complex CLI tools

---

## General Takeaways

- `weak main()` error occurs when Unikraft cannot identify a valid application entry point
- Even **statically compiled** binaries can fail if:
  - They use custom init functions
  - They rely on dynamic syscalls or libc behavior
- Unikraft’s **binary compatibility** layer is evolving — best suited for minimal, self-contained binaries
- Docker rootfs helps with complex dependency trees, but not enough if ELF isn't correctly linked or entry point is non-standard

---

