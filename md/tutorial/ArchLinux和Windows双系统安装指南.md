## 1 安装

### 1.1 制作启动u盘

1. 插入u盘
2. 打开文件管理器
3. 右键点击此电脑 -> 点击管理
4. 点击磁盘管理
5. 选择需要分出空间的盘，右键点击 -> 压缩卷，容量自行决定，建议大于80GB
6. 下载ventoy，ventoy使用自行百度、Google、Bing
7. [下载ArchLinux](https://archlinux.org/download/) 镜像
8. 将ArchLInux镜像放入u盘中
9. 重启电脑
10. 狂按F2进入Bios（进入Bios的快捷键各品牌电脑有差异，自行查阅，我这是华硕）
11. 关闭windows安全检查
12. 设置启动顺序为u盘第一

### 1.2 检查网络

1. 连接网络

   - 无线网络

     1. ```bash
        iwctl
        ```

     2. 进入iwd模式，输入

        ```bash
        device list
        ```

     3. 查看你的网卡名字，这里假设是wlan0，输入

        ```bash
        station wlan0 scan
        ```

     4. 检查扫描网络，输入

        ```bash
        station wlan0 get-networks
        ```

     5. 查看网络名字，假设名字叫BUPT-portal，输入

        ```bash
        station wlan0 connect BUPT-portal
        ```

     6. 接着输入密码（如果有密码的话），输入，退出iwd模式

        ```bash
        exit
        ```

   - 有线网络

     插上网线就行，如果需要认证则要用另一台能用无线网的电脑当交换机

2. 连接成功之后，检查可以连接到pacman源

   ```bash
   pacman -Syyy
   ```

3. 重新设置mirrorlist（可选，建议）：

   ```bash
   reflector -c China -a 6 --sort rate --save /etc/pacman.d/mirrorlist
   ```

### 1.3 硬盘

1. 检查硬盘

   ```bash
   lsblk
   ```

   ![img](https://pic3.zhimg.com/80/v2-202971d782206ee00df4894348acf2b6_720w.webp)

   这里没有看到之前划分好的空间，不要慌，那是因为之前只是划了空间，并没有建立分区

2. 建立分区

   因为之前划好的空间在nvme0n1上，所以执行

   ```bash
   cfdisk /dev/nvme0n1
   ```

   这里以你个人情况而定 sda sdb 或是其他的

   ![img](https://pic2.zhimg.com/80/v2-49855cb185cf06755485c570310f0af1_720w.webp)

   这里我们就能看到最后有250G的Free Space

   ![img](https://pic2.zhimg.com/80/v2-d254c8449f527d10e724fe7b0c5cd465_720w.webp)

   选择New 回车

   ![img](https://pic2.zhimg.com/80/v2-beb16fd18d81dc69d910bc0f9578cacd_720w.webp)

   这里就输入250G 回车

   ![img](https://pic1.zhimg.com/80/v2-4c370c9718b302ed0774c8f2a9faad44_720w.webp)

   选择Write 回车 输入 yes 回车

   写入完成 选择Quit 回车退出

   检查分区情况

   ```bash
   lsblk
   ```

   ![img](https://pic1.zhimg.com/80/v2-553670cd4ee4b584a616078bc750c948_720w.webp)

   建立分区之后就可以看到分好的250G区

3. 分区格式化

   将刚刚分好的区格式化为ext4格式，这里认准分区号是nvme0n1p5

   ```bash
   mkfs.ext4 /dev/nvme0n1p5
   ```

   ![img](https://pic4.zhimg.com/80/v2-311d75a2b56ac97898484f449341009b_720w.webp)

4. 挂载分区

   先挂载/分区，同样，这里分区号也是nvme0n1p5

   ```bash
   mount /dev/nvme0n1p5 /mnt
   ```

   这里利用Windows的EFI分区，检查EFI分区号

   ```bash
   lsblk
   ```

   ![img](https://pic2.zhimg.com/80/v2-dcb17539e1c3938c74b377a65526001d_720w.webp)

   这里可以看到是nvme0n1p2

   建立boot文件夹

   ```bash
   mkdir /mnt/boot
   ```

   挂载EFI分区

   ```bash
   mount /dev/nvme0n1p2 /mnt/boot
   ```

   ![img](https://pic3.zhimg.com/80/v2-265d33a99301d176fae47b6343311c1e_720w.webp)

### 1.4 安装基本系统

执行

```bash
pacstrap /mnt base linux linux-firmware nano
```

等待安装完毕

（如果你不想用默认的内核，也可以使用linux-lts, linux-zen, linux-hardened，具体介绍请看[Wiki](https://wiki.archlinux.org/title/kernel)

### 1.5 生成fstab文件

```bash
genfstab -U /mnt >> /mnt/etc/fstab
```

检查生成的fstab文件

```bash
cat /mnt/etc/fstab
```

![img](https://pic4.zhimg.com/80/v2-bb67b920ff61eb7ea40d8c073bd15857_720w.webp)

如图说明一切正确

### 1.6 正式配置新系统

1. 切换到装好的系统

   ```bash
   arch-chroot /mnt
   ```

2. 建立swapfile（建议，没有swap空间无法休眠）

   > 在 ext4 上使用 swapfile 的用户请注意，升级到 5.7.x 内核后可能出现诸如「kernel: swapon: swapfile has holes」这样的报错而无法启用 swapfile 。使用 dd 命令创建 swapfile （而非 fallocate） 可能可以解决问题，也可以回退 5.6 系列内核等待上游修复。
   > Arch Linux 错误跟踪：[https://bugs.archlinux.org/task/66921](https://link.zhihu.com/?target=https%3A//bugs.archlinux.org/task/66921)
   > 内核错误跟踪：[https://bugzilla.kernel.org/show_bug.cgi?id=207585](https://link.zhihu.com/?target=https%3A//bugzilla.kernel.org/show_bug.cgi%3Fid%3D207585)

   如果之前安装的内核是linux-lts：

   ```bash
   fallocate -l 2GB /swapfile
   ```

   注意：命令中是 **小写字母l** 而非 数字1 也非 字母i的大写

   如果之前安装的内核不是linux-lts，这里创建swapfile需要使用dd命令：

   ```bash
   dd if=/dev/zero of=/swapfile bs=2048 count=1048576 status=progress
   ```

   这里分了2G作为swapfile

   改权限

   ```bash
   chmod 600 /swapfile
   ```

   建立swap空间

   ```bash
   mkswap /swapfile
   ```

   激活swap空间

   ```bash
   swapon /swapfile
   ```

   修改/etc/fstab文件

   ```bash
   nano /etc/fstab
   ```

   到文件末尾输入

   ```bash
   /swapfile none swap defaults 0 0
   ```

   ![img](https://pic4.zhimg.com/80/v2-32f9a86a9806695b1a4c6d94c79d25e3_720w.webp)

   保存退出

3. 设置时区

   ```bash
   ln -sf /usr/share/zoneinfo/Asia/Shanghai /etc/localtime
   ```

   或

   ```bash
   timedatectl set-timezone Asia/Shanghai
   ```

   同步硬件时钟

   ```bash
   hwclock --systohc
   ```

4. 设置locale

   ```bash
   nano /etc/locale.gen
   ```

   Ctrl+W 输入 #en_US 回车 找到UTF-8那一行 删掉前面的#（取消注释）

   Ctrl+W 输入 #zh_CN 回车 找到UTF-8那一行 删掉前面的#（取消注释）

   保存退出

   生成locale

   ```bash
   locale-gen
   ```

   创建并写入/etc/locale.conf文件

   ```bash
   nano /etc/locale.conf 
   ```

   填入内容，注意这里只能填这个

   ```bash
   LANG=en_US.UTF-8
   ```

5. 创建并写入hostname

   ```bash
   nano /etc/hostname
   ```

   这里我写入的是 arch 作为hostname，你也可以输别的

   ![img](https://pic2.zhimg.com/80/v2-c6f62d0bf545ebaa952a1746cd7668ad_720w.webp)

   保存退出

6. 修改hosts

   ```bash
   nano /etc/hosts
   ```

   写入内容如图（中间的空白用tab而非空格），arch替换为你之前在hostname里写入的内容，其他都按照图里面的写（注意最后一行的ip是127.0.1.1）

   ![img](https://pic3.zhimg.com/80/v2-54fc38004dc15b76d68983a78e59928a_720w.webp)

   保存退出

   建议上述编辑的内容都用cat输出检查一下

7. 为root用户创建密码

   ```bash
   passwd
   ```

   然后输入并确认密码（linux终端的密码没有回显，输完直接回车就好）

8. 创建启动器

   安装基本的包，这里使用grub为启动器

   ```bash
   pacman -S grub efibootmgr networkmanager wget yakuake unzip network-manager-applet dialog wireless_tools wpa_supplicant os-prober mtools dosfstools ntfs-3g base-devel linux-headers reflector git sudo
   ```

   **如果你不知道这些包的作用，请务必确保输入的指令与上面的一致**

   检查完毕回车，需要选择直接回车就好，等待安装结束

   如果你是intel的cpu，需要安装intel的微码文件

   ```bash
   pacman -S intel-ucode
   ```

   如果是amd

   ```bash
   pacman -S amd-ucode
   ```

   ------

   2021.06.16更新：

   > Grub 2.06 更新 os-prober 用户需要手动干预
   >
   > grub 2.06 更新已经进入官方源，本次更新有以下两个需要注意的变化：
   > \1. 如果您正在使用 os-prober 生成其他系统的引导项，grub 2.06 不再自动启用 os-prober，您需要添加 GRUB_DISABLE_OS_PROBER=false 至 /etc/default/grub 配置文件中并且重新运行 grub-mkconfig
   > \2. grub 2.06 现在会自动添加 固件设置菜单 引导项目，无需手动创建

   鉴于此需要手动启用os-prober来确保Windows能被正确识别：

   输入

   ```bash
   nano /etc/default/grub
   ```

   在里面找一条空行输入

   ```bash
   GRUB_DISABLE_OS_PROBER=false
   ```

   之后Ctrl-X 加Y回车保存退出

   ![img](https://pic4.zhimg.com/80/v2-405f1485261b0213aceef88ca833909b_720w.webp)

   完成之后输入

   ```bash
   grub-install --target=x86_64-efi --efi-directory=/boot --bootloader-id=Arch
   ```

   确保输入指令完全正确回车

   ![img](https://pic3.zhimg.com/80/v2-9a43ea86f2ab93b70b53549d3ee84c52_720w.webp)

   生成grub.cfg

   ```bash
   grub-mkconfig -o /boot/grub/grub.cfg
   ```

   完成之后如图

   ![img](https://pic3.zhimg.com/80/v2-de2e93e62d337ba61654e02001a1f226_720w.webp)

### 1.7 退出新系统并取消挂载

```bash
exit
```

![img](https://pic2.zhimg.com/80/v2-240ca9989b005714cdeaf20e85c7340d_720w.webp)

```bash
umount -a
```

![img](https://pic1.zhimg.com/80/v2-e87ae5d5e79da0247e93cf2e19e3bc50_720w.webp)

重启

```bash
reboot
```

启动时请拔出u盘

### 1.8 进入装好的Arch系统并激活网络

进去之后 先输入 root 回车 输入密码 回车

启动网络服务

```text
systemctl enable --now NetworkManager
```

设置WiFi

```text
nmtui
```

![img](https://pic4.zhimg.com/80/v2-3be8707847310be9f8f8d86de0799c8f_720w.webp)

回车

![img](https://pic1.zhimg.com/80/v2-8e0546e1ea798ec7f146f4a05924e28c_720w.webp)

选择你要连接到的WiFi 输入密码 回车 然后退出

### 1.8 新建用户并授权

```text
useradd -m -G wheel mir
```

wheel后面是你的用户名，这里输入的是mir

为用户创建密码

```text
passwd mir
```

输入并确认密码

授权

```text
EDITOR=nano visudo
```

Ctrl+W 输入 # %wheel 回车 找到这行 删除前面的 #（取消注释）

![img](https://pic1.zhimg.com/80/v2-8aaf436214afc9aaefbee330f96f4690_720w.webp)

保存退出

### 1.9 安装显卡驱动

这里需要按你要安装的桌面环境而定，这里没有列出的可以自己去ArchWiki查

Gnome：

```text
pacman -S gdm
```

KDE：

```text
pacman -S sddm
```

Xfce || DDE：

```text
pacman -S lightdm lightdm-gtk-greeter
```

设置开机自动启动，以gdm为例：

```text
systemctl enable gdm
```

如果是别的请将这里的gdm替换为你安装的那个dm

### 1.10 安装Desktop Environment

Gnome：

```text
pacman -S gnome
```

KDE：

```text
pacman -S plasma kde-applications packagekit-qt5
```

Xfce：

```text
pacman -S xfce4 xfce4-goodies
```

DDE：

```text
pacman -S deepin deepin-extra
```

同样 需要选择时直接回车

### 1.11 添加archlinuxcn源

```text
nano /etc/pacman.conf
```

在最后加上下面两行（我这里使用了清华的镜像站）

```text
[archlinuxcn]
SigLevel = TrustAll
Server = https://mirrors.tuan.tsinghua.cn/archlinuxcn/$arch
```

同时取消对multilib源的注释

![img](https://pic3.zhimg.com/80/v2-5519f934341533ce81fdff7259b0e9d6_720w.webp)

保存退出之后同步并安装archlinuxcn-keyring

```text
pacman -Syu && pacman -S archlinuxcn-keyring
```

最后不要忘记安装中文的字体，如果这一步不装进去图形界面之后还是要装：

```text
pacman -S ttf-sarasa-gothic noto-fonts-cjk
```

我这里安装的是更纱黑体和noto cjk，包比较大，耐心等待安装完毕。

最后重启

```text
reboot
```

在grub界面选择archlinux回车

当你看到登录界面时，恭喜你，一个相对完整的Arch安装完毕，Enjoy it！

------

进一步配置可以看看专栏前面那篇Manjaro-KDE的配置，装点常用的软件，大体上就能用了，然后根据自己的情况配配显卡驱动，就差不多能玩游戏了

关于N卡的启用与切换，建议使用[optimus-manager](https://link.zhihu.com/?target=https%3A//github.com/Askannz/optimus-manager)（其他方案我都试过不好使）

```text
sudo pacman -S optimus-manager
```

没有DE只有一个Bspwm 这是我的dotfiles：

[Dracula&Nord Dotfilesgithub.com/MiraculousMoon/bspwm-dotfiles.git](https://link.zhihu.com/?target=https%3A//github.com/MiraculousMoon/bspwm-dotfiles.git)

此外还有i3wm的：

[https://github.com/ayamir/i3-dotfilesgithub.com/ayamir/i3-dotfiles](https://link.zhihu.com/?target=https%3A//github.com/ayamir/i3-dotfiles)

dwm的：

[https://github.com/ayamir/dwm-dotfilesgithub.com/ayamir/dwm-dotfiles](https://link.zhihu.com/?target=https%3A//github.com/ayamir/dwm-dotfiles)

spectrwm，xmonad，sway的：

[https://github.com/ayamir/nord-and-lightgithub.com/ayamir/nord-and-light](https://link.zhihu.com/?target=https%3A//github.com/ayamir/nord-and-light)



## 2 配置

### 2.1 自动挂载Windows盘

1. 需要先安装ntfs-3g

   ```bash
   sudo pacman -S ntfs-3g
   ```

2. 列出硬盘列表

   ```bash
   fdisk -l
   ```

   ![image-20221117213540562](https://zergqueen.gitee.io/images/myblog/Screenshot_20221117_213532.png)

3. 获取UUID

   ```bash
   ls -l /dev/disk/by-uuid
   ```

4. 编辑fstab

   ```bash
   vim /etc/fstab
   ```

   ```tex
   UUID=EE90575D90572B7B                       /run/media/zerg/Data    ntfs   rw,relatime,fmask=0022,dmask=0022,codepage=437,iocharset=ascii,shortname=mixed,utf8,errors=remount-ro   0 0 
   /swapfile                                   none                    swap   defaults                                                                                                0 0 
   
   UUID=3ECC3D18CC3CCBC1                       /run/media/zerg/Soft    ntfs   rw,relatime,fmask=0022,dmask=0022,codepage=437,iocharset=ascii,shortname=mixed,utf8,errors=remount-ro   0 0 
   /swapfile                                   none                    swap   defaults                                                                                                0 0 
   
   UUID=562EA87D2EA857AF                       /run/media/zerg/Win     ntfs   rw,relatime,fmask=0022,dmask=0022,codepage=437,iocharset=ascii,shortname=mixed,utf8,errors=remount-ro   0 0 
   /swapfile                                   none                    swap   defaults                                                                                                0 0 
   
   #UUID=f46791cb-0393-42a5-b0db-ecc30f1739a0                      /run/media/zerg/Ldata    ext4   rw,relatime,fmask=0022,dmask=0022,codepage=437,iocharset=ascii,shortname=mixed,utf8,errors=remount-ro   0 0
   #/swapfile                                   none                   swap   defaults                                                                                                0 0
   
   UUID=f46791cb-0393-42a5-b0db-ecc30f1739a0   /run/media/zerg/Ldata   ext4   defaults
   ```

   ![Screenshot_20221117_213835.png](https://zergqueen.gitee.io/images/myblog/Screenshot_20221117_213835.png)

   除了UUID、文件系统类型和挂载点其他都可以照抄

### 2.2 grub找不到windows引导

1. 编辑配置文件

   ```bash
   sudo vim /etc/default/grub
   ```

2. 加入一行

   ```bash
   GRUB_DISABLE_OS_PROBER="false"
   ```

3. 然后

   ```bash
   sudo grub-mkconfig -o /boot/grub/grub.cfg
   ```

### 2.3 双系统时间同步

```bash
sudo timedatectl set-local-rtc 1
```

### 2.4 配置静态ip

1. 安装dbcpcd

   ```bash
   sudo pacman -S dhcpcd
   ```

2. 配置

   ```bash
   vim /etc/dhcpcd.conf
   ```

   ```tex
   interface enp4s0 #网卡名称 ，可通过ip address获取
   static ip_address=192.168.xxx.xxx/24 #这里用CIDR的格式配置地址
   static routers=192.168.xxx.xxx #这里配置的是网关
   ```

### 2.5 支持彩色emoji

1. 安装字体

   ```bash
   sudo pacman -S noto-fonts-emoji                                     
   ```

2. 新建配置文件

   ```bash
   sudo vim /etc/fonts/local.conf
   ```

3. 写入配置文件

   ```tex
   <?xml version="1.0"?>
   <!DOCTYPE fontconfig SYSTEM "fonts.dtd">
   <fontconfig>
    <alias>
      <family>sans-serif</family>
      <prefer>
        <family>Noto Sans</family>
        <family>Noto Color Emoji</family>
        <family>Noto Emoji</family>
        <family>DejaVu Sans</family>
      </prefer> 
    </alias>
    
    <alias>
      <family>serif</family>
      <prefer>
        <family>Noto Serif</family>
        <family>Noto Color Emoji</family>
        <family>Noto Emoji</family>
        <family>DejaVu Serif</family>
      </prefer>
    </alias>
    
    <alias>
     <family>monospace</family>
     <prefer>
       <family>Noto Mono</family>
       <family>Noto Color Emoji</family>
       <family>Noto Emoji</family>
       <family>DejaVu Sans Mono</family>
      </prefer>
    </alias>
   </fontconfig>
   ```

4. 刷新

   ```bash
   fc-cache
   ```

5. 重启

### 2.6 配置VMware

1. Could not connect ‘Ethernet0‘ to virtual network ‘/dev/vmnet0‘

   ```bash
   sudo modprobe vmnet && sudo vmware-networks --start
   ```

   ```bash
   sudo systemctl start vmware-networks.service
   ```

2. 还是没有网络

   ![](https://img-blog.csdnimg.cn/a6d9e86bfee54768b8d2786860f5a139.png)

   ![](https://img-blog.csdnimg.cn/5497320a08284a0f826ddd0ab728babf.png)

   ![](https://img-blog.csdnimg.cn/e4df749a59ec4a75aca24a544fc0a278.png)



## 3 常用软件

### 3.1 安装输入法

> 以下输入法只能任选一安装

   - 搜狗输入法

     1. 安装

        ```bash
        yay -S fcitx fcitx-qt5 fcitx-configtool fcitx-qt4 qtwebkit fcitx-sogoupinyin
        ```

     2. 配置 ~/.pam_environment 并写入

        ```bash
        GTK_IM_MODULE DEFAULT=fcitx
        QT_IM_MODULE  DEFAULT=fcitx
        XMODIFIERS  DEFAULT=\@im=fcitx
        ```

     3. 重启

     4. 终端打开fcitx-configtool

     5. 点击 “+” 号，取消勾选 Only show current language，找到 Sogou Pinyin 并添加

        ![da27cf3193bad5a83d3942047bf4ba4c.png](https://cdn-img.limstash.com/images/2020/04/20/da27cf3193bad5a83d3942047bf4ba4c.png)

     

   - 百度输入法

     有bug，闪退，还会卡掉自带英文输入法

   - fcitx5

     1. 安装fcitx5相关包

        ```bash
        sudo pacman -S fcitx5 fcitx5-chinese-addons fcitx5-gtk fcitx5-qt kcm-fcitx5 fcitx5-material-color
        ```

     2. 添加环境变量

        ```bash
        vim ~/.pam_environment 
        ```

        ```bash
        GTK_IM_MODULE DEFAULT=fcitx
        QT_IM_MODULE  DEFAULT=fcitx
        XMODIFIERS    DEFAULT=\@im=fcitx
        ```

     3. 添加

        ![Screenshot_20221117_222052.png](https://zergqueen.gitee.io/images/myblog/Screenshot_20221117_222052.png)

### 3.2 安装代理软件**v2rayA**和终端代理软件**proxychains**

   1. 安装并设置自启

      ```bash
      sudo pacman -S v2raya xray proxychains
      sudo systemctl enable --now v2raya
      ```

   2. 浏览器访问http://localhost:2017/

   3. 点击右上角的设置

      ![](https://zergqueen.gitee.io/images/myblog/Screenshot_20221117_222825.png)

   4. 导入

   5. 选择节点连接

      ![](https://zergqueen.gitee.io/images/myblog/Screenshot_20221117_223546.png)

   6. 配置proxychains

      > 如果是全局代理可以忽略此步，vray2A是全局代理

      ```bash
      sudo vim /etc/proxychains.conf 
      ```

      添加以下内容

      ```tex
      socks5 127.0.0.1 20170
      ```

### 3.3 安装SVP4

   - 进入[官网](https://www.svp-team.com/zh/home/)下载
   - 解压执行安装
   - 安装完毕打开后点击左上角图标
   - 点击工具
   - 点击 VLC的SVP插件
   - 打开VLC
   - 右键 -> 工具 -> 偏好设置
   - 视频选项里打开反交错
   - 输入/编解码器选项里禁用硬件加速解码

### 3.4 安装WPS

   ```bash
   yay -S wps-office-cn wps-office-mui-zh-cn wps-office-mime-cn ttf-wps-fonts ttf-ms-fonts
   wps-office-mime-cn ttf-ms-fonts is optional
   ```

   wps-office-mime-cn 和 ttf-ms-fonts 可选

### 3.5 安装Docker

```bash
sudo pacman -S docker
sudo usermod -aG docker ${USER}
sudo systemctl enable --now docker
```

### 3.6 安装Nginx

```bash
sudo pacman -S nginx
```

需要修改配置文件，否则启动报错

```bash
sudo vim /etc/nginx/nginx.conf
```

向http中添加以下内容

```bash
# 长连接超时时间，单位是秒
types_hash_max_size 4096;
```

### 3.7 安装Mariadb

由于ArchLinux自带Mariadb，所以不用安装，只需配置一下

```bash
sudo mariadb-install-db --user=mysql --basedir=/usr --datadir=/var/lib/mysql
sudo systemctl enable --now mariadb
sudo mysql -p -u root
```

创建一个密码为0000的zerg用户，并赋予全部权限

```bash
CREATE USER 'zerg'@'localhost' IDENTIFIED BY '0000';
GRANT ALL PRIVILEGES ON mydb.* TO 'zerg'@'localhost';
FLUSH PRIVILEGES;
```

### 3.8 安装JDK

1. [官网](https://www.oracle.com/java/technologies/downloads/#java17)

   ```bash
   wget https://download.oracle.com/java/17/latest/jdk-17_linux-x64_bin.tar.gz
   tar -zxvf jdk-17_linux-x64_bin.tar.gz 
   mv jdk* jdk-17.0.5
   mv jdk-17.0.5 $HOME/soft
   ```

2. 配置环境变量

   ```bash
   sudo vim /etc/profile
   ```

   添加以下内容

   ```bash
   export JAVA_HOME=$HOME/soft/jdk-17.0.5
   export CLASSPATH=$:CLASSPATH:$JAVA_HOME/lib/
   export PATH=$PATH:$JAVA_HOME/bin
   ```

   ```bash
   source /etc/profile
   ```

### 3.9 安装Maven

1. 下载

   ```bash
   wget https://repo.maven.apache.org/maven2/org/apache/maven/apache-maven/3.8.6/apache-maven-3.8.6-bin.zip
   unzip apache-maven-3.8.6-bin.zip
   mv apache-maven-3.8.6 maven3.8.6
   mv maven3.8.6 $HOME/soft
   ```

2. 配置环境变量

   ```bash
   sudo vim /etc/profile
   ```

   添加以下内容

   ```bash
   export MAVEN_HOME=$HOME/soft/maven3.8.6
   export PATH=$MAVEN_HOME/bin:$PATH
   ```

   ```bash
   source /etc/profile
   ```

### 3.10 安装Redis

```bash
sudo pacman -S redis
sudo systemctl enable --now redis
redis-cli
```

### 3.11 安装修改鼠标滚轮速度软件

1. 安装

   ```bash
   sudo pacman -S imwheel
   ```

2. 修改配置文件

   ```bash
   vim ~/.imwheelrc
   ```

3. 编辑内容

   ```tex
   ".*"
   None,      Up,   Button4, 4
   None,      Down, Button5, 4
   Control_L, Up,   Control_L|Button4
   Control_L, Down, Control_L|Button5
   Shift_L,   Up,   Shift_L|Button4
   Shift_L,   Down, Shift_L|Button5
   ```

4. 建立systemd系统级服务

   ```bash
   mkdir -p ~/.config/systemd/user
   vim ~/.config/systemd/user/imwheel.service
   ```

5. 编辑内容

   ```tex
   [Unit]
   Description=IMWheel
   Wants=display-manager.service
   After=display-manager.service
   
   [Service]
   Type=simple
   Environment=XAUTHORITY=%h/.Xauthority
   ExecStart=/usr/bin/imwheel -d
   ExecStop=/usr/bin/pkill imwheel
   RemainAfterExit=yes
   
   [Install]
   WantedBy=graphical-session.target
   ```

6. 自启动

   ```bash
   systemctl --user enable --now imwheel
   ```

### 3.12 安装其他软件

1. 安装yay，之后就可以全部用yay下载软件了

   ```bash
   sudo pacman -S yay 
   ```

2. 安装其他软件

   ```bash
   yay -S netease-cloud-music jetbrains-toolbox google-chrome icalingua++-git gimp typora vlc nodejs npm nginx colorpicker picgo virtualbox wemeet-bin todesk-bin net-tools
   ```

   分别是网易云、jetbrains盒子、chrome浏览器、QQ、图形处理软件、markdown笔记软件、视频播放软件、nodejs、npm、nginx、取色器、图床软件、虚拟机、腾讯会议、远程连接软件、网络工具包

### 3.13 忽略软件升级

```bash
sudo vim /etc/pacman.conf
```

```tex
IgnorePkg   = mariadb nginx nodejs npm docker redis
```

### 3.14 删除kde自带游戏

```tex
yay -Rs bomber bovo granatier kajongg kanagram kapman katomic kblackbox kblocks kbounce kbreakout blinken kdiamond kfourinline kfourinline khangman kigo killbots kiriki kjumpingcube klickety kgoldrunner kmahjongg kmines knetwalk knights kolf kollision konquest kreversi ksirk ksnakeduel ksquares ksudoku kubrick lskat palapeli picmi
klines kspaceduel knavalbattle kpat kshisen ktuberling
```



## 4 美化

### 4.1 主题

- Plasma视觉风格

  ```tex
  McSur-light
  ```

  ~~如果需要更改锁屏壁纸，到以下目录找到当前主题，然后进去更换图片，图片名不要变~~

  ```bash
  cd /home/zerg/.local/share/wallpapers/
  ```

  可直接在设置里改锁屏壁纸，老瞎眼没看见

- 窗口装饰元素

  ```tex
  Aritim-Light
  ```

- 颜色

  ```tex
  McSurLight
  ```

- 图标

  ```tex
  Tela
  ```

- 光标

  ```tex
  Bibata-Modern-Ice
  ```

- 欢迎屏幕

  ```tex
  Monterey
  ```

  如果要换logo，可以去以下目录找到对应主题然后更换 contents/splash/images 文件夹里的logo.svg，注意：24英寸1080P屏幕logo尺寸为128*128显示最佳

  ```bash
  cd /home/zerg/.local/share/plasma/look-and-feel
  ```

- 登陆屏幕（SDDM）

  ```tex
  McSur-light
  ```
  
  可直接在设置里更改背景图
  
- 桌面特效

  1. 窗口打开关闭特效

     ```tex
     缩放
     ```

  2. 弹出特效

     ```tex
     Fold popups
     ```

### 4.2 桌面

1. dock栏

   ```bash
   sudo pacman latte-dock
   ```

2. 挂件

   - [Compact Shutdown](https://store.kde.org/p/1483671/) 关机按钮

   - [Translator](https://store.kde.org/p/1395666/) 翻译

     还需要安装

     ```bash
     yay -S translate-shell
     ```

   - Netspeed Widget 网速监控

     还需要安装

     ```bash
     yay -S kdeplasma-addons ksysguard
     ```

   - Resources Monitor folk 资源监控
   
   - Weather Widget 2

### 4.3 终端

1. 安装zsh

   ```bash
   yay -S zsh
   ```

2. 更改默认终端

   ```bash
   chsh -s /bin/zsh
   ```

3. 安装oh-my-zsh-git

   ```bash
   yay -S oh-my-zsh-git
   ```

4. 默认配置

   ```bash
   cp /usr/share/oh-my-zsh/zshrc ~/.zshrc
   ```

5. 安装插件

   - 跳转目录

     ```bash
     yay -S autojump
     ```

   - 命令高亮和自动建议补全

     ```bash
     yay -S zsh-syntax-highlighting zsh-autosuggestions
     sudo ln -s /usr/share/zsh/plugins/zsh-syntax-highlighting /usr/share/oh-my-zsh/custom/plugins/
     sudo ln -s /usr/share/zsh/plugins/zsh-autosuggestions /usr/share/oh-my-zsh/custom/plugins/
     ```

6. 插件配置

   ```bash
   vim ~/.zshrc
   ```

7. 找到plugins=(git)，写入

   ```tex
   plugins=(
   	sudo
   	git
   	autojump
   	zsh-syntax-highlighting
   	zsh-autosuggestions
   )
   ```

8. 安装 JetBrains mono字体

   ```bash
   wget https://github.com/ryanoasis/nerd-fonts/releases/download/v2.2.2/JetBrainsMono.zip
   unzip  -d JetBrainsMono JetBrainsMono.zip
   mkdir -p ~/.local/share/fonts/jetbrains
   mv JetBrainsMono/'JetBrains Mono Medium Nerd Font Complete Mono.ttf' ~/.local/share/fonts/jetbrains
   fc-cache -fv
   ```

9. 下载powerlevel10k

   ```bash
   git clone --depth=1 https://github.com/romkatv/powerlevel10k.git ${ZSH_CUSTOM:-~/.oh-my-zsh/custom}/themes/powerlevel10k
   ```

10. 修改zsh主题

    - 修改配置文件

      ```bash
      vim ~/.zshrc
      ```

    - 找到ZSH_THEME，修改

      ```bash
      ZSH_THEME="powerlevel10k/powerlevel10k"
      ```

    - 刷新

      ```bash
      source ~/.zshrc
      ```

### 4.4 Grub

1. [下载主题](https://www.gnome-look.org/p/1482847)

   ```bash
   wget -P ~/Downloads/ https://github.com/AdisonCavani/distro-grub-themes/releases/download/v2.8/arch.tar
   ```

2. 解压

3. 移动至系统的grub主题文件夹里

   ```bash
   cp -r ~/.Downloads/arch /usr/share/grub/themes/
   ```

4. 修改grub配置

   ```bash
   sudo vim /etc/default/grub
   ```

5. 找到#GRUB_THEME=，取消注释，更改路径

   ```bash
   GRUB_THEME="/usr/share/grub/themes/arch/theme.txt"
   ```

6. 更新grub.cfg

   ```bash
   yay -S update-grub
   sudo update-grub
   ```

