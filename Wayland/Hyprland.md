# Hyprland 基本配置指北

~~说真的，感觉不如自己慢慢看官方文档~~

## 安装

`Arch Linux` 安装 `hyprland` 包，在群组中讨论得知 Arch 的打包存在多余的依赖项，于是写了这么个 `PKGBUILD` 来避免安装多余的依赖。

```PKGBUILD
pkgname=fake-vulkan-validation-layers
pkgver="0.1"
pkgrel=1
pkgdesc='fake'
arch=(any)
depends=()
makedepends=()
provides=(vulkan-validation-layers)
conflicts=()
source=()
sha256sums=()
prepare() {
true
}
build() {
true
}
package() {
true
}

```

然后基本的包安装 `hyprland`，`xdg-desktop-portal-hyprland`，需要安装插件则还需 `cpio` `cmake` 。
另外建议安装 `wl-clipboard` `wmenu` 等软件包。

## 配置

默认配置在 `/usr/share/hyprland/hyprland.conf`。在完成配置前不建议即刻启动尝鲜。



