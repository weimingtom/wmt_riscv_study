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

## xubuntu 22 (don't use 20) + linux-6.1.14 + qemu-system-riscv64 -M virt    
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
https://github.com/camel-cdr/rvv-bench/issues/8  
cd riscv64-elf-x86_64-20201104/bin
``` 
（来源于melis的工具链）  
./riscv64-unknown-elf-gcc -march=rv64gcv -O2 -static -o hello2 hello.c  
(一定要加-O2，算pi太慢。因为T-HEAD的工具链，所以会变成rv64gcxthead，所以要指定march）  
cp hello2 ../../riscv-isa-sim/ci-test2  
/home/wmt/spike/bin/spike --isa=rv64gcv pk hello2  

（注：下文中的xubuntu 20最好改用xubuntu 22）

spike linux研究。研究了很久勉强可以把spike跑起来
（1）编译spike，代码库是：riscv-software-src/riscv-isa-sim。
我用xubuntu 2004编译，没有报错，但编译出来有两三百M比较大
（2）获取pk。这个轻量级内核可以不用自己编译，在riscv-isa-sim的发布页面中有预编译
（3）获取工具链，我是用平头哥T-Head的非标准交叉工具链：
Tina-Linux/d1s-melis的riscv64-elf-x86_64-20201104
（4）编译hello，因为工具链非标准需要强制用标准架构，
否则spike会出现Illegal instruction错误：
./riscv64-unknown-elf-gcc -march=rv64gcv -O2 -static -o hello2 hello.c
（5）运行：需要指定isa或者忽略：spike --isa=rv64gcv pk hello2
（6）备注，可能-march的架构不用V也可以，V可能是向量特性的意思
search baidupan, xubuntu200464_spike_bakcup
```





