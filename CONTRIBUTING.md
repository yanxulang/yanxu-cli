# 参与贡献

## 环境

安装言序 1.1.6 或更高版本。稳定版修改必须同时在最低版本与当前稳定版通过；不得用只在较新运行时存在的语法或标准库入口悄悄抬高下限。

## 本地门禁

```sh
yanxu 包 .
find src tests examples benchmarks integration -type f -name '*.yx' -print
yanxu 查 src/言令.yx
yanxu 试 tests --json
yanxu 兼容 tests --json
yanxu 兼容 examples --json
yanxu 兼容 benchmarks --json
yanxu 文 src/言令.yx /tmp/API.md
cmp docs/API.md /tmp/API.md
yanxu 编 . -o /tmp/yanxu-cli.yxb --release
```

每个新增公共行为至少覆盖树解释器、字节码 VM、错误路径和相关边界。修改解析状态机时还应验证补全状态机保持同一语义；修改公开声明时重新生成`docs/API.md`。

## 设计原则

- 保持纯言序、零权限和零第三方依赖，除非变更有明确必要性；
- 公开结果使用带`模式版本`的固定形状；
- 程序判断依赖稳定错误代码，不依赖消息文本；
- 规格和结果不得暴露可回写内部状态的可变引用；
- 所有循环、递归替代结构和输出都必须有资源预算；
- 不在库内加入 shell 执行、文件扫描或隐式环境读取。

## 提交与评审

一次提交只处理一个可独立验证的主题。PR 应说明兼容影响、测试证据、资源影响和文档变化；破坏模式字段、默认解析行为或错误代码语义的修改必须明确按主版本处理。
