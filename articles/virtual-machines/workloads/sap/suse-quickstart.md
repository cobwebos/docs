---
title: "测试 Microsoft Azure SUSE Linux VM 上的 SAP NetWeaver | Microsoft Docs"
description: "测试 Microsoft Azure SUSE Linux VM 上的 SAP NetWeaver"
services: virtual-machines-linux
documentationcenter: 
author: hermanndms
manager: timlt
editor: 
tags: azure-resource-manager
keywords: 
ms.assetid: 645e358b-3ca1-4d3d-bf70-b0f287498d7a
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 02/14/2017
ms.author: hermannd
translationtype: Human Translation
ms.sourcegitcommit: eeb56316b337c90cc83455be11917674eba898a3
ms.openlocfilehash: 118b56376eace80788a20625497849181ad2e253
ms.lasthandoff: 04/03/2017


---
# <a name="running-sap-netweaver-on-microsoft-azure-suse-linux-vms"></a>运行 Microsoft Azure SUSE Linux VM 上的 SAP NetWeaver
此文介绍在 Microsoft Azure SUSE Linux 虚拟机 (VM) 上运行 SAP NetWeaver 时应注意的各个事项。 自 2016 年 5 月 19 日起，Azure 上的 SUSE Linux VM 已正式支持 SAP NetWeaver。 有关 Linux 版本、SAP 内核版本等等的所有详细信息，请参阅 SAP 说明 1928533“Azure 上的 SAP 应用程序：支持的产品和 Azure VM 类型”。
有关 Linux VM 上 SAP 的更多文档可在此处找到：[在 Linux 虚拟机 (VM) 上使用 SAP](get-started.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)。

以下信息应有助于避免一些潜在的陷阱。

