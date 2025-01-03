# wmt_riscv_study
My RISC-V study

## TODO
* see https://github.com/weimingtom/wmt_ai_study/blob/master/fpga_cpld_001.md  

## 基于RISC-V代理内核, spike pk  
* https://gitee.com/hustos/riscv-pke  

## 开源RISC-V处理器架构分析与验证  
* Ariane
* Spike  

## RISC-V体系结构编程与实践 (for rv64?)    
* https://github.com/runninglinuxkernel/riscv_programming_practice  
* https://github.com/runninglinuxkernel/NEMU  
```
BenOS这个项目，本来是另一本书的《ARM体系结构》的baremetal项目，可以运行在qemu和树莓派上，
现在又被移植到RISC-V qemu和D1s开发板上，所以感觉有点像xv6的更简单版。。。
不过可能它和xv6比会更简单一些，而且是渐进式开发。
实际上gh上也有其他项目是用类似的方法，渐进地开发出一个OS，但不如xv6那么经典
```
* 《RISC-V体系结构编程与实践》书籍配套硬件DongshanPi-D1s 示例代码程序仓库地址。
https://gitee.com/weidongshan/riscv_programming_practice  
* https://github.com/OpenXiangShan/NEMU
* https://github.com/NJU-ProjectN/nemu
* https://github.com/runninglinuxkernel/BenOS
* https://github.com/OpenXiangShan/XiangShan  

## tvlad1234/linux-ch32v003, 软件模拟器    
* https://github.com/tvlad1234/linux-ch32v003  

## regymm/quasiSoC  
* https://github.com/regymm/quasiSoC  
* 基于RISC-V RV32IMA Zicsr软核 ​​​

