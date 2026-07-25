# B860AV2.1-A GPU 加速内核
![Build](https://github.com/LIyang-linux/B860AV2.1-A-kernel/actions/workflows/build-kernel.yml/badge.svg)

为中兴 B860AV2.1-A (S905L3-B) 定制的 6.1.y LTS 内核，启用 **Mali-450 GPU 加速**。

## 特性

- **内核版本**: 6.1.y LTS（跟随 unifreq/linux-6.1.y 主线）
- **源码**: unifreq/linux-6.1.y（含 Amlogic SoC 补丁）
- **GPU**: Mali-450 lima 开源驱动 + Mesa 用户态
- **编译方式**: x86_64 交叉编译 arm64（无需 ARM runner）
- **CI/CD**: GitHub Actions 自动构建 + 发布 Release

## 关键内核配置

```
CONFIG_DRM_MESON=y          # Amlogic 显示控制器
CONFIG_DRM_MESON_DW_HDMI=y  # HDMI 输出
CONFIG_DRM_LIMA=m           # Mali-450 GPU
```

## 使用方法

内核编译成功后会发布到 [Release](https://github.com/LIyang-linux/B860AV2.1-A-kernel/releases) 标签 `kernel-gpu`。

该内核会被 Armbian CI 自动使用，同时也支持手动安装：

```bash
wget https://github.com/LIyang-linux/B860AV2.1-A-kernel/releases/download/kernel-gpu/kernel-6.1.x-gpu.tar.gz
tar xzf kernel-6.1.x-gpu.tar.gz -C /
update-initramfs -u -k $(uname -r)
reboot
```

## 构建

手动构建需要 x86_64 Linux 环境：

```bash
# 安装交叉编译工具链
sudo apt install gcc-aarch64-linux-gnu flex bison bc u-boot-tools

# 克隆源码
git clone --depth=1 --branch main https://github.com/unifreq/linux-6.1.y.git

# 应用 ophub 内核配置
wget -O .config https://raw.githubusercontent.com/ophub/kernel/main/kernel-config/release/stable/config-6.1

# 编译
make ARCH=arm64 CROSS_COMPILE=aarch64-linux-gnu- olddefconfig
make ARCH=arm64 CROSS_COMPILE=aarch64-linux-gnu- -j$(nproc) Image.gz modules dtbs
```

## 致谢

- [unifreq/linux-6.1.y](https://github.com/unifreq/linux-6.1.y) - 含 Amlogic 补丁的内核源码
- [ophub/kernel](https://github.com/ophub/kernel) - 内核配置
- [B860AV2.1-A](https://github.com/LIyang-linux/B860AV2.1-A) - Armbian 线刷镜像项目
