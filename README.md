# wmt_riscv_study
My RISC-V study

## 基于RISC-V代理内核, spike pk  
* https://gitee.com/hustos/riscv-pke  

## 开源RISC-V处理器架构分析与验证  
* Ariane
* Spike  

## tvlad1234/linux-ch32v003, 软件模拟器    
* https://github.com/tvlad1234/linux-ch32v003  

## regymm/quasiSoC  
* https://github.com/regymm/quasiSoC  
* 基于RISC-V RV32IMA Zicsr软核 ​​​

## asfdrwe/ASFRV32I  
* https://github.com/asfdrwe/ASFRV32I  
* 可以用iverilog运行的RV32I简单软核  

## tinyemu  
* https://bellard.org/tinyemu/
```
tinyemu在xubuntu22上的编译运行效果如下。
虽然tinyemu名字叫tiny但编译需要用到
sudo apt install libcurl4-openssl-dev libsdl1.2-dev。
另外固件是预先提供的（root固件编译好但buildroot需要自己编译），
所以实际上只能这样：
./temu ../diskimage-linux-riscv-2018-09-23/root-riscv32.cfg，
并且这份代码已经较长时间没更新了
```
* https://github.com/drorgl/esp32-tinyemu  

## xubuntu 22 (don't use 20) + mmu rv64 linux-6.1.14 + qemu-system-riscv64 -M virt -kernel arch/riscv/boot/Image     
* (don't use ubuntu 20) sudo apt install gcc-riscv64-linux-gnu  
Q: how to install riscv64-unknown-elf-gcc ?   
A: The Ubuntu 20.04 package is known to be incomplete and hence useless    
https://github.com/riscvarchive/riscv-gcc/issues/171  
* qemu-system-riscv64 -M virt -kernel arch/riscv/boot/Image  
* Image-6.1.14_readme.txt  
```
我测试过，如果想用最简单方法跑riscv-linux，只需要装xubuntu 22（不要用20和之前的）
和qemu-system-misc就可以了，不需要编译opensbi（这个bios是qemu自带了一部分），
工具链用apt装-linux-gnu那个，然后配置编译内核
ARCH=riscv CROSS_COMPILE=riscv64-linux-gnu- make defconfig，
运行必须加-M virt，然后就能勉强跑起来（除了文件系统加载失败）：
qemu-system-riscv64 -M virt -kernel arch/riscv/boot/Image
```
* build and qemu run  
```
sudo apt install qemu-system-misc  
sudo apt install gcc-riscv64-linux-gnu flex bison
sudo apt install libncurses5-dev

ARCH=riscv CROSS_COMPILE=riscv64-linux-gnu- make defconfig
ARCH=riscv CROSS_COMPILE=riscv64-linux-gnu- make -j8

(this command line see also buildroot board folder readme)  
qemu-system-riscv64 -M virt
-kernel arch/riscv/boot/Image
-append "rootwait root=/dev/vda ro"
-netdev user,id=net0
-device virtio-net-device,netdev=net0
-nographic
(Ctrl+A X to exit)  

qemu-system-riscv64 -M help
Supported machines are:
microchip-icicle-kit Microchip PolarFire SoC Icicle Kit
none                 empty machine
shakti_c             RISC-V Board compatible with Shakti SDK
sifive_e             RISC-V Board compatible with SiFive E SDK
sifive_u             RISC-V Board compatible with SiFive U SDK
spike                RISC-V Spike board (default)
virt                 RISC-V VirtIO board

qemu-system-riscv64 -M virt -kernel arch/riscv/boot/Image
or
qemu-system-riscv64 -M sifive_u -kernel arch/riscv/boot/Image
```
* https://twd2.me/archives/13406  
* https://www.cnblogs.com/riyuejiuzhao/p/17695170.html  
* https://www.cnblogs.com/tadshi/p/14785635.html  

## xubuntu 22 + prebuilt cnlohr/mini-rv32ima-images + nommu rv32 linux-6.1.14 + qemu-system-riscv32 -M virt -bios none -kernel arch/riscv/boot/Image      
* https://github.com/cnlohr/mini-rv32ima-images/blob/master/images/linux-6.1.14-rv32nommu-cnl-1.zip  
* qemu-system-riscv32 -M virt -bios none -kernel arch/riscv/boot/Image    
```
怎么用qemu运行risc-v的rv32版linux内核呢？
这个其实比rv6要更容易，rv6你需要装工具链和编译内核，rv32甚至不需要这样做，
就装qemu-system-misc软件包即可。rv32分nommu和mmu版，
由于nommu版rv32比较多人研究跑模拟器，例如cnlohr/mini-rv32ima-images
是已经编译好的Image文件，然后用
qemu-system-riscv32 -M virt -bios none  -kernel Image命令运行即可
（可以参考buildroot的说明），连BIOS opensbi都不需要
```

## xubuntu 22 + prebuilt bootlin rv32-ilp32d gcc + mmu rv32 linux-6.1.14 + qemu-system-riscv32 -M virt -bios fw_dynamic.bin -kernel arch/riscv/boot/Image    
* get riscv32-ilp32d--glibc--stable-2024.05-1 from https://toolchains.bootlin.com  
 https://toolchains.bootlin.com/downloads/releases/toolchains/riscv32-ilp32d/tarballs/riscv32-ilp32d--glibc--stable-2024.05-1.tar.xz  
* PATH="/home/wmt/work_rv32/riscv32-ilp32d--glibc--stable-2024.05-1/bin:$PATH"    
* make ARCH=riscv CROSS_COMPILE=riscv32-buildroot-linux-gnu- rv32_defconfig  
* https://github.com/riscv-software-src/opensbi/releases/tag/v1.5.1  
https://github.com/riscv-software-src/opensbi/releases/download/v1.5.1/opensbi-1.5.1-rv-bin.tar.xz  
* qemu-system-riscv32 -M virt -bios fw_dynamic.bin -kernel arch/riscv/boot/Image  
```
如何编译rv32的mmu版linux内核Image文件？
ubuntu没有提供32位的交叉工具链，
所以一般推荐编译riscv-collab/riscv-gnu-toolchain，
但我这里直接用toolchains.bootlin版本（架构选择riscv32-ilp32d，glibc），
配置用rv32_defconfig（不能用defconfig），
fw_dynamic.bin文件可以从opensbi中找到
（share/opensbi/ilp32/generic），
然后执行qemu：
qemu-system-riscv32 -M virt -bios fw_dynamic.bin -kernel arch/riscv/boot/Image，
效果如下

xz -d riscv32-ilp32d--glibc--stable-2024.05-1.tar.xz
tar xf riscv32-ilp32d--glibc--stable-2024.05-1.tar

gedit ~/.profile
PATH="/home/wmt/work_rv32/riscv32-ilp32d--glibc--stable-2024.05-1/bin:$PATH"

riscv32-buildroot-linux-gnu-gcc

sudo apt install flex bison libncurses5-dev
make ARCH=riscv CROSS_COMPILE=riscv32-buildroot-linux-gnu- rv32_defconfig
(don't use defconfig)
make ARCH=riscv CROSS_COMPILE=riscv32-buildroot-linux-gnu- -j8

(unzip opensbi-1.5.1-rv-bin.tar.xz sbi-1.5.1-rv-bin/share/opensbi/ilp32/generic/fw_dynamic.bin to ~/work_rv32/linux-6.1.14)

qemu-system-riscv32 -M virt -bios fw_dynamic.bin -kernel arch/riscv/boot/Image
```
* 构建RISC-V工作环境  
https://twd2.me/archives/13406  
* Spike模拟Linux 5.3教程  
https://cnrv.io/articles/spike-linux  
* BBL : Berkeley Boot Loader  
https://github.com/riscv-software-src/riscv-pk  
* 【博文精选】eclipse联和DGB在qemu平台调试riscv linux  
https://www.163.com/dy/article/EF89QAOI0512EO4N.html  
RISC-V篇-qemu+gdb调试Linux kernel源码   
https://blog.csdn.net/u010826758/article/details/143319443  
* Juraj's Blog, Building up a RISC-V Linux with Buildroot  
https://jborza.com/post/2020-04-08-riscv-environment/  
* https://sources.buildroot.net/riscv-isa-sim/  
* Running 64- and 32-bit RISC-V Linux on QEMU  
https://risc-v-getting-started-guide.readthedocs.io/en/latest/linux-qemu.html  
* riscv gcc工具链是如何被编译的    
https://zhuanlan.zhihu.com/p/567254343    
* How to install riscv32-unknown-elf-gcc on Debian-based Linuxes  
https://stackoverflow.com/questions/74231514/how-to-install-riscv32-unknown-elf-gcc-on-debian-based-linuxes

# spike --isa=rv64gcv, suggest to use ubuntu 22 or above   
* ./riscv64-unknown-elf-gcc -march=rv64gcv -O2 -static -o hello2 hello.c    
* spike --isa=rv64gcv pk hello2  
* https://github.com/riscv-software-src/riscv-isa-sim  
sudo apt-get install device-tree-compiler  
https://github.com/riscv-software-src/riscv-pk/tree/a3e4ac61d2b1ff37a22b9193b85d3b94273e80cb
* xubuntu200464_spike_bakcup  
* https://cnrv.io/articles/spike-linux
* https://blog.csdn.net/qq_41750952/article/details/118033031  
* https://blog.csdn.net/ACHENJIE/article/details/108450912
* https://zhuanlan.zhihu.com/p/137575836
* toolchain bin  
https://github.com/riscv-collab/riscv-gnu-toolchain/releases  
https://aur.archlinux.org/packages/riscv-gnu-toolchain-bin  
https://github.com/riscv-software-src/riscv-isa-sim/issues/943  
https://cloud.tencent.com/developer/ask/sof/105236918  
cd riscv64-elf-x86_64-20201104/bin
``` 
（来源于melis的工具链）
（https://github.com/sterling-teng/d1s-melis/blob/master/prebuilt/md5-x86_64-linux.txt）    
./riscv64-unknown-elf-gcc -march=rv64gcv -O2 -static -o hello2 hello.c  
(一定要加-O2，算pi太慢。因为T-HEAD的工具链，所以会变成rv64gcxthead，所以要指定march）  
cp hello2 ../../riscv-isa-sim/ci-test2  
/home/wmt/spike/bin/spike --isa=rv64gcv pk hello2  

（注：下文中的xubuntu 20最好改用xubuntu 22）

spike linux研究。研究了很久勉强可以把spike跑起来
（1）编译spike，代码库是：riscv-software-src/riscv-isa-sim。
我用xubuntu 2004编译，没有报错，但编译出来有两三百M比较大
（2）获取pk。这个轻量级内核可以不用自己编译，在riscv-isa-sim的发布页面中有预编译
see also spike-ci.tar  
（3）获取工具链，我是用平头哥T-Head的非标准交叉工具链：
Tina-Linux/d1s-melis的riscv64-elf-x86_64-20201104
（4）编译hello，因为工具链非标准需要强制用标准架构，
否则spike会出现Illegal instruction错误：
./riscv64-unknown-elf-gcc -march=rv64gcv -O2 -static -o hello2 hello.c
（5）运行：需要指定isa或者忽略：spike --isa=rv64gcv pk hello2
（6）备注，可能-march的架构不用V也可以，V可能是向量特性的意思
search baidupan, xubuntu200464_spike_bakcup
```
* https://github.com/camel-cdr/rvv-bench/issues/8    
 'illegal instruction' when using spike  
Should use spike --isa=rv64gcv and gcc -march=rv64gcv to solve this problem    
* spike-ci.tar (two versions ? search spike-ci (1).tar or search spike-ci.tar)   
https://github.com/riscv-software-src/riscv-isa-sim/releases/tag/dummy-tag-for-ci-storage    

## LiteX, include VexRiscv, KianRiscV (kianv), cv32e40p, CVA6 (Ariane), PicoRV32        
* my weibo record
```
我觉得大概率FPGA跑Linux的话用35K逻辑单元就足够了，我的理由是这个：
sipeed/TangNano-20K-example/tree/main/linux，虽然这例子不开源，
但据说它能跑LiteX（怀疑是vexriscv，也可能是kianv，应该都不好看懂代码）。
如果是PicoRV32的话用9K就足够了（不过PicoRV32可能不够酷炫），
理由是这个：sipeed/TangNano-9K-example/tree/main/picotiny

基于FPGA的软核RISC-V Linux，litex（实际是vexriscv软核），
似乎也支持模拟器，命令叫做litex_sim，我不知道是不是我想的那种，
（可能）不需要开发板就能直接模拟跑linux命令行，
如果是的话可以考虑把它的模拟器源代码抽出来玩
（当然有可能没那么简单），待考 ​​​

《LiteX 定制 SoC 上使用 C 和 Rust 嵌入式 (RISC-V)》这东西看上去好tm酷炫，
可以定制外设和生成sdk？（待考，可能工具链是固定的）不过我不想只知道怎么做，
我想知道这东西的原理（以及方法论，以移植到其他架构诸如MIPS），
所以还是慢慢来吧 ​​​

少数几个知名RTOS的文档会收录关于RISC-V软核或模拟器的相关运行资料，但不一定很详细。
（1）rt-thread有关于cv32e40p的介绍（参考：快速上手, core-v-mcu）
（2）zephyr有关于VexRiscv（参考：Suported Boards, RISC-V Boards, LiteX VexRiscv）
（3）nuttx有关于VexRiscv（参考：Enjoy Digital LiteX FPGA’s，类似zephyr）

我查了一下，Ariane应该还是比较有名的，现在改名字叫CVA6，属于openhwgroup，
特点就是可以跑Linux（其实我觉得能跑简单的RTOS或者Bootloader已经足够了）。
同时litex也支持，不过能跑这个软核的FPGA应该比较高配置。
至于openhw group，可以理解为（可能是）最大和最全面的RISC-V开源组织
（我是不是可以说它是最权威的），所以它有各种各样类似CVA6的RISC-V工程
```
* https://github.com/sipeed/TangNano-20K-example/tree/main/linux  
* https://github.com/sipeed/TangNano-9K-example/tree/main/picotiny  

## splinedrive/KianV-RV32IMA-RISC-V-uLinux-SoC  
* cocotb, iverilog  
* readmemh
* https://github.com/splinedrive/KianV-RV32IMA-RISC-V-uLinux-SoC/blob/main/test/tb.v  
* https://github.com/splinedrive/KianV-RV32IMA-RISC-V-uLinux-SoC/blob/main/test/spiflash.v
* https://github.com/splinedrive/KianV-RV32IMA-RISC-V-uLinux-SoC/tree/main/test  
```
sudo apt install python3-pip
pip install -r requirements.txt
. ~/.profile
(or restart ubuntu 22 to let ~/.profile to add ~/.local/bin to PATH)
cd test
sudo apt install iverilog
make clean
make all
```
* https://github.com/darklife/darkriscv
* https://github.com/darklife/darkriscv/blob/master/src/darksocv.mem
* https://github.com/darklife/darkriscv/blob/master/rtl/darkram.v  
* https://gitlab.com/x653/xv6-riscv-fpga  

## kianRiscV + Tang Nano 20K  
* https://github.com/splinedrive/kianRiscV/tree/master/linux_socs/kianv_harris_mcycle_edition/demo/tangNano20K  
* https://github.com/splinedrive/kianRiscV/tree/tangnano20k_ulinux_soc/linux_socs/kianv_harris_mcycle_edition/demo/tangNano20K  
```
cd linux_socs/kianv_harris_mcycle_edition/demo/tangNano20K
qemu-system-riscv32 -M virt -bios none -kernel Image 

splinedrive/kianRiscV的分支tangnano20k_ulinux_soc里面也有
一个预先编译好的rv32ima的Image内核文件，
我试过也是可以用qemu跑的，例如这样：
qemu-system-riscv32 -M virt -bios none -kernel Image，
内核版本是6.1，不过不确定是否开源，
也有可能是直接复制mini-rv32ima作者编译的镜像，待考 ​​​
```

## mit-pdos/xv6-riscv, version 2022-08-26 + xubuntu 22   
* https://github.com/mit-pdos/xv6-riscv/tree/f5b93ef12f7159f74f80f94729ee4faabe42c360  
```
sudo apt install gcc-riscv64-unknown-elf libnewlib-dev qemu-system-misc
cd xv6-riscv-f5b93ef12f7159f74f80f94729ee4faabe42c360/

TOOLPREFIX=riscv64-unknown-elf- make CPUS=1 clean qemu
(Ctrl+A X to exit)

qemu-system-riscv64 -machine virt -bios none -kernel kernel/kernel
-m 128M -smp 1 -nographic -global virtio-mmio.force-legacy=false
-drive file=fs.img,if=none,format=raw,id=x0
-device virtio-blk-device,drive=x0,bus=virtio-mmio-bus.0
```
* (TODO) how to gdb target remote :26000
```
用xubuntu 22编译运行mit-pdos/xv6-riscv。工具链用apt装-unknown-elf即可。
gdb似乎连不上（target remote :26000），待考。
我发现最新版编译了跑不了，不知道原因，
所以我这里是回退到2022年8月份版本
```

## Linux 6.1.14-rv32ima On Scratch.sb3  
* https://scratch.mit.edu/projects/892602496

## sifive prebuilt riscv64-unknown-elf-gcc, 2019 (gcc 8.1.0) and 2020 (gcc 10.2.0)    
* work_spike_linux
* freedom-tools  
https://riscv.org/news/2021/07/video-spike-proxy-kernel-from-source-to-hello-world-danny-pratama/    
https://www.youtube.com/watch?v=sPvMXGFTC2U    
https://github.com/sifive/freedom-tools/releases/tag/v2020.12.0     
riscv64-unknown-elf-toolchain-10.2.0-2020.12.8-x86_64-linux-ubuntu14.tar.gz    
riscv-openocd-0.10.0-2020.12.1-x86_64-linux-ubuntu14.tar.gz    
bin2hex, elf2bin, elf2hex, zspike-dasm:    
sdk-utilities-1.0.1-2020.12.1-x86_64-linux-ubuntu14.tar.gz    
* sifive_dev_tools_zephyr  
risc-v toolchain, zephyr litex vexriscv on arty board  
https://docs.zephyrproject.org/latest/boards/enjoydigital/litex_vexriscv/doc/index.html  
https://developer.nordicsemi.com/nRF_Connect_SDK/doc/1.9.99-dev1/zephyr/boards/riscv/litex_vexriscv/doc/index.html    
wget https://static.dev.sifive.com/dev-tools/riscv64-unknown-elf-gcc-8.1.0-2019.01.0-x86_64-linux-ubuntu14.tar.gz    
tar -xf riscv64-unknown-elf-gcc-8.1.0-2019.01.0-x86_64-linux-ubuntu14.tar.gz    
export PATH=$PATH:$PWD/riscv64-unknown-elf-gcc-8.1.0-2019.01.0-x86_64-linux-ubuntu14/bin/    
riscv64-unknown-elf-gcc-8.1.0-2019.01.0-x86_64-linux-ubuntu14.tar.gz  
