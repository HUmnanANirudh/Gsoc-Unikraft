# Hello World (C & C++) with Unikraft

This folder demonstrates my attempts to run a simple **Hello World** application written in both **C** and **C++** on Unikraft. The goal was to validate Unikraft's ability to handle static binaries in a minimal environment, using both **native static linking** and **binary compatibility mode**.

---

## Objective

- Test the execution of static C and C++ binaries on Unikraft
- Ensure that basic static linking and execution work as expected without relying on external libraries
- Understand the behavior of Unikraft with static binaries and the `weak main()` error

---

## What Worked

### C Hello World (Static Binary)
- Successfully compiled a static C binary and executed it on Unikraft
- The program printed "Bye, World!" successfully

### C++ Hello World (Static Binary)
- Similarly, the C++ version also worked once compiled as a static binary

---

## What Didn’t Work

### Binary Compatibility Mode (weak main() error)
- The attempt to run the static binary in binary compatibility mode resulted in the error:
    - `weak main() called`
- This indicates that Unikraft couldn't locate a valid entry symbol in the ELF binary
- The issue was traced to the way symbols were handled in the binary compatibility mode, which could not resolve `main()` correctly in dynamically linked binaries

---

## Key Learnings

- **Static binaries** work fine on Unikraft, provided all dependencies are statically linked.
- **Binary compatibility mode** requires special attention to symbols; missing symbols (like `main()`) result in errors.
- Unikraft’s binary mode is less reliable for dynamically linked binaries unless full root filesystems or static binaries are used.
- The `weak main()` error is indicative of Unikraft's inability to resolve the entry point in a dynamic binary.

---

## ScreenShots

- C  Hello World (Static Binary)

![Screenshot 2025-04-06 014315](https://github.com/user-attachments/assets/5afe41bc-43ae-44cf-9587-eb966d197f4b)

- C++ Hello World (Static Binary

![Screenshot 2025-04-06 013624](https://github.com/user-attachments/assets/6512ba3c-891c-4259-9728-4fdf401835f3)

- Binary Compatibility Mode

![Screenshot 2025-04-06 014358](https://github.com/user-attachments/assets/a79ec982-4025-461e-aaee-fcd90084fc96)
