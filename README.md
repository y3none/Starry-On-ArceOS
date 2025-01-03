# StarryOS

[![CI](https://github.com/arceos-org/starry-next/actions/workflows/ci.yml/badge.svg?branch=main)](https://github.com/arceos-org/starry-next/actions/workflows/ci.yml)

A monolithic kernel based on [ArceOS](https://github.com/arceos-org/arceos).

## Quick Start

### 1. Install Build Dependencies

Install [cargo-binutils](https://github.com/rust-embedded/cargo-binutils) to use `rust-objcopy` and `rust-objdump` tools:

```bash
cargo install cargo-binutils
```

#### Dependencies for C apps

Install `libclang-dev`:

```bash
sudo apt install libclang-dev
```

Download & install [musl](https://musl.cc) toolchains:

```bash
# download
wget https://musl.cc/aarch64-linux-musl-cross.tgz
wget https://musl.cc/riscv64-linux-musl-cross.tgz
wget https://musl.cc/x86_64-linux-musl-cross.tgz
# install
tar zxf aarch64-linux-musl-cross.tgz
tar zxf riscv64-linux-musl-cross.tgz
tar zxf x86_64-linux-musl-cross.tgz
# exec below command in bash OR add below info in ~/.bashrc
export PATH=`pwd`/x86_64-linux-musl-cross/bin:`pwd`/aarch64-linux-musl-cross/bin:`pwd`/riscv64-linux-musl-cross/bin:$PATH
```

#### Dependencies for running apps

```bash
# for Debian/Ubuntu
sudo apt-get install qemu-system
```

```bash
# for macos
brew install qemu
```

Notice: The version of `qemu` should **be no less than 8.2.0**.

Other systems, arch and version please refer to [Qemu Download](https://www.qemu.org/download/#linux)

### 2. Build & Run

```bash
# Clone the base repository
./scripts/get_deps.sh

# Build user applications
make user_apps ARCH=<arch> AX_TESTCASE=<testcases>

# Build kernel
make ARCH=<arch> LOG=<log> AX_TESTCASE=<testcases> build

# Run kernel
make ARCH=<arch> LOG=<log> AX_TESTCASE=<testcases> run
```

Where `testcases` are shown under the `apps/` folder.

`<arch>` should be one of `riscv64`, `aarch64`, `x86_64`.

`<log>` should be one of `off`, `error`, `warn`, `info`, `debug`, `trace`.

More arguments and targets can be found in [Makefile](./Makefile).

For example, to run the [nimbos testcases](apps/nimbos/) on `qemu-system-x86_64` with log level `info`:

```bash
make ARCH=x86_64 LOG=info AX_TESTCASE=nimbos run
```

Note: Arguments like `NET`, `BLK`, and `GRAPHIC` enable devices in QEMU, which take effect only at runtime, not at build time.


### 磁盘读取  
1.使用build_img脚本打包所需要的文件，注意是可执行文件的父目录  
```
sh ./build_img.sh -a x86_64 -fs fat32 -file apps/nimbos/build/x86_64 -s 30
```  
2.将生成的disk.img放入.arceos根目录，修改`Starry-On-ArceOS/src/main.rs`中的`JUNIOR`为所打包的测例名  
3.运行下面指令跑相应的测例  
```
make ARCH=x86_64 LOG=info AX_TESTCASE=nimbos run BLK=y NET=y
```  