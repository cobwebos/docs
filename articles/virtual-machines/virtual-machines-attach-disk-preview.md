<!-- not suitable for Mooncake -->

<properties
	pageTitle="附加数据磁盘 | Azure"
	description="如何使用资源管理器部署模型在 Azure 门户中将新磁盘或现有数据磁盘附加到 VM。"
	services="virtual-machines"
	documentationCenter=""
	authors="cynthn"
	manager="timlt"
	editor=""
	tags="azure-resource-manager"/>

<tags
	ms.service="virtual-machines"
	ms.date="01/21/2016"
	wacn.date=""/>

# 如何在 Azure 门户中附加数据磁盘

[AZURE.INCLUDE [了解部署模型](../includes/learn-about-deployment-models-rm-include.md)] [classic deployment model](/documentation/articles/storage-windows-attach-disk)。

本文向你介绍如何通过 Azure 门户将新磁盘和现有磁盘附加到虚拟机。在开始之前，请查看以下提示：

- 虚拟机的大小决定了可以附加多少个磁盘。有关详细信息，请参阅[虚拟机大小](/documentation/articles/virtual-machines-size-specs)。
- 要使用高级存储，需要使用 DS 系列或 GS 序列虚拟机。可以从高级存储帐户和标准存储帐户通过这些虚拟机使用磁盘。高级存储只在某些区域可用。有关详细信息，请参阅[高级存储：适用于 Azure 虚拟机工作负荷的高性能存储](/documentation/articles/storage-premium-storage)。
- 附加到虚拟机的磁盘实际上是 Azure 存储帐户中的 .vhd 文件。有关详细信息，请参阅[关于虚拟机的磁盘和 VHD](/documentation/articles/virtual-machines-disks-vhds)。
- 对于新磁盘，你不必首先创建它，因为 Azure 将在附加磁盘时创建该磁盘。
- 对于现有磁盘，Azure 存储帐户中必须要有可用的 .vhd 文件。你可以使用已经存在的 .vhd（如果该磁盘没有附加到另一虚拟机），或者将你自己的 .vhd 文件上载到存储帐户。

## 查找虚拟机

1. 登录到 Azure 门户。

2. 在“中心”菜单中，单击“虚拟机”。

3.	从列表中选择虚拟机。

4. 在右侧，在“Essentials”的下方，单击“所有设置”，然后单击“磁盘”。

	![打开磁盘设置](./media/virtual-machines-attach-disk-preview/find-disk-settings.png)

按照附加新磁盘或现有磁盘的说明继续操作。

## 选项 1：附加新磁盘

1.	在“磁盘”边栏选项卡上，单击“附加新磁盘”。

2.	检查默认设置，根据需要更新，然后单击“确定”。

 	![检查磁盘设置](./media/virtual-machines-attach-disk-preview/attach-new.png)

3.	在 Azure 创建磁盘并将磁盘附加到虚拟机之后，新磁盘将出现在“数据磁盘”下的虚拟机磁盘设置中。

## 选项 2：附加现有磁盘

1.	在“磁盘”边栏选项卡上，单击“附加现有磁盘”。

2.	在“附加现有磁盘”下，单击“VHD 文件”。

	![附加现有磁盘](./media/virtual-machines-attach-disk-preview/attach-existing.png)

3.	在“存储帐户”下，选择帐户和容纳 .vhd 文件的容器。

	![查找 VHD 位置](./media/virtual-machines-attach-disk-preview/find-storage-container.png)

4.	选择 .vhd 文件

5.	在“附加现有磁盘”下，刚才选择的文件将出现在“VHD 文件”中。单击“确定”。

6.	在 Azure 将磁盘附加到虚拟机之后，磁盘将出现在“数据磁盘”下的虚拟机磁盘设置中。

## 后续步骤

在添加磁盘之后，需要准备磁盘以便在虚拟机的操作系统中使用：

- 对于 Linux，请参阅[本文](/documentation/articles/virtual-machines-linux-how-to-attach-disk)中的“如何：在 Linux 中初始化新的数据磁盘”。
- 对于 Windows，请参阅[本文](/documentation/articles/storage-windows-attach-disk)中的“如何：在 Windows Server 中初始化新的数据磁盘”。

## 其他资源

[关于 Azure 存储帐户]

<!--Link references-->

[关于 Azure 存储帐户]: /documentation/articles/storage-whatis-account

<!---HONumber=Mooncake_0411_2016-->