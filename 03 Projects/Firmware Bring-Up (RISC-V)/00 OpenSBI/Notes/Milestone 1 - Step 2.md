# Knowledge goals
- [ ] Can state, unaided and out loud, what M/S/U privilege modes are for and why RISC-V has more than one
- [ ] Can explain why execution starts in M-mode at reset (not S or U)
- [ ] Can describe what `mtvec`, `mepc`, and `mstatus` each do, in your own words, without looking it up mid-explanation
- [ ] Can state why bare-metal firmware (not Linux) must run first on real silicon
# Notes
Let's start by pointing to useful specs for answer the questions.
We have to read: 
* [Volume II: RISC-V Privileged ISA Specification](https://docs.riscv.org/reference/isa/v20260120/priv/priv-intro.html) 
	* [Chapter 1 - Introduction](https://docs.riscv.org/reference/isa/v20260120/priv/priv-intro.html)
	* [Chapter 3 - Machine-Level ISA](https://docs.riscv.org/reference/isa/v20260120/priv/machine.html)

## Chapter 1 - Notes
### RISC-V Software stack terminology
![[Pasted image 20260705134228.png]]
I'll focus on the linux example for my explanation:
* **ABI - Application Binary Interface:** It is basically the interface that an user space application being executed on U-Mode sees and uses to operate. Following an example based on a linux system it would be the syscalls that an user space applications uses to ask permission to the kernel for certain operations to be done.
* **AEE - Application Execution Environment**: Is the actual implementation that the ABI sees and uses. Following the linux example it would be the linux kernel.
* **SBI - Supervisor Binary Interface:** It is another interface that the AEE relies on. Since the kernel is being executed in S-Mode there are certain operations that cannot be done and needs another interface to delegate those operations to the M-Mode. Following the example of linux, it would be the kernel relying into SBI Calls.
* **SEE - Supervisor Execution Environment**: The actual implementation of that SBI that the kernel AEE sees and uses. Following our example it would be OpenSBI.
The explanation above focuses on one hardware running a OpenSBI + Linux. We could escalate that in one hardware by having an hypervisor which enables the hardware to run several OS instances. Therefore adding one more layer into the system. At this point the OS would do the SBI calls but instead of going into the M-Mode directly those calls would trap into the hypervisor instead which could emulate and answer or actually delegate it to the real M-Mode. It is worth mentioning that the Guest-OS is not aware of it. Hypervisor is not a new priviledge mode, is actually a S-mode extension (Hypervisor-extended S-Mode).
### Privilege levels
Privilege levels are used to provide protection between different components of the software stack, and attempts to perform operations not permitted by the current privilege mode will cause an exception to be raised. Each of these modes have a core set of privileged ISA extensions:
* M-Mode: This mode is the only mandatory one. The softwares that runs in this privilege is trusted software managing low level operations.
* S-Mode: This mode is intended for the operating system.
* U-Mode: This mode is intended for user applications.
Usual usage

| Privilege Modes | System example           |
| --------------- | ------------------------ |
| M only          | Embedded systems         |
| M,U             | Secure embedded system   |
| M,S,U           | Operating System (Linux) |
Hart usually runs in U-Mode. Then some event can trigger privilege escalation (timer interrupt, sbi call, syscall). This event forces a switch to the trap handler, when the trap is handled the code returns to U-mode. This sequence is called vertical trap because it increases the privilege level. On the other side a horizontal trap is a trap that happens on the same privilege level that the code is running.

Is worth noting that there is the D-Mode called Debug Mode. We can consider that this in an additional privilege mode that has even more privileged than the M-mode. We can say that because there are certain CSR that are only accessible from D-Mode.

### CSRs
The CSR are Control and Status Registers. Those are a separated bank of registers that are used to configure the CPU itself. Those kind of registers are privilege gated and the address of those registers actually encode which privilege mode can access to them
#### Address encoding
Those registers have 12bit addresses and the encoding is as follows
* csr[11:10] -> Access permission: R, W, R/W
* csr[9:8] -> Minimal privilege mode (U, S , M)
* csr[7:0] -> Address value

## Chapter 3 - Notes
M-Mode is the first mode that is entered at reset.

#### Interesting takes:
* Userspace  Application runs in U-Mode and when needs to escalate privileges uses syscalls for the kernel to execute that particular request in S-Mode. If the kernel needs to do machine level operations it uses the SBI Ecalls to escalte priviledges and request a particular operation in M-Mode
* M-mode is the only mandatory mode as it is the only that can perform high privilege low level operations meaning interact directly with the hardware.
* CSR are special registers to configure the CPU. The address itself has encoded which privilege level can interact with the register. The have an special 12bits address encoding which specifies permitted R/W operation and Minimal privilege level 

