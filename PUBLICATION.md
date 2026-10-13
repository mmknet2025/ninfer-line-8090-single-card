# 本地推理生产线①：单卡长上下文线（KVMem）

- **角色**：单张 RTX 5060 Ti 16GB 上跑 27B，靠引擎内建 **KVMem 分页 KV 池**换长上下文。
- **模型工件（未包含在本仓）**：`swift-qwen38-rco-iq3s.ninfer`，15.0 GB，Qwen3.8-27B Swift-1.5 GSQ-RCO IQ3_S。
- **生产配置**：端口 `8090`，ctx `262144`，单元名 `ninfer-prod`（**由路由器按需拉起的瞬时单元**，`systemd-run --user --collect`，仓内无静态 unit 文件），需 4 个 `NINFER_KVMEM_*` + `HOST_KV_MIB=16384`。
- **本仓内容**：引擎源码快照（**不含**编译产物；生产二进制 1.37 GB 不进仓）。
- **来源与许可**：fork 自 `5258MF/ninfer-rtx3060-27b`（公开，**该上游未附许可文件**），再上游为 `Neroued/ninfer`。故本仓继承其许可状态（未附许可文件）。
- **快照点**：分支 `upgrade-013-20261005` @ `c7f5a1ba`（2026-10-12），本仓库以 `main` 发布。改动细节见 `git log`。
