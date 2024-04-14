新增了对交换芯片RTL8367S的支持，相关的机型为以MTK7620A/DA为主控外接交换芯片实现千兆有线的水星D12G，Tplink C5 v4等等  
源码来自于https://gitlab.com/dm38/padavan-ng  
相关提交为https://gitlab.com/dm38/padavan-ng/-/commit/4ec2acb96dccc268ec23aa71b8f5fcb283b9e122  
  
新增了对xtls的支持，源码来自于xumng123  
https://github.com/xumng123/rt-n56u  
感谢vb1980持续对代码做出的测试和改进，他在原本的基础上新增了对xray的编译，而我之前就直接替换了v2ray的bin，所以是殊途同归啦  
https://github.com/vb1980/Padavan-KVR  
  
# Padavan
基于hanwckf,chongshengB以及padavanonly的源码整合而来，支持kvr  
编译方法同其他Padavan源码，主要特点如下：  
1.采用padavanonly源码的5.0.4.0无线驱动，支持kvr  
2.添加了chongshengB源码的所有插件  
3.其他部分等同于hanwckf的源码，有少量优化来自immortalwrt的padavan源码  
4.添加了MSG1500的7615版本config  
  
以下附上他们四位的源码地址供参考  
https://github.com/hanwckf/rt-n56u  
https://github.com/chongshengB/rt-n56u  
https://github.com/padavanonly/rt-n56u  
https://github.com/immortalwrt/padavan  

已测试的机型为MSG1500-7615，JCG-Q20，CR660x  

# 默认配置
```
wifi名称：设备名称_MAC后四位
wifi密码：password
管理地址：192.168.32.1
管理账号密码：admin admin
```

# 编译说明

* 安装依赖包

```shell
# Debian/Ubuntu
sudo apt update
sudo apt install unzip libtool-bin curl cmake gperf gawk flex bison nano xxd \
	fakeroot kmod cpio git python3-docutils gettext automake autopoint \
	texinfo build-essential help2man pkg-config zlib1g-dev libgmp3-dev \
	libmpc-dev libmpfr-dev libncurses5-dev libltdl-dev wget libc-dev-bin

# Archlinux/Manjaro
sudo pacman -Syu --needed git base-devel cmake gperf ncurses libmpc \
        gmp python-docutils vim rpcsvc-proto fakeroot cpio help2man

# Alpine
sudo apk add make gcc g++ cpio curl wget nano xxd kmod \
	pkgconfig rpcgen fakeroot ncurses bash patch \
	bsd-compat-headers python2 python3 zlib-dev \
	automake gettext gettext-dev autoconf bison \
	flex coreutils cmake git libtool gawk sudo

# CentOS 7
sudo yum update
sudo yum groupinstall "Development Tools"
sudo yum install ncurses-* flex byacc bison zlib-* texinfo gmp-* mpfr-* gettext \
	libtool* libmpc-* gettext-* python-docutils nano help2man fakeroot

# CentOS 8
sudo yum update
sudo yum groupinstall "Development Tools"
sudo yum install ncurses-* flex byacc bison zlib-* gmp-* mpfr-* gettext \
	libtool* libmpc-* gettext-* nano fakeroot

# CentOS 8不能直接通过yum安装texinfo，help2man，python-docutils。请去官网下载发行的安装包编译安装
# 以texinfo为例
# cd /usr/local/src
# sudo wget http://ftp.gnu.org/gnu/texinfo/texinfo-6.7.tar.gz
# sudo tar zxvf texinfo-6.7.tar.gz
# cd texinfo-6.7
# sudo ./configure
# sudo make
# sudo make install

```

* 克隆源码

```shell
git clone --depth=1 https://github.com/RikudouPatrickstar/padavan-fusion.git /opt/rt-n56u
```

* 准备工具链

```shell
cd /opt/rt-n56u/toolchain-mipsel

# （推荐）从源码编译工具链，这需要一些时间：
./clean_toolchain
./build_toolchain

# 或者，也可以使用脚本下载预编译的工具链：
sh dl_toolchain.sh

```

* 指定要编译的机型，例如
```shell
device_name=K2P
```

* (可选) 修改机型内核配置文件

```shell
cp -f /opt/rt-n56u/trunk/configs/boards/${device_name}/kernel-3.4.x.config /opt/rt-n56u/trunk/linux-3.4.x/.config
make -C /opt/rt-n56u/trunk/linux-3.4.x menuconfig
mv -f /opt/rt-n56u/trunk/linux-3.4.x/.config /opt/rt-n56u/trunk/configs/boards/${device_name}/kernel-3.4.x.config
```

* (可选) 修改机型配置文件

```shell
vim /opt/rt-n56u/trunk/configs/templates/${device_name}.config
```

* 开始编译

```shell
cd /opt/rt-n56u/trunk
# 对于WSL环境，建议使用sudo进行编译，或者使用fakeroot-tcp代替fakeroot
fakeroot ./build_firmware_modify ${device_name}
# 脚本第一个参数为路由型号，在trunk/configs/templates/中
# 编译好的固件在trunk/images里
# 首次编译完成后，如果需要再次编译其它固件，需要执行清理脚本：
./clear_tree
```
