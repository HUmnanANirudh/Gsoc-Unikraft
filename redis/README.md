# Redis on Unikraft

This folder contains my experiments with running Redis inside a Unikraft unikernel. Both binary compatibility mode and a Docker-based rootfs approach were tested.

## Objective
- Run the Redis server on Unikraft using:
  - A precompiled or statically compiled binary (binary compatibility mode)
  - A custom root filesystem built using Docker
- Understand the requirements and limitations of Unikraft for running complex ELF binaries like Redis

## What Worked

### Docker-based RootFS
Using a custom Dockerfile to build a minimal root filesystem containing:
- The Redis binary
- All required libraries: libssl, libcrypto, libc, ld-linux
- redis.conf configuration file

This method successfully launched Redis on Unikraft:
- Redis bound to 0.0.0.0 and listened on port 6379
- All major functionality worked

However, Unikraft logs showed warnings related to:
- Background job support: Redis attempts to fork() for background processes, which Unikraft does not fully support yet.

These warnings did not prevent Redis from starting or serving requests.

## What Didn't Work

### Binary Compatibility Mode
**Approach 1: Cloning and Building Redis from Source**

Cloned Redis from the official GitHub repo:
```bash
git clone https://github.com/redis/redis.git
cd redis
```

Attempted to statically build Redis using make with musl:
```bash
make CC=musl-gcc MALLOC=libc
```

Output binary appeared valid, but failed when run inside Unikraft using binary mode in the Kraftfile.

Unikraft printed the following error:
```
weak main() called
```

**Why It Failed:**
- Unikraft could not detect the correct entry point from the ELF binary.
- Redis uses custom entry points and internal initialization routines, which Unikraft doesn't resolve in binary compatibility mode unless properly symbol-mapped.
- Static linking removed some symbols needed at runtime, but dynamic linking failed due to missing dependencies.

## Key Learnings
- Even statically compiled binaries can hit `weak main()` errors if entry points or init routines don't align with Unikraft's expectations.
- Logging output is critical—Unikraft provides early hints like "weak main" or "missing syscall" that indicate where things fail.
## Screenshots
- Docker-based RootFS

![Screenshot 2025-04-05 165455](https://github.com/user-attachments/assets/a553224f-eb2f-42bd-9c02-87119763148e)

- Binary Compatibility Mode

<img width="548" alt="image" src="https://github.com/user-attachments/assets/f55c5ca8-2d0f-4fa4-b91b-1171712ecb27" />
