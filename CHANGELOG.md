# CHANGELOG

## v3.1.0 — 2026-11-10（收官）
- LOCALVERSION：`-zstd` → `-Aukimor`（uname -r = `4.9.186-Aukimor-g<hash>`）
- 配套 Magisk 模块 v3.1.0（内核已内置项从模块删除，改"内核附加模块"定位）
- 实机验证全部通过：开机 / VPN（加固 app）/ FolkPatch root / swappiness=100 / zram=[lz4] / config.gz 欺骗

## v3.0.0 — 2026-11-09（stable 补丁挖掘收官）
- 24 个 4.9.186→337 官方 stable 补丁落地（详见 docs/patches.md），分 6 批发：
  - 第一批：zsmalloc destroy/isolation race ×2、tun register UAF
  - 第二批：UFS hold loop/atomic、query desc、tcp ecn/rtx-hint、memcg iter UAF
  - swapfile 读写偏移统一（map_swap_page）
  - 第三批：dwc3 remove 顺序、input keycode 防护、clk-rcg2 父时钟空检查
  - 第四批：random crng data race、RNDRESEEDCRNG、timer wheel/jiffies、fork CLONE_PARENT race
  - 第五批：genirq affinity_notify kref leak、printk kmsg last-line、perf_mmap_close ABA
  - 追加：zsmalloc migrated stats、UFS early dev_set_drvdata、tcp md5-sack option
- futex：arm64 启用 HAVE_FUTEX_CMPXCHG（编译期路径替代运行时探测）
- COMPAT_VDSO=y（32 位 app 系统调用免陷入）
- schedutil 三段式调频（helix 风格，保留 1.25x 基准 + sysfs 旋钮 + bit_shift 范围校验）
- 息屏限频（display_state 接线 dsi_panel + 大小核上限，默认开：银 1.32G / 金 1.2G）
- 内存参数：swappiness 60→100、dirty 10→5/20→10、watermark_scale_factor 0→300（外部 AI 评审校准）

## v2.0.0 — 2026-11-08（186 基线 + 优化全家）
- 基线：88-zstd 分支（官方 dipper-q-oss + 215 社区提交）导入新仓库（fork 中转站方案）
- KSU 全移除（Kconfig/Makefile 引用 + 5 个 syscall 插桩）
- 构建链：Actions CI + 作者同款工具链（clang13 + linaro-gcc-4.9），AK3 打包
- dtb 精简 54→1（polaris-mp-v2.1）
- 功能：LZ4 1.10 + lz4armv8 NEON、exFAT（arter97 5.8.2）、config.gz 欺骗、UFS 双补丁（fc55974ee + b78d30deb）
- Kconfig 默认化：schedutil/HZ100/westwood/deadline/PSI/LZ4/exfat/zram-lz4 全部内置默认
- 裁剪：28 项调试 + 88XXAU + CAN

## v1.0.0 — 2026-11-08（起）
- 337 基线弃用（加固 VPN app 闪退，根因 186→337 行为级差异无法修复）
- 新仓库 Aukimor_Polaris_Kernel 建立，186/88-zstd 基线迁移
- 实机验证 42470011272：开机 + VPN 正常（基线正确性确认）