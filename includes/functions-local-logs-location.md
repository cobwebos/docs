当 Functions 主机在本地运行时，它会将日志写入以下路径：

```
<DefaultTempDirectory>\LogFiles\Application\Functions
```

在 Windows 上，`<DefaultTempDirectory>` 是 TMP、TEMP、USERPROFILE 环境变量或 Windows 目录的第一个找到的值。
在 MacOS 或 Linux 上，`<DefaultTempDirectory>` 是 TMPDIR 环境变量。

[!Note]
当 Functions 主机启动时，它将覆盖目录中的现有文件结构。