
有关磁盘的更多详细信息，请参阅[关于虚拟机的磁盘和 VHD](/documentation/articles/virtual-machines-disks-vhds)。

##<a id="attachempty"></a>如何：附加空磁盘

附加空磁盘是添加数据磁盘的简单方法，因为 Azure 将为你创建 .vhd 文件并将其存储在存储帐户中。

1. 单击“虚拟机”，然后选择相应的 VM。

2. 在命令栏上，单击“附加”，然后单击“附加空磁盘”。


	![附加空磁盘](./media/howto-attach-disk-window-linux/AttachEmptyDisk.png)

3.	将显示“附加空磁盘”对话框。


	![附加新的空磁盘](./media/howto-attach-disk-window-linux/AttachEmptyDetail.png)


	请执行以下操作：

	- 在“文件名”中，接受默认名称或为 .vhd 文件键入另一个名称。数据磁盘使用自动生成的名称，即使你为 .vhd 文件键入另一个名称。

	- 键入数据磁盘的**大小 (GB)**。

	- 单击复选标记以完成。

4.	创建并附加数据磁盘后，它将列在 VM 的仪表板中。

	![已成功附加了空数据磁盘](./media/howto-attach-disk-window-linux/AttachEmptySuccess.png)

> [AZURE.NOTE] 添加新数据磁盘后，你需要登录到 VM 并初始化该磁盘，以便可以使用它。


##<a id="attachexisting"></a>如何：附加现有磁盘

附加现有磁盘需要存储帐户中具有可用的 .vhd。使用 [Add-AzureVhd](https://msdn.microsoft.com/zh-cn/library/azure/dn495173.aspx) cmdlet 将 .vhd 文件上载到存储帐户。在创建并上载 .vhd 文件后，你可以将其附加到 VM。

1. 单击“虚拟机”，然后选择相应的虚拟机。

2. 在命令栏中，单击“附加”，然后选择“附加磁盘”。


	![附加数据磁盘](./media/howto-attach-disk-window-linux/AttachExistingDisk.png)


3. 选择数据磁盘，然后单击复选标记以附加该数据磁盘。

	![输入数据磁盘详细信息](./media/howto-attach-disk-window-linux/AttachExistingDetail.png)

4.	附加数据磁盘后，它将列在 VM 的仪表板中。


	![已成功附加了数据磁盘](./media/howto-attach-disk-window-linux/AttachExistingSuccess.png)

<!---HONumber=Mooncake_0411_2016-->