<properties
   pageTitle="在 Azure SUSE Linux VM 上测试 SAP NetWeaver | Azure"
   description="在 Azure SUSE Linux VM 上测试 SAP NetWeaver"
   services="virtual-machines,virtual-network,storage"
   documentationCenter="saponazure"
   authors="hermanndms"
   manager="juergent"
   editor=""
   tags="azure-resource-manager"
   keywords=""/>
<tags
	ms.service="virtual-machines"
	ms.date="02/12/2016"
	wacn.date=""/>

# 在 Azure SUSE Linux VM 上测试 SAP NetWeaver


下面是你在 Azure SUSE Linux VM 上测试 SAP NetWeaver 时要考虑的事项列表。
目前，SAP-Linux-Azure 没有正式的 SAP 支持声明。
不过，只要不依赖于官方 SAP 支持，客户可以执行一些测试、演示或原型制作操作。

以下信息应有助于避免一些潜在的陷阱。



## Azure 上用于测试 SAP 的 SUSE 映像

对于 Azure 上的 SAP 测试，仅使用 SUSE Linux Enterprise Server (SLES) 11 SP4 和 SLES 12。专门的 SUSE 映像（“SLES 11 SP3 for SAP CAL”）位于 Azure 库中，但这并非适用于常规用途。它保留给 [SAP 云设备库](https://cal.sap.com/)解决方案。没有任何选项可对公众隐藏此映像。因此，只要不要使用它。

应使用 Azure Resource Manager 完成 Azure 上的所有新测试。若要通过 Azure PowerShell 或 Azure 命令行界面 (CLI) 查找 SUSE SLES 映像和版本，请使用以下命令。然后可以使用输出，例如，在 JSON 模板中定义 OS 映像以部署新的 SUSE Linux VM。以下 PowerShell 命令适用于 Azure PowerShell 1.0.1 版或更高版本。

* 查找现有发布服务器（包括 SUSE）：

	
	   PS : Get-AzureRmVMImagePublisher -Location "West Europe" | where-object { $\_.publishername -like "*US*" }
	   CLI : azure vm image list-publishers westeurope | grep "US"
	   

* 从 SUSE 中查找现有产品/服务：

	
	   PS : Get-AzureRmVMImageOffer -Location "West Europe" -Publisher "SUSE"
	   CLI : azure vm image list-offers westeurope SUSE
	   

* 查找 SUSE SLES 产品/服务：

	
	   PS : Get-AzureRmVMImageSku -Location "West Europe" -Publisher "SUSE" -Offer "SLES"
	   CLI : azure vm image list-skus westeurope SUSE SLES
	   

* 查找特定版本的 SLES SKU：

	
	   PS : Get-AzureRmVMImage -Location "West Europe" -Publisher "SUSE" -Offer "SLES" -skus "12"
	   CLI : azure vm image list westeurope SUSE SLES 12
	   

## 在 SUSE VM 中安装 WALinuxAgent

名为 WALinuxAgent 的代理是 Azure 库中 SLES 映像的一部分。可以在以下位置查找有关如何手动安装该代理的信息（例如，从本地上载 SLES OS VHD 时）：

- [OpenSUSE](http://software.opensuse.org/package/WALinuxAgent)

- [Azure](virtual-machines-linux-endorsed-distributions.md)

- [SUSE](https://www.suse.com/communities/blog/suse-linux-enterprise-server-configuration-for-windows-azure/)

## 将 Azure 数据磁盘附加到 Azure Linux VM

永远不要通过设备 ID 将 Azure 数据磁盘装载到 Azure Linux VM，请改用 UUID。例如，使用图形化工具装载 Azure 数据磁盘时要小心。请仔细检查 /etc/fstab 中的条目。

使用设备 ID 的问题是，它可能会更改，然后 Azure VM 可能会在启动过程中挂起。可以在 /etc/fstab 中添加 nofail 参数来缓解此问题。但请注意，使用 nofail 时，在外部 Azure 数据磁盘未在启动期间装载的情况下，应用程序可能会像以前一样使用装入点并且可能会写入根文件系统。

有关通过 UUID 装载的唯一例外与附加 OS 磁盘用于故障排除目的相关，如以下部分所述。

## 对不再可访问的 SUSE VM 进行故障排除

可能会出现这样的情况：Azure 中的 SUSE VM 在启动过程中挂起（例如，出现与装载磁盘相关的错误）。此问题可以（例如）通过门户中 v2 VM 的启动诊断功能进行验证（[请参阅此博客](https://azure.microsoft.com/blog/boot-diagnostics-for-virtual-machines-v2/)）。

解决此问题的一个选项是将已损坏 VM 中的 OS 磁盘附加到 Azure 上的另一个 SUSE VM 中，然后进行相应更改，例如编辑 /etc/fstab 或删除网络 udev 规则，如下一部分所述。

但是，需要考虑一个重要的事项。从同一 Azure 库映像（例如 SLES 11 SP4）部署多个 SUSE VM 显示：将始终通过同一个 UUID 装载 OS 磁盘。因此，通过 UUID 从使用同一 Azure 库映像部署的不同 VM 附加 OS 磁盘将生成两个相同的 UUID。这会导致问题，并且可能意味着用于故障排除的 VM 实际上将从附加的已损坏 OS 磁盘（而不是原始磁盘）启动。

有两种可能可以避免出现这种情况：

* 将不同 Azure 库映像用于要进行故障排除的 VM（例如，使用 SLES 12 而不是 SLES 11 SP4）
* 不要通过 UUID（而是使用其他内容）从另一个 VM 附加损坏的 OS 磁盘

## 从本地将 SUSE VM 上载到 Azure

[本文](virtual-machines-linux-create-upload-vhd-suse.md)说明将 SUSE VM 从本地上载到 Azure 的步骤。

如果要使用最终没有取消预配步骤的方法上载 VM 以保留（例如）现有 SAP 安装以及主机名，需要检查以下项：

* 确保通过 UUID（而不是设备 ID）装载 OS 磁盘。只是在 /etc/fstab 中更改为 UUID 对于 OS 磁盘来说是不够的。此外，不要忘记通过 YaST 或通过编辑 /boot/grub/menu.lst 调整启动加载程序。
* 如果对 SUSE OS 磁盘使用了 VHDX 格式，并将其转换为 VHD 以上载到 Azure，则很有可能网络设备会从 eth0 更改为 eth1。若要避免稍后在 Azure 上启动时出现问题，应将其更改回 eth0，如[此文](https://dartron.wordpress.com/2013/09/27/fixing-eth1-in-cloned-sles-11-vmware/)所述。

除了此文中所述的内容以外，建议也删除以下项：

   /lib/udev/rules.d/75-persistent-net-generator.rules

安装 Azure Linux 代理 (waagent) 还应帮助你避免在没有多个 NIC 时可能出现的问题。

## 在 Azure 上部署 SUSE VM

在新的 Azure Resource Manager 模型中，应通过 JSON 模板文件创建新的 SUSE VM。创建 JSON 模板文件后，便可以使用以下 CLI 命令作为 PowerShell 的替代方法部署 VM 了：

	
	   azure group deployment create "<deployment name>" -g "<resource group name>" --template-file "<../../filename.json>"
	
	   
你可以在[此文](resource-group-authoring-templates.md)和[此网页](https://azure.microsoft.com/documentation/templates/)中找到有关 JSON 模板文件的更多详细信息。

可以在[此文](/documentation/articles/xplat-cli-azure-resource-manager)中找到有关 CLI 和 Azure Resource Manager 的更多详细信息。

## SAP 许可证和硬件密钥

对于官方 SAP-Microsoft-Azure 认证，引入了一种新机制来计算用于 SAP 许可证的 SAP 硬件密钥。
SAP 内核必须进行修改才能利用此机制。
适用于 Linux 的当前 SAP 内核版本不包括此代码更改。因此可能会发生这样的情况，即在某些情况下（例如 Azure VM 调整大小）SAP 硬件密钥发生更改，SAP 许可证将不再有效。

## SUSE sapconf 包

SUSE 提供了一个名为“sapconf”的包，该包处理一组特定于 SAP 的设置。可以在[此 SUSE 博客文章](https://www.suse.com/communities/blog/using-sapconf-to-prepare-suse-linux-enterprise-server-to-run-sap-systems/)和[此 SAP 博客文章](http://scn.sap.com/community/linux/blog/2014/03/31/what-is-sapconf-or-how-to-prepare-a-suse-linux-enterprise-server-for-running-sap-systems)中找到有关此包的更多详细信息（它的功能以及如何安装和使用它）。

## 分布式 SAP 安装中的 NFS 共享

在要将（例如）数据库和 SAP 应用程序服务器安装在单独的 VM 中的分布式安装情况下，可能需要通过网络文件系统 (NFS) 共享 /sapmnt 目录。如果在为 /sapmnt 创建 NFS 共享后，安装步骤会出现问题，请检查是否为该共享设置了“no\_root\_squash”。这是内部测试用例中的解决方案。


## 逻辑卷

逻辑卷管理器 (LVM) 未在 Azure 上完全验证。如果需要跨多个 Azure 数据磁盘的大逻辑卷（例如，用于 SAP 数据库），应使用 mdadm。[此文](/documentation/articles/virtual-machines-linux-configure-raid)介绍如何在 Azure 中使用 mdadm 设置 Linux RAID。


## Azure SUSE 存储库

如果访问标准 Azure SUSE 存储库时遇到问题，有一个简单的命令可重置它。在以下情况下会发生这种情况：你在 Azure 区域中创建一个专用 OS 映像，然后将该映像复制到其他区域，你要在该区域中基于此专用 OS 映像部署新 VM。只需在 VM 中运行以下命令：

	
	   service guestregister restart
	   

## Gnome 桌面

如果要使用 Gnome 桌面在单个 VM 中安装完整的 SAP 演示系统（包括 SAP GUI、浏览器、SAP 管理控制台，等等），下面是在 Azure SLES 映像上安装该系统的一个小提示：

   SLES 11

	
	   zypper in -t pattern gnome
	   

   SLES 12

	
	   zypper in -t pattern gnome-basic
	   

## 云中 Linux 上的 SAP-Oracle 支持

在虚拟化环境中，对于Linux 上的 Oracle 有支持限制。这是一个常规主题，并非特定于 Azure。不过，请务必要了解。
SAP 不支持 Azure 等公有云中的 SUSE 或 RedHat 上的 Oracle。
客户应直接联系 Oracle 讨论此主题。

<!---HONumber=Mooncake_0321_2016-->