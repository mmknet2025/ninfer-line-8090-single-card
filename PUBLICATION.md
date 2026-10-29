# 本地推理生产线①：单卡长上下文线（KVMem）

> ⚠️ **本仓与现役生产不同源（2026-10-29 起）**
>
> - **本仓内容** = `5258MF/ninfer-rtx3060-27b` 的**源码镜像快照**（2026-10-13 `78b2544e`，RTX 3060 懒人包线）。
> - **本机现役 8090** 已于 2026-10-28 换线到 **`kvmem/kvmem` 产品线**（`iamwavecut/ninfer-all@63428ac2` + **20 补丁** → 树 `d976d3f8b206` → 二进制 `a6dffc2d`）。**那份源码不在本仓**，在本机 `~/src/kvmem-product/repo/backends/ninfer`。
> - 现役状态、参数与实测基线见 `local-ai-production-lines` 仓的 `docs/CURRENT-STATE.md`。
> - 单卡线只跟 `kvmem/kvmem` 的 `backends/versions.json` pin 升级，**不自行前滚 `iamwavecut/ninfer-all` master**（KVMem 与 TP 源码层互斥）。

## 角色

单张 RTX 5060 Ti 16GB 上跑 27B，靠引擎内建 **KVMem 分页 KV 池**换长上下文。

## 生产配置（现役）

- 端口 `8090`，ctx `262144`（显存 KV 池 65,536，超出走 `--kvmem-host-mib 16384` 的 host KV）
- 投机 `--spec mtp --draft-tokens 4`
- 单元 `ninfer-prod`（**路由器按需拉起的瞬时单元**，`systemd-run --user --collect`）
- 冷启 ≈4.3 s，显存 14,885 MiB，RSS 3.6 GB

## 模型工件（不进本仓）

`swift-qwen38-rco-iq3s.ninfer`，15,017,456,128 B，Qwen3.8-27B Swift-1.5 GSQ-RCO IQ3_S。
该工件 header 同时带 `Qwen3_5MTP` 与 `DFlash2DraftModel`（5 层）两套草稿，可 `--spec mtp|dflash2` 直接对跑；实测单卡保持 MTP（接受率 37.5% vs 33.5%，且 DFlash2 多占 1.8 GiB 使上下文腰斩）。

## 来源与许可

本仓 fork 自 `5258MF/ninfer-rtx3060-27b`（公开，**该上游未附许可文件**），再上游为 `Neroued/ninfer`。故本仓继承其许可状态（未附许可文件）。

## 快照点

- 本仓：`78b2544e`（2026-10-13，上游 main）
- 历史快照：分支 `upgrade-013-20261005` @ `c7f5a1ba`（2026-10-12）

## 不含什么

编译产物（生产二进制 684 MB）与模型权重都不进仓。
