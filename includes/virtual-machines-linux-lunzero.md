在数据磁盘添加到 Linux VM 时，如果磁盘不存在在 LUN 0 可能会遇到错误。 如果要添加的磁盘使用手动`azure vm disk attach-new`命令，你指定的 LUN (`--lun`) 而不是允许 Azure 平台来确定相应的 LUN，负责磁盘已存在 / 在 LUN 0 将存在。 

考虑下面的示例显示的输出的代码片段`lsscsi`:

```bash
[5:0:0:0]    disk    Msft     Virtual Disk     1.0   /dev/sdc 
[5:0:0:1]    disk    Msft     Virtual Disk     1.0   /dev/sdd 
```

在 LUN 0 和 LUN 1 存在两个数据磁盘 (中的第一列`lsscsi`输出详细信息`[host:channel:target:lun]`)。 这两个磁盘应 accessbile 从 VM 中。 如果您已手动指定要在 LUN 1 添加的第一个磁盘和相应的 LUN 2 处的第二个磁盘，你可能无法看到你的 VM 中的正确从磁盘。

> [!NOTE]
> Azure`host`值为 5 在这些示例中，但具体取决于你选择的存储类型，这可能有所变化。
> 
> 

此磁盘行为不是 Azure 的问题，而在其中 Linux 内核遵循 SCSI 规范的方法。 在 Linux 内核扫描连接的设备的 SCSI 总线，设备必须在 LUN 0 让系统继续扫描其他设备上找到。 在这种情况下：

* 查看的输出`lsscsi`后添加数据磁盘来验证是否在 LUN 0 具有一个磁盘。
* 如果你的磁盘不会不正确显示在你的 VM，请验证磁盘位于 LUN 0。

