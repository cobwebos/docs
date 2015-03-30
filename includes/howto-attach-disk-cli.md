
有关磁盘的详细信息，请参阅[关于 Azure 中的虚拟机磁盘](https://msdn.microsoft.com/zh-cn/library/azure/dn790303.aspx)。

## <a id="cliattachempty"></a>如何：附加空磁盘
附加空磁盘是添加数据磁盘的较为简单的方法。运行以下命令即可附加新的空磁盘：

    vm disk attach-new <vm-name> <size-in-gb> [blob-url]

将 `vm-name` 替换为虚拟机的名称，将 `size-in-gb` 替换为新磁盘的大小。你可以选择使用 Blob URL 作为显式指定要创建的目标 Blob 的最后一个参数。如果你不指定 Blob URL，将自动生成一个 Blob 对象。  

运行以下命令，以验证是否已创建你的磁盘：

    vm disk list <vm-name>

以下是上述命令的示例演练（包括终端输出）：

    ~$ azure vm disk attach-new pinkylinux 20 http://pinkylinux.blob.core.windows.net/vhds/pinkydisk1.vhd
    info:   Executing command vm disk attach-new
    + Getting virtual machines
    + Adding Data-Disk
    info:   vm disk attach-new command OK
    ~$ azure vm disk list pinkylinux
    info:    Executing command vm disk list
    + Fetching disk images
    + Getting virtual machines
    + Getting VM disks
    data:    Lun  Size(GB)  Blob-Name                               OS
    data:    ---  --------  --------------------------------------  -----
    data:         30        pinkylinux-pinkylinux-2015-02-05.vhd    Linux
    data:    0    5         pinkydisk1.vhd
    data:    1    20        pinkylinux-f8ef0006ab182209.vhd
    info:    vm disk list command OK
