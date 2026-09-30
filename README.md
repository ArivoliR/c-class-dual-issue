<div class="title-block" style="text-align: center;" align="center">

[![License](https://img.shields.io/badge/License-BSD%203--Clause-blue.svg)](LICENSE)
[![pipeline status](https://gitlab.com/shaktiproject/cores/c-class/badges/master/pipeline.svg)](https://gitlab.com/shaktiproject/cores/c-class/commits/master)
# C-Class Core Generator
</div>

## What is C-Class 

C-Class is a member of the [SHAKTI](https://shakti.org.in) family of processors.
It is an extremely configurable and commercial-grade 5-stage in-order core supporting the standard
RV64GCSUN ISA extensions. The core generator in this repository is capable of configuring the core
to generate a wide variety of design instances from the same high-level source code. The design instances
can serve domains ranging from embedded systems, motor-control, IoT, storage, industrial applications
all the way to low-cost high-performance linux based applications such as networking, gateways etc.

There have been multiple successful silicon [prototypes](http://shakti.org.in/tapeout.html)
of the different instances of the C-class thus proving its versatility. The extreme parameterization
of the design in conjunction with using an HLS like Bluespec, it makes it easy to add new features
and design points on a continual basis.

## Why Bluespec
The entire core is implemented in [Bluespec System Verilog (BSV)](https://github.com/BSVLang/Main), 
an open-source high-level hardware description language. Apart from guaranteeing synthesizable
circuits, BSV also gives you a high-level abstraction, like going from assembly [level programming] 
to C. You don’t do the dirty work, the compiler does all the work for you. It enables users to work 
at a much higher level thereby increasing throughput. 

The language is now supported by an open-source Bluespec compiler, which can generate synthesizable
verilog compatible for FPGA and ASIC targets.

## License
All of the source code available in this repository is under the BSD license. 
Please refer to LICENSE.iitm for more details.

## Building this dual-issue fork

This is the dual-issue C-class (branch `24-branch-di` of
[mounakrishna/c-class-dual-issue](https://github.com/mounakrishna/c-class-dual-issue)) plus the fixes
needed to build it today with public dependencies. None of them change the datapath.

- `configure/consts.py`: `caches_mmu` over HTTPS; csrbox and benchmarks repointed from deleted
  branches to `master`; Verilator `--no-timing` and `-Wno-MULTIDRIVEN` (needed by Verilator 5.04x/5.05x).
- `configure/configure.py`: `pip install --no-build-isolation`; applies
  `configure/patches/csrbox-master-compat.patch` (4-arg `logLevel`, debug probe path) before
  installing csrbox.
- `configure/utils.py`: decode tool output as UTF-8 (the ASCII decode hid real errors).
- `src/stage5.bsv`: two csrbox-master shims (`mv_simulate_log_start` → `0`; `ma_set_fflags`
  takes `rdtype`).

Prerequisites on `PATH`: `bsc` (Bluespec compiler), `verilator` (5.x), `riscv64-unknown-elf-gcc`
(only for benchmarks and tests), `git`, Python 3. `scripts/elf2hex` is a drop-in for the legacy
`elf2hex` tool if you don't have it.

```sh
python3 -m venv .venv && . .venv/bin/activate
pip install "setuptools<80" wheel
pip install --no-build-isolation -r requirements.txt
python -m configure.main --ispec sample_config/c64/rv64i_isa.yaml \
  --customspec sample_config/c64/rv64i_custom.yaml --gspec sample_config/c64/csr_grouping64.yaml \
  --dspec sample_config/c64/rv64i_debug.yaml --cspec sample_config/c64/core64.yaml
make generate_verilog -j$(nproc)
make link_verilator generate_boot_files        # simulator: bin/out
```

If `csrbox/` or `benchmarks/` is missing after configure, re-run the configure command; it resumes.

Run a benchmark:

```sh
make -C benchmarks dhrystone ITERATIONS=500    # -> benchmarks/output/code.mem
cd benchmarks/output && ln -sf ../../bin/* . && ./out   # results in app_log
```

The simulator does not exit on its own after the program finishes; stop it once `app_log` is written.

## Get Started [here](https://c-class.readthedocs.io/)

## Contributors (in alphabetical order of last name):

- Rahul Bodduna
- Neel Gala
- Vinod Ganesan
- Paul George
- Aditya Govardhan
- Mouna Krishna
- Arjun Menon
- Girinath P
- Deepa N Sarma
- Sadhana S
- Snehashri S
- Aditya Terkar

For any queries, please contact shakti.iitm@gmail.com



