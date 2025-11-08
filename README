# CMPE-283 : Assignment 2

# Q1. Team Members Contributions

### Shantanu:
Contributed to the kernel-side implementation of the linux build, and setting up the Linux kernel repository by forking the official upstream repo on GitHub and cloning it into the outer VM. Configured the kernel build using `make menuconfig` command, and made sure the build environment is up and running.

Implemented main logic for KVM exit statistics tracking, including:

- Function `get_exit_reason_name()` to map VM exit codes to human-readable names.
- Global counters for both per-exit-type and total exit tracking.
- Changes in `__vmx_handle_exit()` to increment counters and print statistics every 10000 exits.

Ensured usage of atomic operations for thread safe counter updates.

Successfully built, installed, and booted the modified kernel (`6.18.0-rc4+`) to verify correct functionality.

Helped in documentation review and final repository organization for submission.

---

### Atharva:
Focused on testing, validation, and documentation.

Performed sanity tests on the modified kernel build including verifying the kernel version, boot process, and instrumentation matched the grading expectations.

Booted the inner VM under the modified kernel and ran workloads to trigger different VM exits.

Collected and analyzed VM exit statistics, identifying the most and least frequent exit types.

Contributed to final documentation by writing the `README.md`, summarizing implementation details, results, and observations.

---

# Q2. Detailed Steps

### Step 1: Repository Setup
- Forked and cloned Linux kernel from GitHub  
- Verified remote configuration  

### Step 2: Kernel Configuration
```bash
cp /boot/config-$(uname -r) .config
make olddefconfig
```
- Disabled `CONFIG_SYSTEM_TRUSTED_KEYRING`
- Verified `CONFIG_KVM_INTEL = m` or `y`

### Step 3: Locate KVM Exit Handler
- Edited `arch/x86/kvm/vmx/vmx.c`
- Found `__vmx_handle_exit()` and exit reason mappings

### Step 4: Implement Exit Statistics
```c
get_exit_reason_name()  // readable exit names
atomic64_t vm_exit_counters[256];
atomic64_t total_vm_exits;
```
- Updated `__vmx_handle_exit()` to count exits and print stats every 10,000 exits

### Step 5: Build Kernel
```bash
make bzImage
make modules
```
- Compiled modified file and fixed errors  
- Verified `bzImage` and `kvm-intel.ko`

### Step 6: Install & Test
```bash
make install
make modules_install
reboot
modprobe kvm-intel
dmesg -w
```
- Installed modules and kernel  
- Rebooted and confirmed new kernel  
- Loaded KVM modules, ran inner VM  
- Used `dmesg -w` to monitor exit statistics  

### Step 7: Commit & Push
```bash
git add arch/x86/kvm/vmx/vmx.c
git commit -m "Added VM exit tracking"
git push origin main
```
- Staged and committed `vmx.c` changes  
- Pushed to GitHub and verified visibility  

### Step 8: Verification & Data Collection
- Verified readable, correct, non-zero stats every 10,000 exits  
- Collected data and analyzed most/least frequent exit types  

---

# Q3. Frequency Exits

### Does the number of exits increase at a stable rate?

Yes, the exits are non-stop alongside the inner VM's operation, and after each 10,000 total exits, the statistics are displayed. The normal use of virtual machines does not impact the exit rate, however, it does vary a little due to different types of workloads. The performance shows a steady increase with initially 580,000 then increasing to 590,000 then increasing to 600,000 then increasing to 610,000 then increasing to 620,000 exits. This means there are 10,000 exits every 1-2 minutes during normal operation.

---

### Are there more exits performed during certain VM operations?

Yes, more exits occur during:

- **VM Boot:** Higher as the guest OS initializes, loads drivers, and sets up resources. Includes many CPUID calls Exit 10, CR access Exit 28, and EPT operations Exit 48, 49. The boot phase has the highest exit rate.  
- **IO Operations:** Substantial increase in IO Instruction exits Exit 30, the most frequent exit type. Statistics show 279,060 IO Instruction exits out of 620,000 total (45%).  
- **System Calls:** Triggers various exit types including CPUID Exit 10, MSR reads/writes Exit 31, 32, and EPT violations Exit 48.  
- **Idle State:** HLT exits Exit 12 become more common, with 32,691 HLT exits observed.  

---

### Approximately how many exits does a full VM boot entail?

According to the analysis of over 620,000 exits, the complete boot of a virtual machine (VM) results in about **200,000 to 300,000 exits** before the machine is fully operational and the system is prepared:

- **Startup phase:** Almost 50,000–100,000 exits (CPUID, CR accesses, EPT setup)  
- **Driver loading:** Close to 50,000–100,000 exits (IO instructions, MSR accesses)  
- **System services startup:** Close to 50,000–100,000 exits (various exit types)  
- **Final boot completion:** Close to 50,000 exits  

The exact number varies with guest OS, installed software, and system configuration. After initial boot, the exit rate stabilizes to a lower, more consistent rate.

---

# Q4. Most and Least Frequent Exit Type

### Most Frequent Exit Types:
- Exit 30 (IO Instruction Exit) – 279,060 exits (45%)  
- Exit 10 (CPUID Exit) – 198,245 exits (32%)  
- Exit 28 (CR Access Exit) – 32,697 exits (5.3%)  

### Least Frequent Exit Types:
- Exit 29 (DR Access Exit) – 2 exits  
- Exit 47 (LDTR/TR Exit) – 2 exits  
- Exit 54 (WBINVD Exit) – 3 exits  
- Exit 55 (XSETBV Exit) – 3 exits  

Most frequent exits are IO Instruction and CPUID.  
Least frequent exits are debugging and system-level operations.