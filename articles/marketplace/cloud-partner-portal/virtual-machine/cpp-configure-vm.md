---
title: 为 Azure 市场配置 Microsoft Azure 托管的 VM | Microsoft Docs
description: 介绍如何调整、更新和通用化 Azure 中托管的 VM。
services: Azure, Marketplace, Cloud Partner Portal,
documentationcenter: ''
author: v-miclar
manager: Patrick.Butler
editor: ''
ms.assetid: ''
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: conceptual
ms.date: 10/19/2018
ms.author: pbutlerm
ms.openlocfilehash: 9cf363bc5f4230306c2fec99eb6287b23e598a4c
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2019
ms.locfileid: "60744340"
---
# <a name="configure-the-azure-hosted-vm"></a>配置 Azure 托管的 VM

本文介绍如何调整、更新和通用化 Azure 中托管的虚拟机 (VM)。  必须执行这些步骤才能准备好要从 Azure 市场部署的 VM。


## <a name="sizing-the-vhds"></a>调整 VHD 大小

<!--TD: Check if the following assertion is true. I didn't understand the original content. -->
如果选择了某一操作系统 （和可选的其他服务），使用预配置的 Vm，则已选取的标准 Azure VM 大小，如中所述[虚拟机 Sku 选项卡](./cpp-skus-tab.md)。使用预先配置的 OS 启动解决方案是建议的方法。  但是，若要手动安装 OS，则必须在 VM 映像中调整主要 VHD 的大小：

- 对于 Windows，操作系统 VHD 应创建为 127-128 GB 的固定格式 VHD。 
- 对于 Linux，此 VHD 应创建为 30-50 GB 的固定格式 VHD。

如果物理大小小于 127-128GB，则 VHD 应是稀疏的磁盘。 提供的基本 Windows 和 SQL Server 映像已满足这些要求；请不要更改获取的 VHD 的格式或大小。 

数据磁盘可以大至 1TB。 决定其大小时，请记住，客户无法在部署时对映像中的 VHD 调整大小。 数据磁盘 VHD 应创建为固定格式 VHD。 它们还应是稀疏的。 数据磁盘最初可以是空的或包含数据。


## <a name="install-the-most-current-updates"></a>安装最新的更新

操作系统 VM 的基础映像包含截至发布日期为止的最新更新。 在发布创建的操作系统 VHD 之前，请务必使用所有最新的安全和维护修补程序来更新 OS 和所有已安装的服务。

对于 Windows Server 2016，请运行“检查更新”命令。  对于旧版 Windows，请参阅[如何通过 Windows 更新获取更新](https://support.microsoft.com/help/3067639/how-to-get-an-update-through-windows-update)。  Windows 更新将自动安装最新的关键/重要安全更新。

对于 Linux 分发版，通常可以通过命令行工具或图形实用工具来下载和安装更新。  例如，Ubuntu Linux 提供 [apt-get](https://manpages.ubuntu.com/manpages/cosmic/man8/apt-get.8.html) 命令和[更新管理器](https://manpages.ubuntu.com/manpages/cosmic/man8/update-manager.8.html)工具用于更新 OS。


## <a name="perform-additional-security-checks"></a>执行附加的安全检查

应该为 Azure 市场中的解决方案映像保持高级别的安全性。  以下文章提供了有助于实现此目标的安全配置和过程的清单：[适用于 Azure 市场映像的安全建议](https://docs.microsoft.com/azure/security/security-recommendations-azure-marketplace-images)。  其中的某些建议特定于基于 Linux 的映像，但大部分建议适用于任何 VM 映像。 


## <a name="perform-custom-configuration-and-scheduled-tasks"></a>执行自定义配置和计划任务

如果需要其他配置，建议的方法是使用在启动时运行的计划任务，以便在部署 VM 之后对它进行任何最终更改。  另请考虑以下建议：
- 对于一次性运行的任务，建议在成功完成该任务后，使它删除自身。
- 配置不应依赖于除驱动器 C 或 D 以外的驱动器，因为这两个驱动器是仅有的始终保证存在的驱动器。 驱动器 C 是操作系统磁盘，而驱动器 D 是临时的本地磁盘。

有关 Linux 自定义项的详细信息，请参阅[适用于 Linux 的虚拟机扩展和功能](https://docs.microsoft.com/azure/virtual-machines/extensions/features-linux)。


## <a name="generalize-the-image"></a>通用化映像

Azure 市场中的所有映像必须可采用一般形式重复使用。 若要实现这种可重用性，必须将操作系统 VHD 通用化，此操作会从 VM 中删除所有特定于实例的标识符以及软件驱动程序。

### <a name="windows"></a>Windows

Windows OS 磁盘已使用 [sysprep 工具](https://docs.microsoft.com/windows-hardware/manufacture/desktop/sysprep--system-preparation--overview)通用化。 如果后来更新或重新配置了 OS，则必须重新运行 sysprep。 

> [!WARNING]
>  由于运行 sysprep 后更新可能自动运行，因此，在部署 VM 之前应将其关闭。  此关闭操作可避免后续更新对 VHD OS 或安装的服务做出特定于实例的更改。

有关运行 sysprep 的详细信息，请参阅[通用化 VHD 的步骤](https://docs.microsoft.com/azure/virtual-machines/windows/prepare-for-upload-vhd-image#steps-to-generalize-a-vhd)

### <a name="linux"></a>Linux

以下两步过程将通用化 Linux VM，并将其重新部署为单独的 VM。  有关详细信息，请参阅[如何创建虚拟机或 VHD 的映像](../../../virtual-machines/linux/capture-image.md)。 

#### <a name="remove-the-azure-linux-agent"></a>删除 Azure Linux 代理
1.  使用 SSH 客户端连接到 Linux VM。
2.  在 SSH 窗口中，键入以下命令： <br/>
    `sudo waagent -deprovision+user`
3.  键入 `y` 以继续。 （在上述命令中添加 `-force` 参数可以消除此确认步骤。）
4.  该命令完成后，键入 `exit` 关闭 SSH 客户端。

<!-- TD: I need to add meat and/or references to the following steps -->
#### <a name="capture-the-image"></a>捕获映像
1.  转到 Azure 门户，选择资源组 (RG) 并取消分配 VM。
2.  VHD 现已通用化，你可以使用此 VHD 创建新的 VM。


## <a name="create-one-or-more-copies"></a>创建一个或多个副本

创建 VM 的副本通常有利于备份、测试、自定义故障转移或负载均衡、提供不同的解决方案配置，等等。 有关如何复制和下载主要 VHD 以创建非托管克隆的信息，请参阅：

- Linux VM：[从 Azure 下载 Linux VHD](../../../virtual-machines/linux/download-vhd.md)
- Windows VM：[从 Azure 下载 Windows VHD](../../../virtual-machines/windows/download-vhd.md)


## <a name="next-steps"></a>后续步骤

配置 VM 后，即可[从虚拟硬盘部署虚拟机](./cpp-deploy-vm-vhd.md)。
