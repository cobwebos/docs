#如何将数据磁盘附加到虚拟机




- [概念](#concepts)
- [How to 文章：附加现有磁盘](#attachexisting)
- [How to 文章：附加空磁盘](#attachempty)
- [How to 文章：在 Windows Server 2008 R2 中初始化新的数据磁盘](#initializeinWS)
- [How to 文章：初始化新的数据磁盘在 Linux 中](#initializeinlinux)



##<a id="concepts"></a>概念



你可以将数据磁盘附加到虚拟机以存储应用程序数据。数据磁盘是您可以创建使用您自己的计算机在本地或云中使用 Azure 的虚拟硬盘 (VHD)。你管理虚拟机中数据磁盘的方式与管理办公室中服务器的方式相同。

您可以使用管理门户上载并附加到虚拟机中，数据包含的数据磁盘，以及从虚拟机使用的同一个存储帐户中添加空磁盘。本文介绍了这些过程。若要附加空磁盘位于不同的存储帐户，请使用[Add-azuredatadisk](http://msdn.microsoft.com/library/azure/dn495298.aspx) cmdlet，Azure PowerShell 模块中提供。若要下载该模块，请参阅[下载](http://www.windowsazure.cn/zh-cn/downloads/?sdk=net)页。

添加磁盘时不会停止虚拟机。你可以附加到虚拟机中的磁盘数量是根据虚拟机的大小确定的。有关虚拟机和磁盘大小的信息，请参阅[Azure 的虚拟机大小](http://msdn.microsoft.com/library/azure/dn197896.aspx)。

> [WACOM.NOTE]
> Azure 存储空间支持 blob 最多达到 1TB 的大小，可容纳最大虚拟大小为 999 GB 的 VHD。但是，如果使用 HYPER-V 来创建新的 VHD，你指定的大小表示虚拟大小。若要在 Azure 中使用 VHD，请指定不超过 999 GB 的大小。

**数据磁盘与资源磁盘**
数据磁盘驻留在 Azure 存储空间，并可用于文件和应用程序数据的持久性存储区。

每个虚拟机还具有一个临时的本地 * 资源磁盘 * 附加。由于资源磁盘上的数据不一定是持久的重新启动，它是经常使用的应用程序和用作数据的短暂和临时存储的虚拟机中运行的进程。它还用来为操作系统存储页面文件或交换文件。

在 Windows 上，资源磁盘被标记为**d:**驱动器。在 Linux 上，通常由 Azure Linux 代理管理并且自动装载到资源磁盘**/mnt/资源**（或**/mnt** Ubuntu 映像上）。请参阅[Azure Linux 代理用户指南](/zh-cn/documentation/articles/virtual-machines-linux-agent-user-guide/) 有关详细信息。

有关使用数据磁盘的详细信息，请参阅[管理磁盘和映像](http://msdn.microsoft.com/zh-cn/library/azure/jj672979.aspx)。

##<a id="attachexisting"></a>如何：附加现有磁盘


1. 如果尚未这样做，登录到 [Azure 管理门户](http://manage.windowsazure.cn)。

2. 单击**虚拟机**，然后选择您想要将磁盘附加到虚拟机。

3. 在命令栏中，单击**附加**，然后选择**附加磁盘**。


	![Attach data disk](./media/howto-attach-disk-window-linux/AttachExistingDiskWindows.png)

	**附加磁盘**对话框随即出现。



	![Enter data disk details](./media/howto-attach-disk-window-linux/AttachExistingDisk.png)

3. 选择您想要将附加到虚拟机的数据磁盘。
4. 单击复选标记以将数据磁盘附加到虚拟机。
  
 
	虚拟机的仪表板中将列出该数据磁盘。


	![Data disk successfully attached](./media/howto-attach-disk-window-linux/AttachSuccess.png)

##<a id="attachempty"></a>如何：附加空磁盘

已创建并上载要用作空磁盘的.vhd 文件后，可以将其附加到虚拟机。使用[Add-azurevhd](http://msdn.microsoft.com/library/azure/dn495173.aspx) cmdlet 将.vhd 文件上载到存储帐户。  

1. 单击**虚拟机**，然后选择您想要将数据磁盘附加到虚拟机。



2. 在命令栏中，单击**附加**，然后选择**附加空磁盘**。


	![Attach an empty disk](./media/howto-attach-disk-window-linux/AttachDiskWindows.png)



	**附加空磁盘**对话框随即出现。



	![Attach a new empty disk](./media/howto-attach-disk-window-linux/AttachNewDiskWindows.png)

 
3. 在**文件名**，可以接受自动生成的名称，或输入想要用于存储的 VHD 文件的名称。从 VHD 文件创建的数据磁盘始终使用自动生成的名称。



4. 在**大小**，输入数据磁盘的大小。 



5. 单击复选标记以附加空数据磁盘。

	虚拟机的仪表板中将列出该数据磁盘。


	![Empty data disk successfully attached](./media/howto-attach-disk-window-linux/AttachEmptySuccess.png)


> [WACOM.NOTE] 
> 添加数据磁盘后，您将需要登录到虚拟机并初始化磁盘，因此虚拟机可以将该磁盘用于存储。



##<a id="initializeinWS"></a>如何：在 Windows Server 中初始化新的数据磁盘

1. 使用中列出的步骤连接到虚拟机[如何登录到运行 Windows Server 的虚拟机上][登录]。



2. 在您登录后，打开**服务器管理器**，在左窗格中，展开**存储**，然后单击**磁盘管理**。



	![Open Server Manager](./media/howto-attach-disk-window-linux/ServerManager.png)



3. 右键单击**磁盘 2**，然后单击**初始化磁盘**。



	![Initialize the disk](./media/howto-attach-disk-window-linux/InitializeDisk.png)

4. 单击**确定**若要开始初始化过程。



5. 右键单击磁盘 2 的空间分配区域，单击**新建简单卷**，然后完成该向导使用默认值。
 

	![Initialize the volume](./media/howto-attach-disk-window-linux/InitializeDiskVolume.png)



	磁盘现在处于联机状态并且准备使用新的驱动器号。



	![Volume successfully initialized](./media/howto-attach-disk-window-linux/InitializeSuccess.png)




##<a id="initializeinlinux"></a>如何：在 Linux 中初始化新的数据磁盘



1. 使用中列出的步骤连接到虚拟机[如何登录到运行 Linux 的虚拟机上][logonlinux]。



2. 在 SSH 窗口中，键入以下命令，然后输入您创建的用于管理虚拟机的帐户的密码：

	`sudo grep SCSI /var/log/messages`

	你可以在所示消息中找到最后添加的数据磁盘的标识符。



	![Get the disk messages](./media/howto-attach-disk-window-linux/DiskMessages.png)



3. 在 SSH 窗口中，键入以下命令以创建新的设备，然后输入帐户密码：

	`sudo fdisk /dev/sdc`

	>[WACOM.NOTE] 在此示例中，您可能需要使用 sudo-i 在某些分发，如果 /sbin 或 /usr/sbin 不在您 $PATH 上。


4. 类型**n**若要创建一个新的分区。


	![Create new device](./media/howto-attach-disk-window-linux/DiskPartition.png)

5. 类型**p**若要将该分区设置为主分区，键入**1**以使其第一个分区，然后键入 enter 以接受柱面的默认值。


	![Create partition](./media/howto-attach-disk-window-linux/DiskCylinder.png)



6. 键入 **p**以查看有关分区磁盘的详细信息。


	![List disk information](./media/howto-attach-disk-window-linux/DiskInfo.png)



7. 键入 **w** 以写入磁盘的设置。


	![Write the disk changes](./media/howto-attach-disk-window-linux/DiskWrite.png)

8. 你必须在新分区上创建文件系统。例如，键入以下命令以创建文件系统中，然后输入帐户密码：

	`sudo mkfs -t ext4 /dev/sdc1`

	![Create file system](./media/howto-attach-disk-window-linux/DiskFileSystem.png)

	>[WACOM.NOTE] 请注意在 SUSE Linux Enterprise 11 系统上提供对于 ext4 文件系统的只读访问权限。这些系统的建议新的文件系统的格式设置为 ext3 而非 ext4。


9. 接下来，您必须具有一个目录可用来装载新文件系统。例如，键入以下命令以生成驱动器上，安装新目录，然后输入帐户密码：

	`sudo mkdir /datadrive`


10. 键入以下命令以安装驱动器：

	`sudo mount /dev/sdc1 /datadrive`

	数据磁盘现在就可以用作**/datadrive**。


11. 将新驱动器添加到 /etc/fstab 中：

	若要确保此驱动器处于重新装载自动重新启动后它必须添加到 /etc/fstab 文件。此外，强烈建议的 UUID （通用唯一标识符） 用于在 /etc/fstab 中引用该驱动器，而不只是设备名称 （即 /dev/sdc1)。若要查找的新的驱动器可以使用 UUID **blkid**实用程序：
	
		`sudo -i blkid`

	The output will look similar to the following:

		`/dev/sda1: UUID="11111111-1b1b-1c1c-1d1d-1e1e1e1e1e1e" TYPE="ext4"`
		`/dev/sdb1: UUID="22222222-2b2b-2c2c-2d2d-2e2e2e2e2e2e" TYPE="ext4"`
		`/dev/sdc1: UUID="33333333-3b3b-3c3c-3d3d-3e3e3e3e3e3e" TYPE="ext4"`

	>[WACOM.NOTE] blkid 可能不需要在所有情况下的 sudo 访问权限，但是，则可能是更轻松地使用运行 sudo-i 在某些分发，如果 /sbin 或 /usr/sbin 不在您 $PATH 上。

	**注意：**错误地编辑 /etc/fstab 文件可能会导致系统无法启动。如果无法确定，请参阅有关如何正确编辑该文件的信息的分发的文档。此外建议 /etc/fstab 文件的备份在编辑之前创建。

	使用文本编辑器中，输入 /etc/fstab 文件的结尾处的新文件系统有关的信息。在此示例中我们将使用新的 UUID 值**/dev/sdc1**在前面步骤中，并使用装载点中创建的设备**/datadrive**：

		`UUID=33333333-3b3b-3c3c-3d3d-3e3e3e3e3e3e   /datadrive   ext4   defaults   1   2`

	或者，在基于 SUSE Linux 上的系统上，您可能需要使用稍有不同的格式：

		`/dev/disk/by-uuid/33333333-3b3b-3c3c-3d3d-3e3e3e3e3e3e   /   ext3   defaults   1   2`

	如果创建其他数据驱动器或分区将需要其输入到单独以及 fstab/等 /。

	现在可以测试已装入文件系统正确通过简单地卸载并重新装载文件系统，即使用该示例装入点 / datadrive' 在前面的步骤中创建： 

		`sudo umount /datadrive`
		`sudo mount /datadrive`

	如果第二个命令产生错误，请检查 /etc/fstab 文件的语法正确。


	>[WACOM.NOTE] 随后在不编辑 fstab 的情况下删除数据磁盘可能会导致 VM 无法引导。如果这是一种常见情况，大多数分发都将提供任一 nofail 和/或 nobootwait fstab 选项，将允许系统在启动即使该磁盘不存在。请有关这些参数的详细信息，参阅你的分发的文档。



[登录]: /zh-cn/documentation/articles/virtual-machines-log-on-windows-server/
[logonlinux]: /zh-cn/documentation/articles/virtual-machines-linux-how-to-log-on/


