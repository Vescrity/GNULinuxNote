# Wine 运行 RPG_MAKER 游戏踩坑记录

很早以前踩的坑了，想到可以水个文放出来就写一下。  
主要是自己有一定数量的 RM 魔塔游戏，但是直接在 wine 上跑还是碰到了一些问题。

## Q1: 这 Alt+Enter 全屏它也不好使啊

魔塔游戏的窗口大小往往在现代的显示器上很小。在 win 下可以直接 `Alt+Enter` 全屏游玩，但在 wine 上就是另一回事了。一下 `Alt+Enter`，全屏了，但是却显示在左上角。  

解决方案倒是有，当然是不是最好的就不清楚了。

### X11：调整分辨率

可以用 xrandr 来调整显示分辨率。写成 shell 脚本快捷一点的话可以是：

```bash
#!/bin/sh

if [ $# -ne 1 ]; then
  echo "请传入一个数字作为参数"
  exit 1
fi

# 获取传入的数字
number=$1

# 判断数字并执行相应操作
if [ $number -eq 0 ]; then
  xrandr --output eDP-1 --mode 1920x1080
elif [ $number -eq 1 ]; then
  xrandr --output eDP-1 --mode 960x540
elif [ $number -eq 2 ]; then
  xrandr --output eDP-1 --mode 1400x900
else
  echo "无效的数字参数"
  exit 1
fi
```
命名为 `xr`，这样执行 `xr 1` 就可直接切成刚好够放下游戏的大小。`xr 0` 可直接切回 `1920x1080`。  

### Wayland: Wayfire 的缩放插件

Wayfire 有 zoom 插件，在配置文件中设置一下即可。例如：
```ini
[zoom]
interpolation_method = 0
modifier = <ctrl> <super> 
smoothing_duration = 300ms linear
speed = 0.010000
```
即可用 `Ctrl+Super` 配合滚轮来缩放画面。  

同时因为 wayland 混成器也可在其他的混成器甚至 X11 中启动，因此这个方案也可用于其他环境。Wayfire 可在启动时指定配置文件，所以如果你想用 Wayfire 本体，也想用其他的混成器中使用 wayfire 而不想冲突，可以分别指定不同的配置文件启动。

## Q2: 这游戏怎么音乐没声啊

推测是因为 lib32 的库没有打包解码 mp3 的库。总之把音频文件夹中的 mp3 都转成同名的 ogg 就好了。这个操作可以用脚本自动化完成。  

以下脚本为将当前目录中的音频文件转换为 ogg 格式并输出至 `./ogg` 文件夹中。请根据需求更改。

```bash
#!/bin/bash

# 定义输入和输出目录
INPUT_DIR="./"  # 当前目录
OUTPUT_DIR="./ogg"

# 创建输出目录
mkdir -p "$OUTPUT_DIR"

# 遍历输入目录中的所有音频文件
for file in "$INPUT_DIR"*; do
    # 检查文件是否为音频文件（可以根据需要修改扩展名）
    if [[ $file == *.wav || $file == *.flac || $file == *.mp3 || $file == *.aac || $file == *.m4a ]]; then
        # 获取文件名（不带扩展名）
        filename=$(basename "$file")
        filename_no_ext="${filename%.*}"
        
        # 定义输出文件路径
        output_file="$OUTPUT_DIR/${filename_no_ext}.ogg"
        
        # 使用 ffmpeg 转换文件
        ffmpeg -i "$file" -q:a 0 "$output_file"
        
        echo "转换: $file -> $output_file"
    fi
done

echo "所有文件已转换完成。"
```

原理似乎是因为 RMXP 对音频文件的扩展名不进行区分。