## <a name="suse-images-on-azure-for-running-sap"></a>Azure 上用于运行 SAP 的 SUSE 映像
若要在 Azure 上运行 SAP NetWeaver，只能使用 SUSE Linux Enterprise Server SLES 12 (SPx) — 另请参阅 SAP 说明 1928533。 专门的 SUSE 映像（“SLES 11 SP3 for SAP CAL”）位于 Azure 应用商店中，但这并非适用于常规用途。 请不要使用此映像，因为它是为 [SAP 云设备库](https://cal.sap.com/)解决方案保留的。  

应使用 Azure Resource Manager 完成 Azure 上的所有新测试和安装。 若要通过 Azure PowerShell 或 Azure 命令行界面 (CLI) 查找 SUSE SLES 映像和版本，请使用以下命令。 然后可以使用输出，例如，在 JSON 模板中定义 OS 映像以部署新的 SUSE Linux VM。
这些 PowerShell 命令适用于 Azure PowerShell 1.0.1 和更高版本。

尽管仍可使用标准 SLES 映像进行 SAP 安装，但建议使用新的 SLES for SAP 映像（Azure 映像库现已提供）。 有关这些映像的详细信息，可参阅相应的 [Azure 应用商店页面]( https://azuremarketplace.microsoft.com/en-us/marketplace/apps/SUSE.SLES-SAP )或[关于 SLES for SAP 的 SUSE FAQ 网页]( https://www.suse.com/products/sles-for-sap/frequently-asked-questions/ )。


* 查找现有发布服务器（包括 SUSE）：
  
   ```
   PS  : Get-AzureRmVMImagePublisher -Location "West Europe"  | where-object { $_.publishername -like "*US*"  }
   CLI : azure vm image list-publishers westeurope | grep "US"
   ```
* 从 SUSE 中查找现有产品/服务：
  
   ```
   PS  : Get-AzureRmVMImageOffer -Location "West Europe" -Publisher "SUSE"
   CLI : azure vm image list-offers westeurope SUSE
   ```
* 查找 SUSE SLES 产品/服务：
  
   ```
   PS  : Get-AzureRmVMImageSku -Location "West Europe" -Publisher "SUSE" -Offer "SLES"
   PS  : Get-AzureRmVMImageSku -Location "West Europe" -Publisher "SUSE" -Offer "SLES-SAP"
   CLI : azure vm image list-skus westeurope SUSE SLES
   CLI : azure vm image list-skus westeurope SUSE SLES-SAP
   ```
* 查找特定版本的 SLES SKU：
  
   ```
   PS  : Get-AzureRmVMImage -Location "West Europe" -Publisher "SUSE" -Offer "SLES" -skus "12-SP2"
   PS  : Get-AzureRmVMImage -Location "West Europe" -Publisher "SUSE" -Offer "SLES-SAP" -skus "12-SP2"
   CLI : azure vm image list westeurope SUSE SLES 12-SP2
   CLI : azure vm image list westeurope SUSE SLES-SAP 12-SP2
   ```

## <a name="installing-walinuxagent-in-a-suse-vm"></a>在 SUSE VM 中安装 WALinuxAgent
名为 WALinuxAgent 的代理是 Azure 应用商店中 SLES 映像的一部分。 有关如何手动安装该代理的信息（例如，从本地上载 SLES OS 虚拟硬盘 (VHD) 时），请参阅：

* [OpenSUSE](http://software.opensuse.org/package/WALinuxAgent)
* [Azure](../../linux/endorsed-distros.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [SUSE](https://www.suse.com/communities/blog/suse-linux-enterprise-server-configuration-for-windows-azure/)

## <a name="sap-enhanced-monitoring"></a>SAP“增强型监视”
SAP“增强型监视”是在 Azure 上运行 SAP 的必要先决条件。 请查看 SAP 说明 2191498“Azure 上的 Linux 版 SAP：增强型监视”中的详细信息。

## <a name="attaching-azure-data-disks-to-an-azure-linux-vm"></a>将 Azure 数据磁盘附加到 Azure Linux VM
永远不要使用设备 ID 将 Azure 数据磁盘装载到 Azure Linux VM， 而应该使用全局唯一标识符 (UUID)。 例如，使用图形化工具装载 Azure 数据磁盘时应小心。 请仔细检查 /etc/fstab 中的条目。

使用设备 ID 的问题是，它可能会更改，然后 Azure VM 可能会在启动过程中挂起。 为了缓解问题，可以在 /etc/fstab 中添加 nofail 参数。 但使用 nofail 时请小心，因为在外部 Azure 数据磁盘未在启动期间装载的情况下，应用程序可能会像以前一样使用装入点并且可能会写入根文件系统。

有关通过 UUID 装载的唯一例外与附加 OS 磁盘用于故障排除目的相关，如以下部分所述。

## <a name="troubleshooting-a-suse-vm-that-isnt-accessible-anymore"></a>对不再可访问的 SUSE VM 进行故障排除
可能会出现这样的情况：Azure 中的 SUSE VM 在启动过程中挂起（例如，出现与装载磁盘相关的错误）。 可以使用 Azure 门户中的 Azure 虚拟机 v2 引导诊断功能来检查问题。 有关详细信息，请参阅[引导诊断](https://azure.microsoft.com/blog/boot-diagnostics-for-virtual-machines-v2/)。

解决此问题的方法之一是从受损的 VM 将 OS 磁盘附加到 Azure 上的另一个 SUSE VM。 然后进行适当的更改，例如，编辑 /etc/fstab 或删除网络 udev 规则，如下一部分中所述。

需要考虑一个重要事项。 从同一 Azure 应用商店映像（例如 SLES 11 SP4）部署多个 SUSE VM 会导致始终通过同一个 UUID 装载 OS 磁盘。 因此，通过 UUID 从使用同一 Azure 应用商店映像部署的不同 VM 附加 OS 磁盘将生成两个相同的 UUID。 这会导致问题，并且可能意味着用于故障排除的 VM 实际上将从附加的已损坏 OS 磁盘（而不是原始磁盘）启动。

可通过两种方式来避免此问题：

* 将不同 Azure 应用商店映像用于要进行故障排除的 VM（例如，使用 SLES 11 SPx 而不是 SLES 12）。
* 不要使用 UUID（而是使用其他内容）从另一个 VM 附加损坏的 OS 磁盘

## <a name="uploading-a-suse-vm-from-on-premises-to-azure"></a>从本地将 SUSE VM 上载到 Azure
有关从本地将 SUSE VM 上载到 Azure 的步骤说明，请参阅[为 Azure 准备 SLES 或 openSUSE 虚拟机](../../linux/suse-create-upload-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)。

例如，若要使用最终没有取消预配步骤的方法上载 VM 以保留现有 SAP 安装以及主机名，需要检查以下项：

* 确保使用 UUID（而不是设备 ID）装载 OS 磁盘。 只是在 /etc/fstab 中更改为 UUID 对于 OS 磁盘来说是不够的。 此外，不要忘记通过 YaST 或通过编辑 /boot/grub/menu.lst 调整启动加载程序。
* 如果对 SUSE OS 磁盘使用了 VHDX 格式，并将其转换为 VHD 以上载到 Azure，则很有可能网络设备会从 eth0 更改为 eth1。 若要避免稍后在 Azure 上启动时出现问题，应将其更改回 eth0，如 [Fixing eth0 in cloned SLES 11 VMware](https://dartron.wordpress.com/2013/09/27/fixing-eth1-in-cloned-sles-11-vmware/)（修复克隆 SLES 11 VMware 中的 eth0）中所述。

除了此文中所述的内容以外，建议也删除以下项：

   /lib/udev/rules.d/75-persistent-net-generator.rules

还可以安装 Azure Linux 代理 (waagent) 来帮助避免在没有多个 NIC 时可能出现的问题。

## <a name="deploying-a-suse-vm-on-azure"></a>在 Azure 上部署 SUSE VM
在新的 Azure Resource Manager 模型中，应使用 JSON 模板文件创建新的 SUSE VM。 创建 JSON 模板文件后，便可以使用以下 CLI 命令作为 PowerShell 的替代方法部署 VM 了：

   ```
   azure group deployment create "<deployment name>" -g "<resource group name>" --template-file "<../../filename.json>"

   ```
有关 JSON 模板文件的更多详细信息，请参阅[创作 Azure Resource Manager 模板](../../../resource-group-authoring-templates.md)和 [Azure 快速启动模板](https://azure.microsoft.com/documentation/templates/)。

有关 CLI 和 Azure Resource Manager 的更多详细信息，请参阅 [Use the Azure CLI for Mac, Linux, and Windows with Azure Resource Manager](../../../xplat-cli-azure-resource-manager.md)（将适用于 Mac、Linux 和 Windows 的 Azure CLI 与 Azure Resource Manager 配合使用）。

## <a name="sap-license-and-hardware-key"></a>SAP 许可证和硬件密钥
对于官方 SAP-Azure 认证，引入了一种新机制来计算用于 SAP 许可证的 SAP 硬件密钥。 SAP 内核必须进行修改才能利用此机制。 适用于 Linux 的旧 SAP 内核版本不包括此代码更改。 因此，在某些情况下（例如 Azure VM 重设大小），SAP 硬件密钥会发生变化，SAP 许可证不再有效。 最新的 SAP Linux 内核中已解决此问题。 有关详细信息，请查看 SAP 说明 1928533。

## <a name="suse-sapconf-package--tuned-adm"></a>SUSE sapconf 包/tuned-adm
SUSE 提供了一个名为“sapconf”的包，该包可管理一组特定于 SAP 的设置。 有关此包的作用、安装方式和用法的更多详细信息，请参阅 [Using sapconf to prepare a SUSE Linux Enterprise Server to run SAP systems](https://www.suse.com/communities/blog/using-sapconf-to-prepare-suse-linux-enterprise-server-to-run-sap-systems/)（使用 sapconf 来准备要运行 SAP 系统的 SUSE Linux Enterprise Server）和 [What is sapconf or how to prepare a SUSE Linux Enterprise Server for running SAP systems?](http://scn.sap.com/community/linux/blog/2014/03/31/what-is-sapconf-or-how-to-prepare-a-suse-linux-enterprise-server-for-running-sap-systems)（什么是 sapconf 或如何准备要运行 SAP 系统的 SUSE Linux Enterprise Server？）。

在此期间有一种新工具将替换 sapconf - tuned-adm。 可通过以下两个链接详细了解此工具。

可以在[此处](https://www.suse.com/documentation/sles-for-sap-12/book_s4s/data/sec_s4s_configure_sapconf.html)找到有关 tuned-adm 配置文件 sap-hana 的 SLES 文档 

可以在[此处](https://www.suse.com/documentation/sles-for-sap-12/pdfdoc/book_s4s/book_s4s.pdf)（6.2 章中）找到如何使用 tuned-adm 针对 SAP 工作负荷优化系统的信息

## <a name="nfs-share-in-distributed-sap-installations"></a>分布式 SAP 安装中的 NFS 共享
如果你使用了分布式安装 -- 例如，要将数据库和 SAP 应用程序服务器安装在单独的 VM 中 -- 你可以通过网络文件系统 (NFS) 来共享 /sapmnt 目录。 如果在为 /sapmnt 创建 NFS 共享后，安装步骤会出现问题，请检查是否为该共享设置了“no_root_squash”。

## <a name="logical-volumes"></a>逻辑卷
在过去，如果用户需要一个跨多个 Azure 数据磁盘的大型逻辑卷（例如用于 SAP 数据库），会建议使用 mdadm，因为 lvm 在 Azure 上尚未完全通过验证。 若要了解如何使用 mdadm 在 Azure 上设置 Linux RAID，请参阅[在 Linux 上配置软件 RAID](../../linux/configure-raid.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)。 自 2016 年 5 月起的这段时间，lvm 在 Azure 上也已获得完全支持，并可用作 mdadm 的替代方案。 有关 Azure 上 lvm 的其他信息，请参阅[在 Azure 中的 Linux VM 上配置 LVM](../../linux/configure-lvm.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)。

## <a name="azure-suse-repository"></a>Azure SUSE 存储库
如果访问标准 Azure SUSE 存储库时遇到问题，可以使用一个简单的命令来重置它。 在以下情况下会发生这种情况：你在 Azure 区域中创建一个专用 OS 映像，然后将该映像复制到其他区域，你要在该区域中基于此专用 OS 映像部署新 VM。 只需在 VM 中运行以下命令：

   ```
   service guestregister restart
   ```

## <a name="gnome-desktop"></a>Gnome 桌面
如果要使用 Gnome 桌面在单个 VM 中安装完整的 SAP 演示系统（包括 SAP GUI、浏览器、SAP 管理控制台），请根据以下提示在 Azure SLES 映像上安装该系统：

   对于 SLES 11：

   ```
   zypper in -t pattern gnome
   ```

   对于 SLES 12：

   ```
   zypper in -t pattern gnome-basic
   ```

## <a name="sap-support-for-oracle-on-linux-in-the-cloud"></a>对云中 Linux 上 Oracle 的 SAP 支持
在虚拟化环境中，对于Linux 上的 Oracle 有支持限制。 尽管本主题并非特定于 Azure，但必须了解其中所述的知识。 SAP 不支持 Azure 等公有云中的 SUSE 或 Red Hat 上的 Oracle。 若要讨论此主题，请直接联系 Oracle。


