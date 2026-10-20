# samples/inbox

只接受匿名化、由书写者本人提供的轨迹批次。

- 文件名：`YYYYMMDD-<8位随机小写hex>.jsonl`；
- 一行一个样本，格式见 [../docs/sample-format.md](../docs/sample-format.md)；
- 提交前按 [../CONTRIBUTING.md](../CONTRIBUTING.md) 自检；
- 本目录是公开的，合并后任何人可见；不愿公开请等待客户端匿名上传通道。

维护者合并前会检查字段范围、时间单调性、`id` 唯一性和标签正确性；不符合的批次会退回。
