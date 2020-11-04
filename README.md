# Spectre Attack Resistant BOOM

Modern processor는 성능을 향상시키기 위해 Out of order execution, Specualtive Execution과 같은 방법을 사용한다. 
성능면에서는 큰 변화를 일으켰지만, 성능이 좋아진만큼 예상치못한 security에 문제가 생겼다. 
Misprediction이 발생한 경우 micro-architecture state는 prediction이전 상태로 restore를 해서 programmer입장에서는 mispredicted instruction들의 결과를 확인할 수 없다. 
그러나, Cache state는 misprediction handling하는 과정에서 restore를 하지 않아 FLUSH+RELOAD와 같은 cache side channel attack을 통해 data를 빼낼 수 있게 되었다. 

이를 막기 위해 Data Cache miss를 handling하는 MSHR의 동작을 수정하여 SAR-BOOM (Spectre Attack Resistant BOOM)을 구현하였다.  
SAR-BOOM은 2가지 version으로 구현되었는데, 
SAR-1 BOOM은 MSHR의 Bypass state에서 branch mask를 통해 commit여부를 판단하고, 
SAR-2 BOOM은 Speculation check state에서 PNR index와 branch mask를 통해 non-speculative해질 때까지 기다린 다음에 commit을 한다. 


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

Spectre Attack variant 1에 대한 공격을 수행

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

* Jaewoong Sim
Assistant Professor
Electrical and Computer Engineering
Seoul National University
Room 1007, Bldg. 301


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
