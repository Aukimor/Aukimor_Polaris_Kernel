# Stable 补丁清单（4.9.186 → 4.9.337）

本项目从官方 stable 区间 `v4.9.186...v4.9.337` 的 10326 个提交中，按"日用相关 + 零风险 + 锚点匹配"三标准筛出的 24 个补丁。全部是纯 bugfix（竞态/悬空指针/错误值/缺失防护），不改变行为路径。

## 批次明细

### 第一批（commit ba1b91bdac）
| 上游 sha | 内容 | 文件 |
|---|---|---|
| 0e7e2730da | zsmalloc: fix race in zs_destroy_pool（销毁与迁移竞态）| mm/zsmalloc.c |
| fdcd8b63ea | zsmalloc: dec_isolated 补 smp_mb 配对 | mm/zsmalloc.c |
| a8d8b692eb | tun: UAF on register_netdev failure（publish 延后）| drivers/net/tun.c |

### 第二批（commit 634ac2d8af）
| 上游 sha | 内容 | 文件 |
|---|---|---|
| b2a01a5269 | ufs: fix possible infinite loop in ufshcd_hold | drivers/scsi/ufs/ufshcd.c |
| db1b2190d5 | ufs: fix scheduling while atomic in ufshcd_hold | drivers/scsi/ufs/ufshcd.c |
| 6ebc434f30 | ufs: fix potential bug ends in system hang（query descriptor 悬空）| drivers/scsi/ufs/ufshcd.c |
| 6aab2d9c76 | tcp: clear TCP_ECN_QUEUE_CWR in tcp_ecn_withdraw_cwr | net/ipv4/tcp_input.c |
| 3930b48a34 | tcp: fix marked lost packets not retransmitted（hint 悬空）| net/ipv4/tcp_input.c |
| 43729e6fea | memcg: fix use-after-free in mem_cgroup_iter | mm/memcontrol.c |

### swapfile 批（commit b4033b8cf4）
| 上游 sha | 内容 | 文件 |
|---|---|---|
| da4e1e3f62 | swap: fix swapfile read/write offset（统一 map_swap_page）| mm/page_io.c, mm/swapfile.c |

### 第三批（commit bb10620f56）
| 上游 sha | 内容 | 文件 |
|---|---|---|
| ff4c63f3e8 | dwc3: fix kernel panic when reboot（remove 顺序）| drivers/usb/dwc3/core.c |
| 5f27f97dfe | input: safety guards to input_set_keycode（keycode 越界）| drivers/input/input.c |
| 0698560f03 | clk: qcom rcg2: don't crash if parent not found | drivers/clk/qcom/clk-rcg2.c |

### 第四批（commit 57028795cf）
| 上游 sha | 内容 | 文件 |
|---|---|---|
| f01ddd4824 | random: fix data race on crng init time | drivers/char/random.c |
| 776ce24f7b | random: fix RNDRESEEDCRNG ioctl（用 input_pool）| drivers/char/random.c |
| b1ba1298d5 | timer: fix wheel index calculation on last level | kernel/time/timer.c |
| 75be612c1e | timer: read jiffies once when forwarding base clk | kernel/time/timer.c |
| 66be43d818 | fork: fix copy_process(CLONE_PARENT) race with exiting real_parent | kernel/fork.c |

### 第五批（commit 7f5ea12afd）
| 上游 sha | 内容 | 文件 |
|---|---|---|
| cc24d74e50 | genirq: fix kref leaks on irq affinity notifiers | kernel/irq/manage.c |
| c1f7ffbf97 | printk: do not lose last line in kmsg buffer dump | kernel/printk/printk.c |
| 5a097d6437 | perf: fix race in perf_mmap_close（ABA）| kernel/events/core.c |

### 追加批（commit a459e56dba）
| 上游 sha | 内容 | 文件 |
|---|---|---|
| df5e2d32c5 | zsmalloc: fix migrated zspage statistics（跨 zone 计数）| mm/zsmalloc.c |
| a09ae03e61 | ufs: fix race conditions related to driver data（early dev_set_drvdata）| drivers/scsi/ufs/ufshcd.c, ufshcd-pltfrm.c |
| e12119e721 | tcp: fix overestimation of TCP option space（md5-sack）| net/ipv4/tcp_output.c |

## 筛选方法（可复用）

1. `gregkh/linux compare v4.9.186...v4.9.337`（10326 条）按路径 + 时间过滤
2. 路径选择：日用内核热路径（mm/、kernel/sched、net/ipv4、drivers/scsi/ufs、drivers/char、drivers/net/tun ...）
3. 逐条验证：186 树是否已含（CAF 常已 backport）→ 锚点匹配 → 纯修复无行为变化
4. 排除典型：互逆中间态（thermal ×2 已含最终态）、结构不匹配（free_pcppages_bulk、sched/core CAF 版）、设备不匹配（wcn36xx vs WCN3990）、THP 全家（845 关 THP）

## 引用面安全验证

- **map_swap_page**（唯一改语义的导出符号）：引用面穷举 = swapfile.c 定义 + page_io.c ×3，全部配套
- 其余改动函数全部 static 或签名不变
- 与自移植代码文件域唯一重叠：drivers/scsi/ufs/ufshcd.c（UFS 双补丁 + 4 个 stable 补丁，改点分布在不同函数，无冲突）