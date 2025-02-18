FireGuard: A Generalized Microarchitecture for Fine-Grained Security Analysis on OoO Superscalar Cores
==================================================

This repository contains artefacts and workflows (Parsec 3.0) to reproduce experiments experiments in the DAC 2025 paper - "FireGuard: A Generalized Microarchitecture for Fine-Grained Security Analysis on OoO Superscalar Cores" by Zhe Jiang, Sam Ainsworth, and Timothy Jones.

Platform pre-requisities
========================
* An x86-64 system (more cores will improve simulation time).
* Linux operating system (we used Ubuntu 20.04 and Ubuntu 22.04)
* An AMD U280 FPGA (for FPGA simulation)

Dependencies Installation
========================
**Step 1.** Building simulation platform, RISC-V toolchain, Chisel toolchain, and other dependencies: 

```
git clone https://github.com/ucb-bar/chipyard.git
cd chipyard
git checkout 1.7.0
./scripts/init-submodules-no-riscv-tools.sh
./scripts/build-toolchains.sh riscv-tools # for a normal risc-v toolchain 
```

Hardware
========================
**Step 1.** Setting environments:

```
export FIREGUARD=$(dirname $(pwd))
export PLATFORM=$(pwd)
. ./env.sh
```

**Step 2.** Updating the source code, ensuring to achieve the latest version:
```
$FIREGUARD/Scripts/update_src.sh
```

**Step 3.** Building hardware for FireGuard:
```
cd $PLATFORM/sims/verilator
make config=RocketConfig
```

(**OUTPUT I**) After a few hours, a software simulator and the corresponding Verilog code will be generated:
```
./simulator-chipyard-RocketConfig # Software Simulator
./generated-src/chipyard.TestHarness.RocketConfig/chipyard.TestHarness.RocketConfig.top.v # Verilog code
```


Software
========================
To run Parsec, both Linux kernel and Parsec are required to be compiled:

**Step 1.** For Linux, downloading the kernel code:
```
git clone https://github.com/firesim/linux
git checkout firesim-v57
```
(**OUTPUT II**) With that, compiling the kernel using following steps: [link](https://firemarshal.readthedocs.io/en/latest/index.html)

**Step 2.**For Parsec, compiling guardian kernel first:
```
cd $FireGuard/Software/guardian_kernel
make $kernel_name
```

As described in the paper, 4 guardian kernels are supported. Setting the $kernel_name correspondingly:

```
gc_main_pmc: performance counter
gc_main_sanitiser: address sanitiser
gc_main_ss: shadow stack
gc_main_minesweeper: minesweeper 
gc_main_none: without a guardian kernel 
```

With the compilation, a guadian_kernel.o is generated. 

**Step 3.** Now, compiling Parsec and link the guardian_kernel.o:
```
cd $FireGuard/Software/parsec/pkgs
./build_parsec.sh
```

(**OUTPUT III** ) After a few minutes, the Parsec is compiled with the guardian_kernel.o:
```
./app # Parsec benchmark
./run_parsec.sh # Scripts to run all Parsec benchmark
```

FPGA Simulation
========================
With the above steps, hardware and software are generated.

Deploying the hardware on the U280 FPGA using standard Xilinx steps or FireSim: [link](https://docs.fires.im/en/latest/Getting-Started-Guides/On-Premises-FPGA-Getting-Started/Running-Simulations/Running-Single-Node-Simulation-Xilinx-Alveo-U280.html)

The Verilog is generated in (**OUTPUT I**), and the OS kernel and workload are in (**OUTPUT II & III**).
