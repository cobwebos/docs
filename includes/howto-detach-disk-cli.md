<properties writer="kathydav" editor="tysonn" manager="timlt" />



# 如何使用 CLI 从虚拟机中分离数据磁盘

当你不再需要附加到虚拟机的数据磁盘时，你可以轻松地分离它。这将从虚拟机中删除磁盘，但不会从存储中删除它。若果你希望再次使用磁盘上的现有数据，可以将其重新附加到相同的虚拟机或另一个虚拟机。

> [AZURE.NOTE] Azure 中的虚拟机使用不同类型的磁盘 - 操作系统磁盘、本地临时磁盘和可选数据磁盘。数据磁盘是为虚拟机存储数据的推荐方法。有关磁盘的详细信息，请参阅[关于磁盘和映像](https://msdn.microsoft.com/zh-cn/library/dd163896)。目前无法分离操作系统磁盘。


1. 获取已附加到 VM 的磁盘列表：

        vm disk list <vm-name>

    如果省略 `<vm-name>`，你将会得到订阅中所有磁盘的列表。


2. 分离磁盘：

        vm disk detach <vm-name> <lun>

    `lun` 标识要分离的磁盘，它是可以在 VM 磁盘列表中找到的一个数字。

包括终端输出的示例演练：

    ~$ azure vm disk list kmlinux
    info:    Executing command vm disk list
    + Fetching disk images
    + Getting virtual machines
    + Getting VM disks
    data:    Lun  Size(GB)  Blob-Name                               OS
    data:    ---  --------  --------------------------------------  -----
    data:         30        kmlinux-kmlinux-2015-02-05.vhd          Linux
    data:    1    5         kmlinux-f8ef0006ab182209.vhd
    data:    2    7         kmlinux-602362868dbb7439.vhd
    info:    vm disk list command OK
    ~$ azure vm disk detach kmlinux 2
    info:    Executing command vm disk detach
    + Getting virtual machines
    + Removing Data-Disk
    info:    vm disk detach command OK
    ~$ azure vm disk list kmlinux
    info:    Executing command vm disk list
    + Fetching disk images
    + Getting virtual machines
    + Getting VM disks
    data:    Lun  Size(GB)  Blob-Name                               OS
    data:    ---  --------  --------------------------------------  -----
    data:         30        kmlinux-kmlinux-2015-02-05.vhd          Linux
    data:    1    5         kmlinux-f8ef0006ab182209.vhd
    info:    vm disk list command OK
