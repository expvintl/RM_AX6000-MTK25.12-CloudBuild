# RM_AX6000-MTK25.12-CloudBuild

红米 AX6000（MT7986，**MTK U-Boot 110M 分区**）云编译仓库。

源码：[chasey-dev/immortalwrt-mt798x-rebase](https://github.com/chasey-dev/immortalwrt-mt798x-rebase) 分支 `25.12`（闭源 Wi-Fi 驱动 `kmod-mt_wifi` + `kmod-mediatek_hnat` 硬件加速）。

配置种子直接取自源码树自带的 `defconfig/mt7986-ax6000.config`，只在其上追加 `config/extra-kmods.config`。**不修改源码**，所以行为应该和成品固件一致，区别只是多带了 kmod。

## 怎么用

1. 打开 **Actions** → `Build AX6000 25.12 (mtkuboot)` → **Run workflow**
2. 等约 2–4 小时
3. 下载两个 artifact：
   - `firmware-ax6000-mtkuboot` — 固件（`*-mtkuboot-squashfs-sysupgrade.bin`）
   - `kmod-ipk` — 本次编出来的全部 kmod `.ipk`

## 三个校验步骤的意义

- **`config/expect.txt`**：这些 kmod 缺一个就让构建**失败**。防止「以为编进去了其实没有」。
- **`config/want.txt`**：源码里可能压根没有这些包，缺了只在 Job Summary 里标 ⚠️，不中断构建。
- 构建开始时会打印 `DEVICE_xiaomi_redmi-router-ax6000` 的选中情况，确认只有 `mtkuboot` 一个变体在编。

## 刷机与 kmod 的重要提醒

- kmod 是**内核版本绑定**的：`vermagic` 必须和固件完全一致。
  - 想用 `kmod-ipk` 里的包，**只能装到本仓库编出来的固件上**，不能装到别人的成品固件上（哪怕同源码，只要 config 有差异就可能对不上）。
  - 反过来：别人编的 kmod 也装不到这个固件上。
- 从 21.02 直刷过来的路径作者未测试；成品固件作者建议走 U-Boot 110M 分区。
- 本仓库产出的 `factory.bin` 与 `sysupgrade.bin` 都在 `bin/targets/mediatek/filogic/`。

## 已知取舍

- `luci-app-adguardhome`、`easytier` 这类**不在官方 feed 里**的第三方插件没有加进来（避免引入不可控 feed 导致构建失败）。需要的话可以后续加自定义 feed。
- 只编 `mtkuboot` 变体；`stock` / `ubootmod` 已显式关掉。