## asfdrwe/ASFRV32I  
* https://github.com/asfdrwe/ASFRV32I  
* 可以用iverilog运行的RV32I简单软核
* (TODO) verilator ???  
* https://qiita.com/asfdrwe/items/44e463cf60a99232fc22  

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
* (don't use ubuntu 20, use xubuntu 22) sudo apt install gcc-riscv64-linux-gnu  
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

## xubuntu 22 + prebuilt bootlin rv32-ilp32d gcc + nommu rv32 linux-6.8 (don't use linux-6.1.14) + qemu-system-riscv32 -M virt -bios none -kernel arch/riscv/boot/Image        
* get riscv32-ilp32d--glibc--stable-2024.05-1 from https://toolchains.bootlin.com  
 https://toolchains.bootlin.com/downloads/releases/toolchains/riscv32-ilp32d/tarballs/riscv32-ilp32d--glibc--stable-2024.05-1.tar.xz   
* PATH="/home/wmt/work_rv32/riscv32-ilp32d--glibc--stable-2024.05-1/bin:$PATH"  
* nommu_rv32_defconfig (add CONFIG_ARCH_RV32I=y and CONFIG_32BIT=y to the top of nommu_virt_defconfig)    
make ARCH=riscv CROSS_COMPILE=riscv32-buildroot-linux-gnu- nommu_rv32_defconfig  
```
xz -d riscv32-ilp32d--glibc--stable-2024.05-1.tar.xz   
tar xf riscv32-ilp32d--glibc--stable-2024.05-1.tar  

copy nommu_virt_defconfig to nommu_rv32_defconfig
add to top
CONFIG_ARCH_RV32I=y
CONFIG_32BIT=y

make sure .config:
remove defconfig INITRAMFS
dont' see CONFIG_MMU=y
dont' see CONFIG_NONPORTABLE=y

sudo apt install flex bison libncurses5-dev
make ARCH=riscv CROSS_COMPILE=riscv32-buildroot-linux-gnu- nommu_rv32_defconfig
make ARCH=riscv CROSS_COMPILE=riscv32-buildroot-linux-gnu- -j8
qemu-system-riscv32 -M virt -bios none -kernel arch/riscv/boot/Image 

############################
/arch/risc/Kconfig
if ARCH_RV32I = true, MMU = true

config ARCH_RV32I
	bool "RV32I"
	depends on NONPORTABLE
	select 32BIT
	select GENERIC_LIB_ASHLDI3
	select GENERIC_LIB_ASHRDI3
	select GENERIC_LIB_LSHRDI3
	select GENERIC_LIB_UCMPDI2
	select MMU
	
see https://github.com/tvlad1234/linux-ch32v003
6.8-rc1 remove this ? 
don't use 6.1.14, use 6.8 instead

https://github.com/cnlohr/mini-rv32ima-images/tree/master/images
https://cdn.kernel.org/pub/linux/kernel/v6.x/
############################
```
* weibo record
```
编译运行risc-v rv32 nommu linux成功，这样就可以不用cnlohr/mini-rv32ima-images
的预编译内核Image也能自己编译出来。
不过这个过程有点绕，因为Linux有一些版本会判断是否编译RV32I这个ISA架构，
如果是的话会强制打开MMU，所以正确的做法不能用linux-6.1，
我这里用的是linux-6.8的代码，复制nommu_virt_defconfig到nommu_rv32_defconfig，
然后在配置开头加上：CONFIG_ARCH_RV32I=y和CONFIG_32BIT=y即可，
编译后执行qemu-system-riscv32 -M virt -bios none -kernel arch/riscv/boot/Image ，
然后切换到qemu的serial0输出效果如下。暂时还没测试mini-rv32ima是否也能正常跑
（补注：mini-rv32ima无法模拟运行这个Image）

其实Linux 6内核源码里面有关于k210的nommu defconfig，
能否用这个默认配置去编译rv32 nommu linux？
我怀疑不行，因为这个可能不是对应rv32i isa架构的，
不过我没实际试验过——我试验过旧版本的linux会出现编译失败的情况，
就是说如果要编译K210的nommu用了32位目标的交叉工具链很可能会失败，
所以最好还是自己另外创建一个virt默认配置
```
* rv32_nommu_linux_build
* linux-6.8.tar.gz

# spike --isa=rv64gcv, suggest to use ubuntu 22 or above   
* (TODO) see also https://github.com/sifive/freedom-e-sdk  
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

spike linux研究。实际上spike的gh仓库riscv-software-src/riscv-isa-sim的说明文档，
还有油管上的教学视频，都有非常大的参考价值，甚至连工具链和bare-metal的写法和linker script写法，
调试方法都有。具体我后面再逐个讨论，不过我觉得调试工具的用法可以延申到其他架构的研究方法论，
但实际操作我觉得太繁琐了，尤其是openocd+gdb。
不过其他调试方法诸如-l日志和-d交互调试我觉得非常值得其他架构借鉴。
不过spike文档关于调试的内容基本上是基于C的，我认为最好还是用汇编，
这样会清晰很多
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

## (Failed, TODO) RV32 version XV6  
* search rv32_xv6_build, xv6_failed.tar.gz  
* rv32_xv6_build
```
目前编译的rv32版的xv6均无法模拟运行，除了splinedrive_kianRiscV-master.zip
里面的xv6勉强能用mini-rv32ima运行了开头的串口输出

其实我想试试用某种方式运行gh上那些rv32版的xv6（它原版不是支持rv32而是支持rv），
不过没成功（最接近的程度是能跑最开头的串口输出），
好吧，这已经超出我的能力范围，等以后再研究了（有生之年）
```
* (good ?) https://github.com/federicorichter/rv32i_xv6  
Seems to run with ubuntu 22 and qemu-system-riscv32 successfully, but code changes very much  

## What different from many combinations about -march=xxx and -mabi=xxx and -mcmodel=medany, "" and f and d    
* https://blog.csdn.net/qq_43493425/article/details/131066874
* https://blog.csdn.net/tugouxp/article/details/106567972
* https://blog.csdn.net/qq_39507748/article/details/115347636

## TangNano20K run RISC-V rv32 Linux with kianVLinuxRiscvSocTangNano20K.bin  
* https://github.com/splinedrive/kianRiscV/tree/master/linux_socs/kianv_harris_mcycle_edition/demo/tangNano20K  
* Gowin Programmer -> right mouse click menu to the list box to configure  
-> Choose the second item: External Flash Mode (and next combo box choose first item)    
->choose file to burn : 'kianVLinuxRiscvSocTangNano20K.bin' (I use master branch one)    
* And then use putty to connect TangNano20K's usb-c uart, baud rate is 2000000   
* Command prompt is a little slow (maybe because systick is too slow)  
```
我找到另外一种方法用TangNano20K运行RISC-V rv32 Linux，
而官方提供的串口litex_term启动linux似乎没成功。
我的方法是这样，通过固化exFlash方式烧录kianVLinuxRiscvSocTangNano20K.bin
（参考splinedrive/kianRiscV），然后通过串口连上去（例如我用putty），
波特率是2000000，然后就能看到控制台了，不过烧录很慢和输入也比较卡（键盘按下去回显会延迟），
估计软核的时钟频率不够高（时钟慢的话Linux的输入也会卡，这是避免不了的，
因为Linux内核非常依靠高速的systick，输入也需要）

Gowin Programmer，配置，选External Flash Mode，下面第二项选第一个
波特率2000000
文件选择kianVLinuxRiscvSocTangNano20K.bin
（可以选master分支中的bin，其他分支可能也有这个文件，但似乎不同，我测试用的是master分支的）
```
* https://github.com/enjoy-digital/litex/wiki/Load-Application-Code-To-CPU  

## TangNano20K run RISC-V rv32 Linux with TangNano-20K-example linux fs and Image  
* https://github.com/sipeed/TangNano-20K-example/tree/main/linux
* Burn firmware/sipeed_tang_nano_20k.fs with Gowin programmer (GW2AR GW2AR-18C, I choose exFlash and then first option)  
* Install python-3.7.8rc1-amd64.exe  
* pip3 install litex (need to update pip3)
* Create console.bat, and double click to enter cmd  
```
@set PATH=%PATH%;C:\Users\admin\AppData\Local\Programs\Python\Python37\Scripts
@cmd
```
* litex_term --serial-boot --images ./image/boot.json COM4 (do not need --speed param)
* Press an enter key     
* Get litex> prompt
* litex> serialboot (need about 10 minutes)  
```
D:\work_gw2a\linux>litex_term --serial-boot --images ./image/boot.json COM4


litex> serialboot

Booting from serial...
Press Q or ESC to abort boot completely.
sL5DdSMmkekro
[LITEX-TERM] Received firmware download request from the device.
[LITEX-TERM] Uploading ./image\Image to 0x40000000 (1818868 bytes)...
[LITEX-TERM] Upload calibration... (inter-frame: 10.00us, length: 64)
[LITEX-TERM] Upload complete (3.7KB/s).
[LITEX-TERM] Uploading ./image\sipeed_tang_nano_20k.dtb to 0x40780000 (2115 bytes)...
[LITEX-TERM] Upload calibration... (inter-frame: 10.00us, length: 64)
[LITEX-TERM] Upload complete (3.6KB/s).
[LITEX-TERM] Uploading ./image\opensbi.bin.tangnano20k to 0x407c0000 (49636 bytes)...
[LITEX-TERM] Upload calibration... (inter-frame: 10.00us, length: 64)
[LITEX-TERM] Upload complete (3.7KB/s).
[LITEX-TERM] Booting the device.
[LITEX-TERM] Done.
Executing booted program at 0x407c0000

--============= Liftoff! ===============--

OpenSBI v0.8-2-ga9ce3ad
...
Platform Name       : LiteX / VexRiscv-SMP
Platform Features   : timer,mfdeleg
Platform HART Count : 8
Boot HART ID        : 0
Boot HART ISA       : rv32imas
BOOT HART Features  : time
BOOT HART PMP Count : 0
Firmware Base       : 0x407c0000
Firmware Size       : 120 KB
Runtime SBI Version : 0.2

MIDELEG : 0x00000222
MEDELEG : 0x0000b101
[    0.000000] Linux version 6.4.0-rc1+ (zjs@CL-A-00022) (riscv64-linux-gnu-gcc (Debian 12.2.0-13) 12.2.0, GNU ld (GNU Binutils for Debian) 2.40) #10 Tue May 16 09:20:49 CST 2023
...
/ # uname -a
Linux (none) 6.4.0-rc1+ #10 Tue May 16 09:20:49 CST 2023 riscv32 GNU/Linux
```
```
我把sipeed的Tang Nano 20K开发板的litex linux例程（sipeed/TangNano-20K-example）跑通了。
简单来说就是官方的教程可能有误（或者是它用的litex_term和我用pip3 install litex安装的litex_term不同）。
litex_term的作用是串口命令行和串口烧录linux内核，所以其实可以在进入串口命令行（执行litex_term后需要多按一下回车）
看到litex提示符后才敲命令serialboot去烧录，而不一定要立刻烧录，所以我去掉了--speed参数，仅保留--serial-boot --images参数，
进入后回车看到绿色的litex提示符，然后执行serialboot，这样就可以看到下载进度条了。一共出现三次，
大概需要10分钟时间，启动非常快，输入输出也不卡顿，可能比splinedrive/kianRiscV的版本还要流畅

我测试过用sipeed/TangNano-20K-example的linux fs固件跑的litex-hub/linux-on-litex-vexriscv的Linux Image镜像是失败的
```

## Use linux-on-litex-vexriscv sim.py (not litex_sim) to run rv32 linux (cpu-type is vexriscv_smp), booting in about 30 minutes     
* https://github.com/litex-hub/linux-on-litex-vexriscv/blob/master/sim.py  
if use pip3 install litex and use litex_sim, I fail, so I use this way instead  
* (I didn't use this method, litex dev env) https://github.com/enjoy-digital/litex/wiki
* (I didn't use this method, litex dev env) wget https://raw.githubusercontent.com/enjoy-digital/litex/master/litex_setup.py
* https://github.com/enjoy-digital/litex/wiki/Load-Application-Code-To-CPU    
```
https://github.com/litex-hub/linux-on-litex-vexriscv/issues/164
(xubuntu200464)
pip3 install litex
pip3 install litedram
pip3 install liteeth
pip3 install litescope
pip3 install git+https://github.com/litex-hub/pythondata-cpu-vexriscv.git
pip3 install git+https://github.com/litex-hub/pythondata-cpu-vexriscv_smp.git
sudo apt install gcc-riscv64-linux-gnu
pip3 install git+https://github.com/litex-hub/pythondata-software-picolibc.git
pip3 install git+https://github.com/litex-hub/pythondata-software-compiler_rt.git
​sudo apt install meson
pip3 install git+https://github.com/litex-hub/pythondata-misc-tapcfg.git
sudo apt install verilator
event2
sudo apt install libevent-dev libjson-c-dev device-tree-compiler
pip3 install -i https://mirrors.aliyun.com/pypi/simple vcd
https://github.com/enjoy-digital/litex/issues/1986
FIXME:怎么离线安装这个东西（模拟器离线化）
(--sdram-data-width=32 --trace)
(not good, boot failed) litex_sim --with-sdram --sdram-init boot.json --cpu-type=vexriscv_smp
https://github.com/SpinalHDL/NaxRiscv/blob/1c50e84d9a6f7ea93d7153f12906c25552267b9d/src/main/scala/naxriscv/platform/litex/NaxGen.scala#L29
​如果用带参数的litex_sim会运行失败
但用litex-hub/linux-on-litex-vexriscv的sim.py就可以了

litex_sim --cpu-type=vexriscv
https://blog.csdn.net/vacajk/article/details/141282633
qemu sdram 4000_0000 ???
https://zhuanlan.zhihu.com/p/631811875

xubuntu20下用sim.py运行riscv rv32 linux的效果（可能是模拟在sdram中），非常卡，
大概需要半小时才能完全启动看到命令行。似乎不能用litex_sim，只能用sim.py
（参考litex-hub/linux-on-litex-vexriscv），
而且可能只支持模拟vexriscv_smp这个目标板的linux。
安装环境也很麻烦（我没用litex_setup），可能是基于verilator，
而且需要gcc去实时编译一些库，所以需要占用较大的硬盘
```
* (not good) litex_sim is installed by 'pip3 install litex'  
* sim.py is installed by https://github.com/litex-hub/linux-on-litex-vexriscv,  
put boot.json and other files to images/,    
see https://github.com/litex-hub/linux-on-litex-vexriscv/issues/164  
* refs  
```
litex, buildroot， litex_sim 模拟器
https://risc-v-getting-started-guide.readthedocs.io/en/latest/linux-avalanche.html
https://github.com/enjoy-digital/litex/wiki/Load-Application-Code-To-CPU
0001-Add-Litex-VexRiscv-support.patch
https://colatkinson.site/linux/riscv/2021/01/27/riscv-qemu/
https://www.qemu.org/docs/master/system/target-riscv.html​
https://yjdwbj.github.io/2021/10/12/XILINX-Arty-A7-35T实践指南/
https://www.rs-online.com/designspark/arty-100trisc-v-1-cn
make BSP=metal PROGRAM=hello TARGET=freedom-e310-arty software, see https://yjdwbj.github.io/2021/10/12/XILINX-Arty-A7-35T实践指南/
https://github.com/sifive/freedom-e-sdk
https://msyksphinz.hatenablog.com/?page=1542308400
https://github.com/eugene-tarassov/vivado-risc-v/blob/master/qemu/boot_qemu.sh
https://github.com/kalray/barebox/blob/main/Documentation/boards/riscv.rst​
https://github.com/litex-hub/linux-on-litex-vexriscv/blob/master/buildroot/board/litex_vexriscv/linux.config​
https://github.com/enjoy-digital/litex
https://github.com/timvideos/litex-buildenv​
https://github.com/enjoy-digital/litex/blob/master/litex/tools/litex_sim.py
https://github.com/litex-hub/linux-on-litex-vexriscv/blob/master/sim.py
https://github.com/enjoy-digital/litex/wiki
安装教程：wget https://raw.githubusercontent.com/enjoy-digital/litex/master/litex_setup.py
```

## xv6 lisp v6  
* https://gitlab.com/x653/xv6-riscv-fpga/-/blob/main/xv6-riscv/user/lisp.c?ref_type=heads  
* https://github.com/x653/xv6-riscv-fpga/blob/main/xv6-riscv/user/lisp.c  
* https://github.com/x653/xv6-riscv-fpga/blob/main/lisp/fib  
* https://github.com/x653/xv6-riscv-fpga/blob/main/lisp/1-examples  
* https://github.com/x653/xv6-riscv-fpga/blob/main/lisp/lisp.c  

## The RISC-V Reader: An Open Architecture Atlas  
* http://www.riscvbook.com/chinese/RISC-V-Reader-Chinese-v2p1.pdf  
* http://www.riscvbook.com  
* http://www.riscvbook.com/greencard-20181213.pdf  

## Computer Organization and Design RISC-V Edition, Edition 1  
* https://educate.elsevier.com/book/details/9780128122754  

## KianV  
* https://github.com/splinedrive/kianRiscV  
* https://github.com/splinedrive/kianRiscV/tree/master/archive/first_cpu  

## RISC-V  
* https://scrapbox.io/htkymtks/RISC-V  

## renode  
* https://github.com/renode/renode  

## LiteX 定制 SoC 上使用 C 和 Rust 嵌入式 (RISC-V)  
```
LiteX 定制 SoC 上使用 C 和 Rust 嵌入式 (RISC-V)
https://zhuanlan.zhihu.com/p/598689675
与本项目配套的设计文档《从零开始写RISC-V处理器》，已经更新完成。
https://gitee.com/liangkangnan/tinyriscv
https://gitee.com/xshadower/riscv-fpga-toolchain?_from=gitee_search
https://gitee.com/liangkangnan
https://gitee.com/liangkangnan/deep_in_riscv_debug
https://liangkangnan.gitee.io/2020/04/29/从零开始写RISC-V处理器/
```

## risc-v linux  
```
https://github.com/cyyself/cemu/blob/master/docs/riscv64-linux.md
构建RISC-V工作环境
https://twd2.me/archives/13406
https://www.cnx-software.com/2018/03/16/how-to-run-linux-on-risc-v-with-qemu-emulator/
pk=bbl, like opensbi

其实都不用我研究，直接就有一篇文章讲怎么用模拟器（不过是用qemu而不是spike）
运行risc-v linux：《构建RISC-V工作环境》（出自twd2.me，Wandai Blog）
。它的思路和MIPS不太一样，需要先编译linux kernel内核文件，
然后用opensbi或pk在编译的时候通过payload参数包住这个内核，
最后用qemu -kernel参数启动bbl(=pk)或opensbi这个bootloader（内容已经包括了linux内核）。
然后我看了这篇的引用文章才发现pk（proxy kernel）
其实就是bbl，即Berkeley Boot Loader，
就是说pk=bbl，对标于opensbi
see https://github.com/michaeljclark/busybear-linux


【搞linux的旺仔】:qemu上如何启动linux，并模拟出一块linux开发板的显示屏，在显示屏上运行图形化界面？
https://www.bilibili.com/video/BV13u4y1o7v6/
risc-v linux qemu

https://github.com/michaeljclark/rv8
RISC-V simulator for x86-64
https://comparch.edu.cvut.cz/qtrvsim/app/
https://github.com/cvut/qtrvsim
qtrvsim



https://gitee.com/xiaowuzxc/SparrowRV?_from=gitee_search

ch32 uclinux?
https://github.com/tvlad1234/linux-ch32v003
https://github.com/cnlohr/mini-rv32ima
https://github.com/cnlohr/ch32v003fun
https://github.com/tvlad1234/pico-rv32ima
https://hackaday.com/2023/03/19/rp2040-runs-linux-through-risc-v-emulation/
https://hackaday.com/2024/03/03/bring-linux-to-ch32v003-through-yes-risc-v-emulation/
https://github.com/owlvisiontech/rv1109-rv1126
ASMI
一个mips IDE
search baidupan, ASMI-master.zip
https://download.csdn.net/download/weixin_42118423/18482921

自制RISC-V处理器可视化运行Linux
https://www.bilibili.com/video/BV1rt421h7WV
https://github.com/LoveLonelyTime/Bergamot


```

## FemtoRV  
```
FemtoRV
https://github.com/BrunoLevy/learn-fpga
基于STM32+iCE40的电赛训练平台完成RISC-V软核的移植
https://www.eetree.cn/project/detail/1510
https://github.com/mortbopet/Ripes
RISC-V处理器的设计与实现（一）—— 基本指令集
https://blog.csdn.net/qq_51103378/article/details/131201501
==
https://twitter.com/Ruinland_Mask/status/1341761899650662400
https://github.com/Lingrui98/RISC-V-book
https://riscv.org/technical/specifications/
WebRISC-V
https://github.com/Mariotti94/WebRISC-V
https://webriscv.altervista.org
```

## risc-v linux emulator   
```
我试验过，现在用ubuntu22应该比较容易跑mit-pdos/xv6-riscv了，不需要自己编译或者找riscv工具链，
方法是sudo apt install gcc-riscv64-unknown-elf libnewlib-dev qemu-system-misc即可。
但ubuntu22跑mit-pdos/xv6-public估计不行，要用32位比较好，例如ubuntu14

https://github.com/xhackerustc/uc-rv32ima

linux-ch32v003_mingw_v2.7z
跑通了mingw上
https://github.com/tvlad1234/linux-ch32v003
port mini-rv32ima.h
记录到wmt_uclinux_study
https://github.com/weimingtom/wmt_simulator_study
???
https://github.com/Its-Jakey/mini-rv32ima-Java
https://github.com/cnlohr/mini-rv32ima-images/tree/master/images
???
https://github.com/ElectroBoy404NotFound/ESP32-rv32-emu
juicevm
https://whycan.com/t_6899.html
https://github.com/juiceRv/JuiceVm
https://github.com/juiceRv/kernel_juicevm_port

https://github.com/cnlohr/mini-rv32ima  
有大量关于risc-v编译的资料
cd mini-rv32ima
make testdlimage
./mini-rv32ima -f DownloadedImage




temu
https://github.com/Low-Speed-Linux-Experimental-Platform/temu
https://www.bilibili.com/video/BV1s2421P7rZ  
据我所知已经有至少三个开源（JuiceVm闭源）项目研究rv32ima linux模拟器的了，
cnlohr/mini-rv32ima
和Low-Speed-Linux-Experimental-Platform/temu
还有juiceRv/JuiceVm。
我也想研究，但目前没有头绪，虽然没什么用，
但似乎可以节省买开发板的钱




(IMP)
linux-ch32v003_v4_good_no_systick.7z
tvlad1234/linux-ch32v003研究，我暂时做了一个可以命令行输入的版本，不过不完美（还是没有实现真实的systick），
不过即便没有systick可以用步进加1的方法模拟systick（我这里模拟器循环每步加5微秒），
这样顺便也解决了大概率会没日志或内核panic的问题（原因不明，我加了模拟systick就没事了）。
这个效果非常卡，输入一个字符要等几秒，执行ls也是会缓慢输出，有点类似uclinux那种每行输出卡顿一下的感觉，
但卡顿得更厉害了
==
tvlad1234/linux-ch32v003研究，这个开源项目代码有点玄乎，我怀疑不同gcc可能也会影响实际的运行效果。
我只有备份某个修改版本且尽在特定情况下才能跑出波浪线井号的正确效果
（不确定条件；用MounRiver Studio编译，但系统嘀嗒为0所以无法输入），
假如我进行代码微调，或者未知条件下，就会出现各种失败情况，例如没有日志，或者内核panic，
但我找不到具体的规律在哪
==
tvlad1234/linux-ch32v003研究，现在可以勉强跑起来，从psram加载完内核镜像到第一行linux日志输出需要15秒，
从psram加载完内核到出现命令行提示符（波浪线井号）需要5分钟，非常慢。
之前的spi共用问题似乎可以通过面包板最邻近插孔解决（不要隔着空位接线）防止产生电阻干扰读写。
暂时还没完全跑通，还差系统时钟的移植代码有问题，所以目前无法测试命令行输入
（日志前面的秒数全是0是错的）
==
（TODO：接线方法未记录）
CH32V003F4P6-R0-1v1 green board：
右上GND<->WCHLinkE-GND
右上第二列VCC<->WCHLinkE-3V3
PD5(TX)<->WCHLinkE-RX
PD6(RX)<->WCHLinkE-TX
PD1(SWIO)<->WCHLinkE-SWDIO/TMS
SD板子（Green board TF+SD==SD/TF Board V1.6.2 use TF）：
PC0<->SD-/CS
面包板（SOP8 ESP-PSRAM64H）：
左下GND<->SD-GND<->ESP-PSRAM-4
右上第一列VCC（避免电阻）<->SD-3.3V<->ESP-PSRAM-8
PC7<->SD-MISO<->ESP-PSRAM-2
PC6<->SD-MOSI<->ESP-PSRAM-5
PC5<->SD-CLK<->ESP-PSRAM-6
PD3<->ESP-PSRAM-1-/CS
注意插面包板（例如三脚连在一起）不要隔孔位，尽量贴近孔位插线，
以防止读写psram和tf卡数据有误
==
tvlad1234/linux-ch32v003研究，我大概总结出这样的规律：
最好VCC供电脚是相邻的，
例如引到面包板的VCC和引到WCH-LinkE的VCC的CH32V003F4P6-R0-1v1绿色板的VCC是相邻的，
否则可能会导致启动失败（电阻干扰PSRAM读写？）。
另外我测试过信泰微的蓝色TF转接板不能用（带AMS1117-3.3和一个14脚chip），
但另一家的绿色SD/TF双用转接板和单tf转接板却可以，
费解，可能明确标记3.3V的tf转接板比较可靠



https://github.com/ElectroBoy404NotFound/pico-linux
https://github.com/ElectroBoy404NotFound/pico-uMIPS/tree/main
https://dmitry.gr/?r=05.Projects&proj=33.%20LinuxCard
https://github.com/ElectroBoy404NotFound/uMIPS/tree/main
https://github.com/ElectroBoy404NotFound/uARM
https://github.com/ElectroBoy404NotFound/BluePill-RV32IMA
https://github.com/drorgl/esp32-tinyemu

tinyemu用法：risc-v模拟器，类似pk,spike
https://bellard.org/tinyemu/
https://bellard.org/tinyemu/buildroot.html
* temu buildroot-riscv32.cfg
* Log in as root with an empty password.
search baidupan, tinyemu-2019-12-21.tar
tinyemu在xubuntu22上的编译运行效果如下。虽然tinyemu名字叫tiny但编译需要用到
sudo apt install libcurl4-openssl-dev libsdl1.2-dev。
另外固件是预先提供的（root固件编译好但buildroot需要自己编译），
所以实际上只能这样：./temu ../diskimage-linux-riscv-2018-09-23/root-riscv32.cfg，
并且这份代码已经较长时间没更新了




（IMP）TODO
search baidupan, work_rv32
asfdrwe_ASFRV32I-master.zip
ASFRV32I的可运行版本：iverilog_windows_v1.rar
我研究软核的计划是这样：软件用iverilog模拟，硬件用tang nano 20k模拟。
iverilog我以前试过这个简单软核是可运行的：asfdrwe/ASFRV32I。
我希望以此为基础尝试跑通一个baremetal程序，更进一步看能否跑通linux内核
（可能有人做这方面的尝试，但可能并不多或者没有）。
如果实在不行只能用tang nano 20k一步一步搭建了
https://github.com/asfdrwe/ASFRV32I
https://github.com/riscv-software-src/riscv-tests
(TODO) 用iverilog输出串口linux
https://twitter.com/regymm0/status/1629501775828123649
https://github.com/regymm/quasiSoC/tree/master
https://github.com/regymm/quasiSoC/blob/master/sim/sim_main.cpp
https://github.com/regymm/quasiSoC/blob/master/Simulation.md
转推，有人尝试过用iverilog或类似的方法（Verilator？）
模拟FPGA软核运行linux成功，我觉得这个思路可以尝试，
至少有人成功运行起来。它说的8M RAM就能运行也和tvlad1234/linux-ch32v003的模拟情况是一致的
https://dmitry.gr/?r=05.Projects&proj=07.%20Linux%20on%208bit
github.com/xniine/rocket-integration



xv6 fpga
https://github.com/x653/xv6-riscv-fpga
https://twitter.com/splinedrive/status/1786847131157893311
https://gitlab.com/x653/xv6-riscv-fpga/-/tree/main?ref_type=heads
https://gitee.com/weimingtom2000/xv6-riscv-fpga
(TODO)
关于x653/xv6-riscv-fpga，2023年的时候有人用此项目成功在FPGA上用RISC-V软核（RV32ia zicsr）运行xv6。
虽然还有Linux和一些RTOS也支持risc-v，不过xv6原本不是适配RV32的，这里似乎改动过，
而且xv6原本基于qemu模拟运行，其实可以简化（qemu过于重量级），
所以这个项目有研究的价值，待考未实际试验


```

## (not tested) HPMicro rv32 nommu linux, maybe hpm6800evk  
* https://github.com/hpmicro/linux/blob/hpmicro/arch/riscv/configs/nommu_hpmicro_defconfig
* CONFIG_PAGE_OFFSET=0x40000000
* https://www.hpmicro.com/service-support/technical-articles/258
* 基于OpenSBI的linux nommu实现
* https://github.com/hpmicro/opensbi.git
* https://github.com/hpmicro/hpm_opensbi_loader.git
* https://github.com/hpmicro/buildroot.git
* https://github.com/hpmicro/hpm_manufacturing_tool.git

## qemu 
* https://risc-v-getting-started-guide.readthedocs.io/en/latest/linux-qemu.html
```
git clone https://github.com/qemu/qemu
cd qemu
git checkout v5.0.0
./configure --target-list=riscv32-softmmu
```

## Gowin E203 RV32  
* https://www.gowinsemi.com.cn/prodshow.aspx?TypeId=70&FId=t31:70:31#top  
* https://github.com/SI-RISCV/e200_opensource
* HumingBird-GW2A.zip

## Building up a RISC-V Linux with Buildroot, rv32  
* https://jborza.com/post/2020-04-08-riscv-environment/  
* https://github.com/jborza/riscv-linux/tree/emuriscv-linux-4.15  
* https://github.com/jborza/emuriscv/  
