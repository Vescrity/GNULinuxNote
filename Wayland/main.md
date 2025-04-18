# Wayland 折腾记录

> 文章不提供样例配置参考，仅作为配置向导。

- **你能在这里看到什么**:
    - 几个 Wayland 混成器的优缺点
    - 这些环境下的桌面/壁纸/常用小工具的选用与配置
        - 包括截屏方案、录屏方案以及通知中心。

## 一些废话

用了快一年的 Linux，本来是 X11 的忠实用户，不过抱着试一试的心态尝试了一下 Wayland。

就个人体验来讲，Wayland 在显示合成上做的显然比 X11 要好很多。

也尝试过 KDE 等桌面环境在 Wayland 下的表现。体验下来是感觉 KDE 的 Wayland 环境相对 X11 还没那么成熟。

但是对于窗口管理器，那效果提升可不是一点半点了。

## 混成器 (Compositer)

试了很多混成器，最后决定保留了下面这几个，算是各有优点吧。

要说有些什么坑的话，有个在 X11 上那些窗口管理器上也需要注意的点就是或许最好先做个启动脚本把需要设置的环境变量先设置好。

例如：

```bash
#!/bin/bash
export LANG=zh_CN.UTF-8
export QT_QPA_PLATFORMTHEME=qt6ct:qt5ct
export GTK_THEME=Sweet-Dark-v40
export XDG_CURRENT_DESKTOP=sway
export XDG_SESSION_DESKTOP=sway
export _JAVA_AWT_WM_NONREPARENTING=1
export SDL_VIDEODRIVER=wayland
export XDG_MENU_PREFIX=lxqt-
export QT_QPA_PLATFORM=wayland
# 似乎有 NVIDIA 专有驱动就必须加这个选项，哪怕它似乎没在用 N 卡渲染。
sway --unsupported-gpu
```
### Niri

#### 优点/特点

- 滚动平铺的窗口布局。
- 触控板手势良好
- 多屏操作逻辑舒适
- ……

#### 缺点

- 没有内置 Xwayland 支持，xwayland-satelite 还不够稳定且部分窗口行为奇怪。

### labwc

“wayland 版 openbox ”

#### 优点

- 总之它能用
- Xwayland 表现良好，行为基本均符合预期。


### Sway/Swayfx

#### 优点

- 易用的平铺窗口管理
- 高可配置性
- 触控板手势
    - 支持超多的手势操作，并且可以绑定几乎任意操作
- 使用 swayfx 有基础的窗口模糊，圆角，阴影等特效。

#### 缺点

- 没有动画特效
- 部分应用下存在一些问题，例如 foot (一个终端模拟器) 中似乎无法正常使用输入法。这个问题在其他混成器下没有注意到。
- 原版 Sway 没有窗口视觉特效(模糊，圆角等)
- Waydroid 滑动操作有些 bug


### Hyprland

#### 优点

- 视觉特效
- 插件生态
    - hy3: i3/sway 布局
    - Hypr-DarkWindow: 指定窗口暗亮色切换

#### 缺点

- 手势配置不如 sway 自由
- 稳定性一般，负载较高时可能会画面冻结


### wayfire

纯纯特效档。3D 桌面实在太超前了。
- 被恶性的输入 bug 整无语了。如果我再想装它就是拿来当任意比例放大窗口的工具来玩rm魔塔。

#### 优点

- **随时以几乎任意比例放大！** > 可惜还是原来那些像素。
    - 这个在我这里感觉还是比较需要的。因为我多数窗口习惯放到相对偏小的字体上。
- 特效极强！
    - 3D 窗口旋转
    - 3D 窗口切换动效
    - 窗口可从一个工作区直接扔到另一个工作区
    - 可以跨工作区的窗口
- 插件化配置。但是插件管理体验上还没有 Hyprland 那么成熟。
- 结合 wcm 等配置工具易于修改配置。
- XWayland 下的 Wine 表现也不错。
- Waydroid 兼容不错

#### 缺点

- 平铺管理过于基础，不符合我的操作逻辑习惯。
- 触控板手势功能有限。

### dwl

单纯是省电档。考虑到 sway 与 swayfx 冲突，我只保留了 swayfx ，而其默认配置有较多特效，因此考虑到省电需求便选择了 dwl 。

结合自己的一套构建脚本倒也算易于配置。

#### 优点

- 确实省电，跟 X11 下纯 i3 的耗电没有明显区别。
- 源代码层面的定制

#### 缺点

- 多少还是有点麻烦
- 默认的窗口布局管理似乎更只适合纯终端工作环境

## bar/panel/dock

折腾了很久想要一个自动隐藏的效果，但是后来发现自动隐藏似乎还没有什么通用的方案，因此基本不太能指望。

### waybar

体验类似 polybar ，可参考的配置也很多，效果也还不错。没有在这上观察到类似 polybar 疯狂刷 pid 的情况。

### wf-dock

wf-shell 包，包含了 wf-panel 和 wf-dock 。panel 配置了半天 css 没把它弄成想要的模样，遂放弃。wf-dock 类似 Win11 风格，倒也正好符合我想要的。

