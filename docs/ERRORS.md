# 错误目录

## 结构

言令自身错误消息以`YANLING_<代码>：`开头。`错误详情(所误)`返回：

```text
代码、消息、源代码、类别、位置、踪迹
```

只有`代码`是程序分支接口；消息可在补丁版本改进。无法识别为稳定前缀的运行时错误使用`YANLING_RUNTIME`，原始运行时信息仍保留在其余字段。

## 规格错误

| 代码 | 条件 |
| --- | --- |
| `YANLING_SPEC_NAME` | 命令、选项、值名或参数名称不合法 |
| `YANLING_SPEC_SHORT` | 短名不是空文或单个有效字符 |
| `YANLING_SPEC_TEXT` | 说明含空字符 |
| `YANLING_SPEC_RESERVED` | 占用内建帮助名称或短名 |
| `YANLING_SPEC_DUPLICATE` | 重复长名、短名、参数名或子命令名 |
| `YANLING_SPEC_ORDER` | 必需/可选/余项位置参数顺序无效 |
| `YANLING_SPEC_MODE` | 必需根位置参数与子命令模式冲突 |
| `YANLING_SPEC_TYPE` | 取值类型不是`文/数/理/JSON` |
| `YANLING_SPEC_DEFAULT` | 默认值类型错误、不可序列化或超过容器预算 |
| `YANLING_SPEC_CYCLE` | 子命令关系形成环 |
| `YANLING_SPEC_DEPTH` | 子命令树超过 32 层 |
| `YANLING_SPEC_LIMIT` | 名称外的规格数量或文本上限被超过 |

规格错误发生在构建器调用中。构建器保证失败前不追加该定义，但同一链中此前已经成功的定义仍保留。

## 配置与输入预算

| 代码 | 条件 |
| --- | --- |
| `YANLING_CONFIG_KEY` | 解析配置含未知键 |
| `YANLING_CONFIG_VALUE` | 配置值不是逻辑值 |
| `YANLING_ARG_LIMIT` | argv 数量、单项字符或总字符超限 |
| `YANLING_SUGGEST_LIMIT` | 建议或补全前缀超过 128 字 |
| `YANLING_HELP_LIMIT` | 最终帮助超过 1048576 字 |

这些错误表示调用契约或资源策略问题，通常不应通过重试解决。

## 值转换

| 代码 | 条件 |
| --- | --- |
| `YANLING_VALUE_NUMBER` | 取值不是 JSON 数字 |
| `YANLING_VALUE_BOOLEAN` | 取值不是支持的逻辑文字 |
| `YANLING_VALUE_JSON` | 取值不是合法 JSON |

错误消息可能包含被拒绝的原始参数。写日志前按应用数据分类进行脱敏，不要把完整消息直接作为低基数指标标签。

## 解析错误

| 代码 | 条件 |
| --- | --- |
| `YANLING_PARSE_UNKNOWN` | 未知长或短选项；长选项可能附建议 |
| `YANLING_PARSE_OPTION` | 开关附值或对取值选项使用否定等非法选项形状 |
| `YANLING_PARSE_MISSING` | 取值选项缺少下一个值 |
| `YANLING_PARSE_DUPLICATE` | 单值选项重复，或严格模式下开关重复 |
| `YANLING_PARSE_REQUIRED` | 必需选项或位置参数缺失 |
| `YANLING_PARSE_POSITIONAL` | 位置参数超过声明容量 |

内建帮助在默认配置下早于必需项检查返回，因此`--help`不是`PARSE_REQUIRED`。关闭帮助短路后它会成为`PARSE_UNKNOWN`。

## 处理模式

```yanxu
定 尝试：典 为 命令.尝试解析（参数）；

若 非 尝试【「成功」】 则
    定 详情：典 为 尝试【「错误详情」】；
    若 （详情【「代码」】 等于 「YANLING_PARSE_REQUIRED」） 则
        # 选择应用自己的退出码
    终
终
```

库不规定退出码。常见 CLI 可把规格/配置错误视为内部配置失败，把值与解析错误视为用户输入失败，把`RUNTIME`视为未分类运行时故障。
