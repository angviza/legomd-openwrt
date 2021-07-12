# Actions-OpenWrt

[Read the details in my blog (in Chinese) | 中文教程](https://p3terx.com/archives/build-openwrt-with-github-actions.html)

```
master是coolsnowwolf/lede编译.
自定义文件 “files 大法”是把你自定义的配置编译到固件里。这样升级或恢复出厂设置都不需要保留配置，缺省值就是自定义的配置。
如你现在的network设置编译进固件：首先提取路由固件下的\etc\config\network 然后在项目根目录下创建files目录并push 到 \files\etc\config\network ，最后编译出来的固件就是现在设置的network。
通过修改diypart1.sh文件修改feeds.conf.default配置。默认添加fw876/helloworld

通过修改diypart2.sh文件可以自定义默认IP，登陆密码等。按我的需要现在的默认IP为192.168.1.11,不需要更改的加#注释就可以。

自定义编译的方法可以搭配使用，自己需要的服务一般不会随意变化，就可以在 make menuconfig 选好（新手参考OpenWrt MenuConfig设置和LuCI插件选项说明）后执行 ./scripts/diffconfig.sh > seed.config 复制一下这个seed.config的文本内容到项目根目录的.config文件中（建议自命名），这样就不用每次都SSH连接到 Actions生成编译配置，真正一键编译。

修改.github/workflows/build-openwrt.yml中.config为你的自命名###.config文件。

另外如果，使用“files 大法”仓库最好设为私有，否则你的配置信息，如宽带账号等会公开在网上。

如果需要可以编写多个workflows文件对应###.config，开启多流程同时编译。

```

## 编译命令如下:

1. 首先装好 Ubuntu 64bit，推荐 Ubuntu 20.04 LTS x64

2. 命令行输入 `sudo apt-get update` ，然后输入 `sudo apt-get -y install build-essential asciidoc binutils bzip2 gawk gettext git libncurses5-dev libz-dev patch python3 python2.7 unzip zlib1g-dev lib32gcc1 libc6-dev-i386 subversion flex uglifyjs git-core gcc-multilib p7zip p7zip-full msmtp libssl-dev texinfo libglib2.0-dev xmlto qemu-utils upx libelf-dev autoconf automake libtool autopoint device-tree-compiler g++-multilib antlr3 gperf wget curl swig rsync`

3. 使用 `git clone https://github.com/coolsnowwolf/lede` 命令下载好源代码，然后 `cd lede` 进入目录

4. ```
   ./scripts/feeds update -a
   ./scripts/feeds install -a
   make menuconfig
   ```

5. `make -j8 download V=s` 下载dl库（国内请尽量全局科学上网）

6. 输入 `make -j1 V=s` （-j1 后面是线程数。第一次编译推荐用单线程）即可开始编译你要的固件了。

本套代码保证肯定可以编译成功。里面包括了 R21 所有源代码，包括 IPK 的。

# 你可以自由使用，但源码编译二次发布请注明我的 GitHub 仓库链接。谢谢合作！

二次编译：

```
cd lede
git pull
./scripts/feeds update -a && ./scripts/feeds install -a
make defconfig
make -j8 download
make -j$(($(nproc) + 1)) V=s
```

如果需要重新配置：

```
rm -rf ./tmp && rm -rf .config
make menuconfig
make -j$(($(nproc) + 1)) V=s
```

编译完成后输出路径：bin/targets
