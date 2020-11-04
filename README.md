# Spectre Attack Resistant BOOM

Modern processor uses methods such as Out of order execution, Speculative Execution to improve performance. <br/>
Although it made a big change in performance, there was a problem with unexpected security. 
In the event of misprediction, the micro-architecture state is restored to the before-prediction state and the result of the mispredicted transient instructions cannot be checked at the position of the programmer.

However, the cache state wasn't restored during the process of misprediction handling, allowing data to be taken out through cache side channel attacks such as FLUSH+RELOAD.

To prevent this, We implemented SAR-1, 2 BOOM (Spectre Attack Resistant BOOM) by modifying the behavior of MSHR handling Data Cache miss. 

SAR-BOOM is implemented in two versions.
The SAR-1 BOOM determines whether the granted Data from L2, Main memory commit or not through the branch mask at the Bypass state of the MSHR, and the SAR-2 BOOM waits until the requested load instruction becomes non-speculative through the PNR index and the branch mask at the Speculation check state, and then commits.


## Getting Started

```
In Ubuntu/Debian-based platforms (Ubuntu)

mkdir Spectre_Boom
cd Spectre_Boom
set -ex
sudo apt-get install -y build-essential bison flex
sudo apt-get install -y libgmp-dev libmpfr-dev libmpc-dev zlib1g-dev vim git default-jdk default-jre
echo "deb https://dl.bintray.com/sbt/debian /" | sudo tee -a /etc/apt/sources.list.d/sbt.list
curl -sL "https://keyserver.ubuntu.com/pks/lookup?op=get&search=0x2EE0EA64E40A89B84B2DF73499E82A75642AC823" | sudo apt-key add
sudo apt-get update
sudo apt-get install -y sbt
sudo apt-get install -y texinfo gengetopt
sudo apt-get install -y libexpat1-dev libusb-dev libncurses5-dev cmake
sudo apt-get install -y python3.6 patch diffstat texi2html texinfo subversion chrpath git wget
sudo apt-get install -y libgtk-3-dev gettext
sudo apt-get install -y python3-pip python3.6-dev rsync libguestfs-tools expat ctags
sudo apt-get install -y device-tree-compiler

# verilator
git clone http://git.veripool.org/git/verilator
cd verilator
git checkout v4.034
autoconf && ./configure && make -j16 && sudo make install
cd ..

# chipyard sources
git clone https://github.com/ucb-bar/chipyard.git
cd chipyard
./scripts/init-submodules-no-riscv-tools.sh

# building a toolchain. It takes more than one hour.
./scripts/build-toolchains.sh riscv-tools # for a normal risc-v toolchain

# environment setup 
source ./env.sh


# make SmallBoomConfiguration with Verilator
cd sims/verilator
make CONFIG=SmallBoomConfig

```


## Running the Spectre Attack var1 on BOOM core


```

# boom attack binary codes
git clone https://github.com/Junsik-Shin/spectre_var1.git
cd spectre_var1
make
cd ..

# run simulator
./simulator-chipyard-SmallBoomConfig spectre_var1/bin/condBranchMispred.riscv

```

## Running the Spectre Attack var1 on SAR-2 BOOM core

```
# change boom core into spectre-resistant boom core
cd ../../generators/
mv boom/ boom_initial/
git clone https://github.com/Junsik-Shin/boom.git
cd ../sims/verilator/
make CONFIG=SmallBoomConfig
./simulator-chipyard-SmallBoomConfig spectre_var1/bin/condBranchMispred.riscv

```



## Built With

* Jaewoong Sim <br />
Assistant Professor <br />
Electrical and Computer Engineering <br />
Seoul National University <br />
Room 1007, Bldg. 301 <br />


## License

The Berkeley Out-of-Order RISC-V Processor [![CircleCI](https://circleci.com/gh/riscv-boom/riscv-boom.svg?style=svg)](https://circleci.com/gh/riscv-boom/riscv-boom)
====================================================================================================================================================================

The Berkeley Out-of-Order Machine (BOOM) is a synthesizable and parameterizable open source RV64GC RISC-V core written in the
[Chisel](https://chisel.eecs.berkeley.edu/) hardware construction language. While BOOM is primarily ASIC optimized, it is also usable on FPGAs.
We support the FireSim flow to run BOOM at 90+ MHz on FPGAs on Amazon EC2 F1. Created at the University of California,
Berkeley in the [Berkeley Architecture Research](https://bar.eecs.berkeley.edu/) group, its focus is to create a high
performance, synthesizable, and parameterizable core for architecture research.

## Documentation and Information

Please check out the BOOM website @ https://boom-core.org for the most up-to-date information.
It contains links to the mailing lists, documentation, design spec., publications and more!
