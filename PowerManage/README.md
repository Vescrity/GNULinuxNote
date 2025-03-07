# 电源管理

记录一些常用的电源配置。

## TLP

我使用 tlp 来做电源管理。为便于理解各项作用使用了 tlpui 。

### CPU

- `CPU_SCALING_.*_FREQ_ON_.*` 限制频率
以及在 `CPU_ENERGY_PERF_POLICY_ON_.*` 中选择一个合适的策略。

### 显卡

插电后温度一直在 50 度以上这个问题困扰过我很久。最后发现问题在 NVIDIA 卡上。

在 PCIe 的项目中启用 RUNTIME PM 即可。亦可只针对 NVIDIA 进行 RUNTIME PM。