- 在 sway 上没法自动隐藏，只能在 wayfire 上自动隐藏。
- panel 与 dock 都有隐藏后难以唤出的问题。解决方法是用 waybar 在屏幕下方创建了一个高度很小的没有内容的透明 bar 。这样刚好在其收起时仍可见一个条。在我的配置中则刚好能够显示出聚焦窗口的高亮。

## 桌面

一如既往地选择了 pcmanfm-qt 。直接 pcmanfm-qt --desktop 就能将传统的桌面像壁纸一样显示。所有工作区均可见。

## 壁纸

不使用桌面的话可以使用下面的来提供壁纸。

### swww

带有切换动效的壁纸。可自行编写脚本来控制壁纸切换。
缺点是切换时有着很高的 cpu 占用。好像有方法避免？


下面是壁纸切换的脚本，可绑定至快捷键使用。效果类似于之前 lxqt+i3 方案中的脚本。

```bash
#!/bin/bash

swpid=$(pidof swww-daemon)
if [ -z "$swpid" ]; then
    swww-daemon &
    exit
fi

# 壁纸文件夹路径
WALLPAPER_DIR=~/Pictures/Wallpaper
# 壁纸索引文件路径
WALLPAPER_INDEX=~/Pictures/Wallpaper/.wallpaper_index
# 读取当前索引
if [ -f "$WALLPAPER_INDEX" ]; then
    index=$(cat "$WALLPAPER_INDEX")
else
    index=0
fi
# 获取文件夹中所有图片文件的列表，假设所有壁纸文件都是.png或.jpg
FILES=($WALLPAPER_DIR/*.{png,jpg})
# 获取文件总数
TOTAL=${#FILES[@]}
# 确保索引不会超出范围
if [ "$index" -ge "$TOTAL" ]; then
    index=0
fi
# 设置壁纸
#hyprctl hyprpaper preload "${FILES[$index]}"
#hyprctl hyprpaper wallpaper "eDP-1,${FILES[$index]}"
swww img "${FILES[$index]}" --transition-type outer --transition-bezier '0.10,0.6,0.15,1.1' --transition-fps 60 --transition-pos 0.1,0.9
# 索引增加
((index++))
# 保存更新后的索引
echo "$index" > "$WALLPAPER_INDEX"
# 退出脚本
exit 0

```

### mpvpaper

单论效果就是把 mpv 当作你的壁纸，也就是动态壁纸。由于没什么动态壁纸的需求所以我更多是当做一个视频播放器。

## 小工具

### 截屏

都说 flameshot 也能用。倒是试过，但是从来没成功运行起来过。
直接用传统的 grim + slurp 了。搭配 imagemagick 可以做一个截屏后自动加阴影/发光特效的边框的截屏脚本。

```bash
#!/bin/bash

# 设置阴影效果参数
shadow_color="cyan"
shadow_x="0"
shadow_y="0"
shadow_sigma="15"
shadow_opacity="50"


grim -g "`slurp`" - | magick - \
       \( +clone -background $shadow_color -shadow ${shadow_opacity}x${shadow_sigma}+${shadow_x}+${shadow_y} \) \
        +swap -background none -layers merge +repage - | wl-copy

```

### 录屏

#### wf-recorder

sway, wayfire, dwl 都是基于 wlroots 的混成器，都可以使用 wf-recorder 来进行录屏。好处是脚本化易于定制，~~坏处是录屏时很占 CPU~~ 通过添加选项可以将编码转到 GPU 上。

在 wf-recorder 上做一些包装，我编写了 [wfrc](https://github.com/Vescrity/wfrc) 脚本，实现了一个易用的选区录屏功能。



#### obs

这里主要是避坑。需要有 xdg-desktop-portal-wlr 安装。如果安装后在打开 obs 后发现仍然找不到 屏幕采集(pipewire) 等类似的源则需要一些 dbus update 的操作。Arch 分发的 sway 中提供了这样的配置文件以在启动时运行它：

```bash
# sway does not set DISPLAY/WAYLAND_DISPLAY in the systemd user environment
# See FS#63021
# Adapted from xorg's 50-systemd-user.sh, which achieves a similar goal.

# Upstream refuses to set XDG_CURRENT_DESKTOP so we have to.
exec systemctl --user set-environment XDG_CURRENT_DESKTOP=sway
exec systemctl --user import-environment DISPLAY \
                                         SWAYSOCK \
                                         WAYLAND_DISPLAY \
                                         XDG_CURRENT_DESKTOP

exec hash dbus-update-activation-environment 2>/dev/null && \
     dbus-update-activation-environment --systemd DISPLAY \
                                                  SWAYSOCK \
                                                  XDG_CURRENT_DESKTOP=sway \
                                                  WAYLAND_DISPLAY
```

对于 wayfire 应该也需要类似的操作。

### 通知中心

- 简洁易用: dunst
- LXQt: lxqt-notificationd
- 推荐: swaync

## 2024-09-15

又折腾了一下从开机动画到 Display-Manager 这一串的外观配置，或许可以再开一篇文。
