# 轻语手写轨迹数据集

为轻语输入法手写识别收集**自愿、匿名、可申请删除**的在线手写轨迹样本，用于离线训练与评测。

> 当前状态：格式 v1 已定稿。客户端支持默认关闭的本机保存与手动导出；自动上传需在设置中明确同意，经腾讯云函数写入**私有训练数据仓库**，不进入本公开仓库。

## 为什么需要轨迹

公开字形库（如 HWDB）偏重书写规范，真实使用中的笔顺、连笔和屏幕书写习惯往往与之不同。轨迹样本体积小、可重新渲染成离线模型需要的灰度图，也不含屏幕上的其他内容，比截图更适合作为训练与评测数据。

## 数据边界

样本**只包含**：

- 归一化的笔迹坐标、笔画分隔和相对时间；
- 目标字（v1 为单个汉字）；
- 采集日期（仅到日）、可选的客户端版本与屏幕密度。

**不包含**：账号、设备标识、IMEI / Android ID、通讯录、剪贴板、输入框内容、位置、精确时间和屏幕截图。密码等 `allowsSuggestions=false` 的输入框不采集、不上传。

自动上传默认关闭，首次开启需明确同意；只上传开启之后新产生的样本。详见 [PRIVACY.md](PRIVACY.md)。

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

注意：本仓库是公开仓库，PR 中的样本对所有人可见。不愿公开的样本可在 App 中开启自动上传，进入私有训练数据仓库，不要提交 PR。

## 自动上传（需明确同意）

轻语客户端支持自动上传，默认关闭。首次开启会列出上传字段和去向；只上传开启之后新产生的样本，默认仅在 Wi-Fi 下上传，可随时关闭。

样本经腾讯云函数写入维护者的**私有训练数据仓库**，不进入本公开仓库、不公开展示。GitHub 凭证只存在云函数环境变量，不进入 APK；云函数不记录请求体。删除流程见 [PRIVACY.md](PRIVACY.md)。

## 许可

除另有说明外，本仓库的文档、Schema、示例与通过 PR 贡献的样本均按 [MIT License](LICENSE) 授权，可用于训练、评测及商业用途。提交 PR 即表示你确认样本由本人书写，并同意以上授权。

App 自动上传到私有训练数据仓库的样本**不适用**本仓库的 MIT 许可，其使用、保留和删除由 App 内的同意说明及私有仓库的数据使用说明约束。

## English

This repository defines an opt-in, anonymized online-handwriting trajectory format for Qingyu IME. Public pull-request samples are contributed under the MIT License. The app's optional automatic upload requires explicit consent and writes to a private training-data repository, not to this public repository. See [docs/sample-format.md](docs/sample-format.md) and [PRIVACY.md](PRIVACY.md).
