# rothko LineageOS 23.2 公开协作入口

这是 Redmi K70 Ultra（`rothko`）的非官方源码协作套件，不是 ROM 发布包，也不表示已达到 LineageOS official 要求。系统框架为 Android 16，原厂 vendor 为 Android 14 / API 34，固件基线为 **OS3.0.307.0.WNNCNXM**。

协作套件由四个仓库组成：

- `android_device_xiaomi_rothko`：设备配置、服务、overlay、策略及提取工具。
- `proprietary_vendor_xiaomi_rothko`：vendor 构建元数据和可移植来源账本，不含专有二进制。
- `rothko-kernel-extraction`：原厂内核材料提取配方，不含内核源码或二进制。
- `rothko-lineage-integration`：固定 manifest、平台补丁、提取与构建说明、验证范围。

**只拿设备树不能复现这套系统。** 按 [BUILD.md](BUILD.md) 同步固定版本并运行提取流程；各仓库提交号记录在 `baseline.json`。原厂内核仓库是提取配方，不是可编译的内核源码。

剩余报告和验收边界见 [KNOWN_ISSUES.md](KNOWN_ISSUES.md)。此前 ROM 的实机结果不自动代表当前源码组合已验证；当前候选实际完成了哪些检查，见 [VALIDATION.md](VALIDATION.md)。

仓库不包含原厂线刷包、校准数据、modem NV、设备身份、生物识别模板、签名私钥或手机日志。参与者需自行准备匹配固件并在本机提取；不要把这些私有输入提交到仓库。源码准备工具不刷手机、不切槽、不操作 bootloader。

公开源码不代表获得原厂二进制再分发许可，也不代表完整 ROM 构建、实机验收、硬件功能完全等价或官方设备资格已通过。
