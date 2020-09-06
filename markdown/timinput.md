花了两个小时的时间解决linux tim无法输入中文的问题，最后直接重装系统对比前后配置解决问题。

我觉得这种事情应该是十分钟就能搞定的，突然想起高中政治老师之前说过的一个问题。一个人的汽车坏了，他去修车，修车师傅一锤子解决了车子的问题，然后要价100，当时她问我们这一锤子值100块钱吗？我当时觉得很不值或者是很不划算，但是现在重新审视这个问题。若有人能给我解决linux TIM中文输入的问题，哪怕就是写了一行指令，我也愿意。

回归正题。

#### 添加Archlinuxcn源

我使用的是清华大学的源，直接摘录清华的使用帮助

> ## ArchlinuxCN 镜像使用帮助
>
> Arch Linux 中文社区仓库 是由 Arch Linux 中文社区驱动的非官方用户仓库。包含中文用户常用软件、工具、字体/美化包等。
>
> 完整的包信息列表（包名称/架构/维护者/状态）请 [点击这里](https://github.com/archlinuxcn/repo) 查看。
>
> -  官方仓库地址：https://repo.archlinuxcn.org
> -  镜像地址: https://mirrors.tuna.tsinghua.edu.cn/archlinuxcn/
>
> 使用方法：在 `/etc/pacman.conf` 文件末尾添加以下两行：
>
> ```
> [archlinuxcn]
> Server = https://mirrors.tuna.tsinghua.edu.cn/archlinuxcn/$arch
> ```
>
> 之后安装 `archlinuxcn-keyring` 包导入 GPG key。

安装keyring需要multilib，在pacman.conf添加archlinuxcn的时候顺便也把multilib注释删除掉

### 安装deepin.wine.tim

添加Archlinuxcn源之后可以直接使用pacman安装tim

```
sudo pacman -S deepin.com.qq.office
```

然后一路回车默认到底，这个时候TIM就安装好了

如果是Gnome桌面环境的话是可以直接打开的

但是我目前使用的是KDE桌面环境，deepin-wine打包了一部分gnome环境的设置，所以需要安装所依赖的gnome设置

```
sudo pacman -S gnome-settings-daemon
```

然后需要打开依赖的gsd-xsettings，在/usr/lib目录下

我们只需要在打开deepin-wine之前打开这个设置。

* 在开机启动添加gsd-xsetting
* 在deepin-wine apps的run.sh脚本里添加gsd-xsetting运行 /ust/lib/gsd-xsettings& (&是后台运行命令符)

这个时候直接打开tim是可以直接运行的

### 无法输入中文

输入法安装我就不再提及，可以查阅之前的文章

然后我们要一步一步检查问题

* 编码是否安装正常
* 输入法环境变量是否正常
* 如果是gnome桌面环境，是否取消ibus绑定
* 运行环境编码是否是zh_CN.UTF-8

##### 编码是否安装正确

很多人在安装系统后会只删除注释en_US.UTF-8，在英文环境下运行系统。或者就是忘记删除中文编码的注释。这个时候我们重新安装编码`vim /etc/locale.gen`在这个文件下，找到zh_CN.UTF-8前面的注释，然后使用`locale-gen`安装编码。缺失编码无法输入的问题一般这样都可以解决

##### 输入法环境变量的问题

不知道什么原因，即使在不配置环境变量的时候，一些程序也可以调用输入法输入中文。这就超出我目前的知识范围了，深入学习linux后回来补充这里的知识盲区。在不了解原理的情况下，对问题的排故不影响什么，估计很多写教程的人应该很多都不知道原理，但是我不知道原理就会浑身难受。先解决问题吧。

编辑~/.xprofile

```
export GTK_IM_MODULE=fcitx
export QT_IM_MODULE=fcitx
export XMODIFIERS="@im=fcitx"
```

安装输入法需要配置的三个变量，如果安装输入法的过程中忘记配置环境变量也会导致TIM无法使用中文输入。

添加好环境变量后可以排除是否是环境变量的问题

##### gnome桌面环境是否绑定ibus

确实存在这么一个问题，需要取消gnome强行捆绑ibus才能使用fcitx输入法。我目前使用的不是gnome环境，等我遇到再写吧，arch中文论坛里面写的很详细

##### 运行环境编码是否是zh_CN.UTF-8

这又是一个环境变量的问题，刚刚测试过，如果LC_ALL不是zh_CN.UTF-8那么其他不管怎么设置都是无法输入中文的，要么直接在locale.conf里设置编码，要么就添加一个用户变量来设置编码

`export LC_ALL=zh_CN.UTF-8`

添加在用户环境变量`~/.xprofile`里面或者直接添加在程序的运行脚本里面/opt/deepinwine/apps/Deepin-TIM/run.sh



以上就是我在使用deepin.wine.tim遇到的所有问题，如果还有别的问题欢迎随时交流