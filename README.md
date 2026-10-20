# 轻语手写轨迹数据集

为轻语输入法手写识别收集**自愿、匿名、可申请删除**的在线手写轨迹样本，用于离线训练与评测。

> 当前状态：格式 v1 已定稿，客户端上传通道尚未开放。本仓库先作为样本格式、授权与隐私边界的参考。

## 为什么需要轨迹

公开字形库（如 HWDB）偏重书写规范，真实使用中的笔顺、连笔和屏幕书写习惯往往与之不同。轨迹样本体积小、可重新渲染成离线模型需要的灰度图，也不含屏幕上的其他内容，比截图更适合作为训练与评测数据。

## 数据边界

样本**只包含**：

- 归一化的笔迹坐标、笔画分隔和相对时间；
- 目标字（v1 为单个汉字）；
- 采集日期（仅到日）、可选的客户端版本与屏幕密度。

**不包含**：账号、设备标识、IMEI / Android ID、通讯录、剪贴板、输入框内容、位置、精确时间和屏幕截图。密码等 `allowsSuggestions=false` 的输入框不采集、不上传。

上传默认关闭，必须由用户逐次明确同意。详见 [PRIVACY.md](PRIVACY.md)。

## 样本格式

- 规范：[docs/sample-format.md](docs/sample-format.md)
- JSON Schema：[schema/sample.schema.json](schema/sample.schema.json)
- 示例：[examples/sample.jsonl](examples/sample.jsonl)（全部为合成数据，不是真实用户样本）

一行一个 JSON 对象（JSONL），UTF-8 编码：

```json
{"format":"qingyu-hw-trajectory","version":1,"id":"6f1c2a9e-7b1d-4c55-9a2e-1d4b6c8e9f01","label":"永","created_at":"2026-10-20","canvas":{"aspect":1.0,"unit":"normalized"},"strokes":[[[0.50,0.18,0],[0.50,0.82,160]],[[0.36,0.36,220],[0.50,0.30,270],[0.64,0.36,320]]]}
```

## 参与方式

1. 阅读 [CONTRIBUTING.md](CONTRIBUTING.md) 和 [PRIVACY.md](PRIVACY.md)。
2. 按规范导出**你自己书写**的样本，去掉任何个人元数据。
3. 将一批 JSONL 文件放到 `samples/inbox/`，提交 Pull Request。
4. 需要删除时，通过 Issue 提供样本 `id`，流程见 [PRIVACY.md](PRIVACY.md)。

注意：本仓库是公开仓库，PR 中的样本对所有人可见。不愿公开的样本请等待客户端的匿名上传通道。

## 许可

除另有说明外，本仓库的文档、Schema、示例与贡献的样本均按 [MIT License](LICENSE) 授权，可用于训练、评测及商业用途。提交即表示你确认样本由本人书写，并同意以上授权。

## English

This repository defines an opt-in, anonymized online-handwriting trajectory format for Qingyu IME and collects contributed samples under the MIT License. Only normalized points, stroke boundaries, relative time, a target character, and coarse metadata are accepted. See [docs/sample-format.md](docs/sample-format.md) and [PRIVACY.md](PRIVACY.md).
