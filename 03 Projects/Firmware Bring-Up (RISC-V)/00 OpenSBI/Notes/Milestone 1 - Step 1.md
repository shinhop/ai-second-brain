
# Goal
* Install riscv toolchain
* Install qemu
# Process
## Install RISC-V toolchain in MAC
Followed steps:
1. Created a case-sensitive APFS disk image at ~/riscv-build.sparseimage (60GB, sparse), mounted at /Volumes/riscv-build — required because glibc's build clobbers files on case-insensitive filesystems, and your main drive is case-insensitive.
2. Cloned repo there.
3. Troubleshooting
	1. Hit a real system bug along the way: your Command Line Tools installation had a corrupted, orphaned c++/v1 header directory shadowing the real libc++ headers, breaking all C++ compilation. Fixed by symlinking it to the correct SDK location
4. Ran make linux -j8 to completion and verified it with: 
```bash
riscv64-unknown-linux-gnu-gcc --version
riscv64-unknown-linux-gnu-gcc (g6afcc4f6d) 16.1.0
Copyright (C) 2026 Free Software Foundation, Inc.
This is free software; see the source for copying conditions.  There is NO
warranty; not even for MERCHANTABILITY or FITNESS FOR A PARTICULAR PURPOSE.
```

# Install qemu in MAC
Installed qemu using: `brew install qemu`
Verified qemu installation with:
``` bash
qemu-system-riscv64 --version
QEMU emulator version 11.0.2
Copyright (c) 2003-2026 Fabrice Bellard and the QEMU Project developers
```
Verified that qemu riscv virt machine is usable:
```bash
qemu-system-riscv64 -M help | grep virt
virt                 RISC-V VirtIO board
```
