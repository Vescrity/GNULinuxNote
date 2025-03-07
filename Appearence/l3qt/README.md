# Arch Linux LXQt + Polybar + i3 配置桌面

话不多说先上图：

![](https://github.com/Vescrity/lxqt-i3-config/raw/main/img/image.png)

## Q&A

- Q: 为什么不用纯 i3 而引入 lxqt
- A: 也能用，就是有的配置还得是有桌面环境才够用
- Q: 你桌面怎么还有 KDE
- A: KDE 组件虽然问题有些多但是实在是太难不依赖了（）
  - KDE connect，同步剪贴板以及手机文件管理相当好用
  - Dolphin, 带终端集成的文件管理器顺便还和 KDE connect 集成
  - 有了上面这俩了干脆就得把 plasma-workspace 都得装下来，不然问题有够受的
- Q: 那怎么不直接用 plasma
- A: i3 更舒服，lxqt 够轻量，plasma 换窗口管理器实在太麻烦（）

## 开始

安装软件包：

```bash
# lxqt
pacman -Syu lxqt-session lxqt-config lxqt-panel lxqt-policykit xdg-desktop-portal-lxqt
# i3
pacman -Syu i3wm picom
# 壁纸设置
pacman -Syu feh
# polybar
pacman -Syu polybar
# 播放控制
pacman -Syu playerctl
# 可选
pacman -Syu i3lock
```

## 配置


### picom 配置

picom 是合成器，没 picom 也能用，就是没什么显示特效。

配置文件[太长了](https://github.com/Vescrity/lxqt-i3-config/tree/main/.config/picom)

主要配置了半透明，淡入淡出，焦点突出显示，圆角，窗口阴影等。

### polybar 配置

折腾过 i3status, i3blocks, 最后还是 polybar 真香。就是 PID 刷的飞快。  
配置文件[太长了](https://github.com/Vescrity/lxqt-i3-config/tree/main/.config/polybar)


### 锁屏脚本

其实感觉锁屏没啥太大用，临时锁屏不如直接切个没登录的tty算了。
这里提供的是一个将当前显示模糊化后设为锁屏壁纸的锁屏脚本。结合 i3 快捷键可以直接锁屏。
此脚本依赖 i3lock, imagemagick, flameshot (截图软件).
在一个添加为 `PATH` 的文件夹中创建如下脚本。此处命名为 `i3l`.
```bash
#!/bin/bash
img=/tmp/i3lock.png

flameshot full -p $img
convert $img -blur 0x40 -brightness-contrast -20 $img
i3lock -i $img

rm $img
```
### 壁纸切换脚本

不喜欢黑壁纸？还想设置多个壁纸切换？你可能需要这个脚本。结合 i3 快捷键，可以每次触发快捷键后更换一张文件夹中的壁纸。
在一个添加为 `PATH` 的文件夹中创建如下脚本。此处命名为 `chbg`.

```bash
#!/bin/bash
# 壁纸文件夹路径
WALLPAPER_DIR=~/Pictures/Wallpaper
# 壁纸索引文件路径
WALLPAPER_INDEX=~/Pictures/Wallpaper/.wallpaper_index.txt
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
feh --bg-fill "${FILES[$index]}"
# 索引增加
((index++))
# 保存更新后的索引
echo "$index" > "$WALLPAPER_INDEX"
# 退出脚本
exit 0

```
### qt样式与gtk样式

安装 qt5ct qt6ct lxappearance 包来进行配置。
需在 lxqt 的会话配置中设置环境变量 QT_QPA_PLATFORMTHEME=qt6ct
结合 kvantum 可设置 kvantum 的风格(在qt6ct qt5ct中设置。)

### i3 配置文件

由 i3 默认配置修改得到。禁用了使用 `j k l ;` 进行窗口或焦点移动。
完整版：https://github.com/Vescrity/lxqt-i3-config/tree/main/.config/i3
`~/.config/i3/config` 节选：

```ini

# Define names for default workspaces for which we configure key bindings later on.
# We use variables to avoid repeating the names in multiple places.
set $ws1 "1:"
set $ws2 "2"
set $ws3 "3"
set $ws4 "4"
set $ws5 "5"
set $ws6 "6"
set $ws7 "7"
set $ws8 "8"
set $ws9 "9"
set $ws10 "10:"
set $ws11 "11"
set $ws12 "12"
set $ws13 "13"
set $ws14 "14"
set $ws15 "15"
set $ws16 "16"
set $ws17 "17"
set $ws18 "18"
set $ws19 "19"
set $ws20 "20"

client.background       #ffffff

# floating pop up automatically
for_window [window_role="pop-up"] floating enable
for_window [window_role="task_dialog"] floating enable
# 对 QQ 弹出的特定窗口默认悬浮
for_window [title="记录$" class="QQ"] floating enable
for_window [title="^图片查看器$" class="QQ"] floating enable
# mpv 默认悬浮
for_window [class="mpv"] floating enable
# pcmanfm-qt 桌面默认不悬浮
for_window [title="^pcmanfm$" class="Pcmanfm"] floating disable
for_window [title="^pcmanfm-desktop" class="pcmanfm-qt"] floating disable
# 桌面所在工作区所有其他窗口默认悬浮
for_window [title="^((?!pcmanfm).)*$" workspace=$ws1] floating enable
default_border pixel 0
new_window none
new_float none

# i3gaps
gaps inner 10
gaps outer 10
smart_gaps on

# 快捷键配置


# 更改默认的关闭窗口，避免关闭pcmanfm-qt的桌面
bindsym $mod+Shift+q [title="^(?!pcmanfm-desktop.*$)" con_id=__focused__] kill 
# 启动 dmenu 并指定字体
bindsym $mod+d exec --no-startup-id dmenu_run -fn 'hack-10'
# 锁屏，i3l为自制脚本
bindsym $mod+shift+l exec --no-startup-id i3l
# 更改壁纸，chbg为自制脚本
bindsym $mod+n exec --no-startup-id chbg
# 下拉式终端
bindsym $mod+p exec --no-startup-id xfce4-terminal --drop-down 

# 壁纸设定
exec_always feh --bg-scale "/home/vescrity/Pictures/Wallpaper/kde6.png"
exec_always --no-startup-id newpoly

exec_always --no-startup-id /usr/lib/polkit-gnome/polkit-gnome-authentication-agent-1
exec_always --no-startup-id numlockx
exec_always --no-startup-id fcitx5
exec_always --no-startup-id dunst
exec_always --no-startup-id picom

```


## End

完整内容请参考：https://github.com/Vescrity/lxqt-i3-config
