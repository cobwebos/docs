<properties writer="kathydav" editor="tysonn" manager="timlt" />



#如何从虚拟机分离数据磁盘 

- [步骤 1：找到磁盘](#finddisks)
- [步骤 2：分离数据磁盘](#detachdisk)

当您不再需要数据磁盘附加到虚拟机时，您可以轻松地分离它。这将从虚拟机中删除该磁盘，但并不会从存储中删除它。如果您想要再次使用现有的数据在磁盘上，可以将它重新连接到同一虚拟机，或者单击另一个。  

> [WACOM.NOTE] Azure 中的虚拟机使用各种类型的磁盘-操作系统磁盘、 本地临时磁盘以及可选数据磁盘。数据磁盘是推荐的方式，用于存储虚拟机的数据。有关磁盘的详细信息，请参阅[有关磁盘和映像][]。有关说明，请参阅[如何将数据磁盘附加到虚拟机][attachdisk]。

## <a id="finddisks"> </a>步骤 1：查找磁盘 # #


如果您不知道，或想要验证的磁盘的名称，使其脱离对象之前，请按照下列步骤。 

> [WACOM.NOTE] Azure 自动时将其附加到磁盘分配一个名称。名称由云服务名称、 虚拟机的名称和一个数字组成。

1. 如果还没有做，请登录到 Azure[管理门户](http://manage.windowsazure.com)。 

2. 单击**虚拟机**，然后选择相应的虚拟机。此时将打开虚拟机的仪表板。

3. 在下**磁盘**，表将列出名称和类型的连接的所有磁盘。例如，此屏幕显示了一个操作系统 (OS) 磁盘而另一个数据磁盘的虚拟机：
		
	![Find data disk](./media/howto-detach-disk-windows-linux/FindDataDisks.png)	


## <a id="detachdisk"> </a>步骤 2：分离磁盘 # #

您发现的磁盘的名称后，您就可以分离该磁盘：

1. 单击**虚拟机**，选择具有要分离数据磁盘的虚拟机。
2. 从命令栏中，单击**分离该磁盘**。

2. 选择，数据磁盘，然后单击复选标记以分离它。


	![Detach disk details](./media/howto-detach-disk-windows-linux/DetachDiskDetails.png)

磁盘保留在存储中，但不再附加到虚拟机。



[attachdisk]:/zh-cn/manage/windows/how-to-guides/attach-a-disk/

[有关磁盘和映像]:http://go.microsoft.com/fwlink/p/?LinkId=263439

