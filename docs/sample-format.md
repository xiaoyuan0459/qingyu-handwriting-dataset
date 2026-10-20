# 手写轨迹样本格式 v1

- 格式标识：`qingyu-hw-trajectory`
- 版本：`1`
- 编码：UTF-8（无 BOM），LF 换行
- 组织方式：JSONL，一行一个 JSON 对象
- JSON Schema：[../schema/sample.schema.json](../schema/sample.schema.json)

## 字段

| 字段 | 类型 | 必填 | 约束与说明 |
| --- | --- | --- | --- |
| `format` | string | 是 | 固定为 `qingyu-hw-trajectory` |
| `version` | integer | 是 | 固定为 `1`；消费方必须拒绝未知版本 |
| `id` | string | 是 | 小写 UUIDv4，全局唯一，随机生成，不得由用户或设备信息派生 |
| `label` | string | 是 | v1 为单个汉字，Unicode NFC |
| `created_at` | string | 否 | 采集日期，`YYYY-MM-DD`，不含时分秒 |
| `canvas` | object | 是 | 坐标归一化信息 |
| `canvas.aspect` | number | 是 | 画布宽高比 `width / height`，大于 0 |
| `canvas.unit` | string | 是 | 固定为 `normalized` |
| `device` | object | 否 | 仅用于复现渲染条件 |
| `device.platform` | string | 否 | 固定为 `android` |
| `device.app_version` | string | 否 | 客户端版本，1–32 个字符 |
| `device.screen_density` | number | 否 | 屏幕密度，大于 0 |
| `strokes` | array | 是 | 笔画数组，1–64 笔，数组顺序即书写顺序 |
| `strokes[i]` | array | 是 | 单笔轨迹，2–2048 个点 |
| `strokes[i][j]` | array | 是 | 单个点 `[x, y, t]`，恰好 3 个元素 |

单个样本总点数不超过 8192。

## 坐标

- `x = 落点像素 X / 画布宽度`，`y = 落点像素 Y / 画布高度`，范围均为 `0..1`；
- `x` 自左向右、`y` 自上向下；
- 归一化保留 `canvas.aspect`，重新渲染时按该宽高比还原，避免字形被拉伸；
- 不要求笔迹铺满画布，也不在格式层做居中或缩放；训练预处理可自行裁剪与加边距。

## 时间

- `t` 是该点相对**样本第一个点**的毫秒数，必须大于等于 0；
- 按 `strokes` 顺序展开后，`t` 必须全局不递减；同一时刻的多个点允许相等；
- 不记录绝对时间、时区或设备开机时长。

## 笔画

- 一个内层数组是一笔；数组之间的间隔表示抬笔；
- 每笔至少 2 个点；
- v1 不记录压感、倾角、笔宽和橡皮擦轨迹。

## 标签

- v1 只接受单个汉字，Unicode NFC；
- 当前客户端识别 3755 个常用字，超范围的字暂不接收；
- 多字词语、标点和英文留给后续版本，消费方遇到不认识的 `label` 应拒绝该样本而不是猜测。

## 文件与命名

- 每行一个样本，行内不得换行；
- 批次文件放在 `samples/inbox/`，命名为 `YYYYMMDD-<8位随机小写hex>.jsonl`；
- 同一 `id` 在仓库内只能出现一次；
- 不得在 JSON 之外附加文件名、注释或设备日志。

## 语义校验

JSON Schema 无法表达全部约束，提交前还需检查：

1. `t` 全局不递减；
2. `label` 是单个 Unicode 标量值（不是由多个码位拼成的组合字）；
3. `id` 在仓库内唯一；
4. 单样本总点数不超过 8192；
5. 不存在 Schema 之外的字段。

## 版本演进

- 任何不兼容变更（字段含义、坐标或时间语义、必填项）都必须提升 `version`；
- 新增可选字段也提升 `version`，避免旧客户端误读；
- 消费方必须按 `version` 分支处理，遇到 `version > 1` 直接拒绝。

## 示例

```json
{"format":"qingyu-hw-trajectory","version":1,"id":"6f1c2a9e-7b1d-4c55-9a2e-1d4b6c8e9f01","label":"永","created_at":"2026-10-20","canvas":{"aspect":1.0,"unit":"normalized"},"device":{"platform":"android","app_version":"example","screen_density":2.75},"strokes":[[[0.50,0.18,0],[0.50,0.82,160]],[[0.36,0.36,220],[0.50,0.30,270],[0.64,0.36,320]],[[0.30,0.52,380],[0.50,0.46,440],[0.70,0.52,500]],[[0.42,0.58,560],[0.34,0.74,620],[0.28,0.84,680]],[[0.58,0.58,740],[0.66,0.74,800],[0.72,0.84,860]]]}
```

更多见 [../examples/sample.jsonl](../examples/sample.jsonl)，其中全部为合成数据。

## 训练侧说明（非格式约束）

离线识别模型当前输入为 64×64 灰度图。渲染轨迹时应保持宽高比、按笔画顺序绘制、线宽随画布缩放；具体裁剪、线宽和灰度化参数属于训练代码，不属于 v1 格式。在线识别研究可以直接使用 `strokes` 与 `t`。
