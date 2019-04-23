---
title: 为 Azure 市场创建 Azure 兼容的 VHD | Microsoft Docs
description: 介绍如何为 Azure 市场中的虚拟机套餐创建 VHD。
services: Azure, Marketplace, Cloud Partner Portal,
documentationcenter: ''
author: pbutlerm
manager: Patrick.Butler
editor: ''
ms.assetid: ''
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: article
ms.date: 08/27/2018
ms.author: pbutlerm
ms.openlocfilehash: 04a1741bbe4e60567a22445c5674ec03b232640c
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/18/2019
ms.locfileid: "59793070"
---
# <a name="create-an-azure-compatible-vhd"></a>创建与 Azure 兼容的 VHD

本文详细介绍为 Azure 市场中的虚拟机 (VM) 套餐创建虚拟硬盘 (VHD) 所要执行的步骤。  此外，还包含各项操作的最佳做法，例如，使用远程桌面协议 (RDP)、选择 VM 大小、安装最新的 Windows 更新，以及通用化 VHD 映像。  以下部分侧重于基于 Windows 的 VHD；有关创建基于 Linux 的 VHD 的详细信息，请参阅 [Azure 认可的 Linux 分发版](../../../virtual-machines/linux/endorsed-distros.md)。 

> [!WARNING]
> 强烈建议遵照本主题中的指导，使用 Azure 创建包含预配置的认可操作系统的 VM。  如果这不符合你的解决方案，然后就可以创建和配置使用已批准的操作系统的本地 VM。  然后可以根据[准备好要上传到 Azure 的 Windows VHD 或 VHDX](https://docs.microsoft.com/azure/virtual-machines/windows/prepare-for-upload-vhd-image) 中所述，对此 VM 进行配置并准备好上传。


## <a name="select-an-approved-base"></a>选择批准的基础映像
用于 VM 映像的操作系统 VHD 必须基于 Azure 批准的基本映像（包含 Windows Server 或 SQL Server）。
若要开始，请基于 Microsoft Azure 门户中的以下映像创建 VM：

-   Windows Server（[2016](https://www.microsoft.com/evalcenter/evaluate-windows-server-2016)、[2012 R2 Datacenter](https://azure.microsoft.com/marketplace/partners/microsoft/windowsserver2012r2datacenter/)、[2012 Datacenter](https://azure.microsoft.com/marketplace/partners/microsoft/windowsserver2012datacenter/)、[2008 R2 SP1](https://azure.microsoft.com/marketplace/partners/microsoft/windowsserver2008r2sp1/)）
-   [SQL Server 2014](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-sql-server-pricing-guidance)（Enterprise、Standard、Web）
-   [SQL Server 2012 SP2](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-sql-server-pricing-guidance)（Enterprise、Standard、Web）

> [!TIP]
> 如果使用最新 Azure 门户或 PowerShell，则在 2014 年 9 月 8 日及以后发布的 Windows Server 映像已得到批准。

Azure 还提供一系列已批准的 Linux 分发版。  有关最新列表，请参阅 [Azure 认可的 Linux 分发版](https://docs.microsoft.com/azure/virtual-machines/linux/endorsed-distros)。


## <a name="create-vm-in-the-azure-portal"></a>在 Azure 门户中创建 VM 

在 Microsoft [Azure 门户](https://ms.portal.azure.com/)中，使用以下步骤创建基础映像。

1. 使用要在其中发布 VM 套餐的 Azure 订阅的 Microsoft 帐户登录到门户。
2. 创建新资源组，并提供**资源组名称**、**订阅**和**资源组位置**。  有关更多指导，请参阅[管理资源组](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-portal)。
3. 在左侧菜单栏中单击“虚拟机”，显示“虚拟机详细信息”页。 
4. 在此新页中，单击“+添加”显示“计算”边栏选项卡。  如果初始屏幕上未显示 VM 类型，可以搜索基础 VM 的名称，例如：

    ![新 VM 的“计算”边栏选项卡](./media/publishvm_014.png)

5. 选择适当的虚拟映像后，提供以下值：
   * 在“基本信息”边栏选项卡中，输入虚拟机的**名称**（1-15 个字母数字字符）。 （本示例使用 `DemoVm009`。）
   * 输入**用户名**和强**密码**，用于在 VM 上创建本地帐户。  （此处使用了 `adminUser`。）密码长度必须为 8 到 123 个字符，并且必须满足以下 4 个复杂性要求的其中 3 个：1 个小写字符、1 个大写字符、1 个数字和 1 个特殊字符。 有关详细信息，请参阅[用户名和密码要求](https://docs.microsoft.com/azure/virtual-machines/virtual-machines-windows-faq#what-are-the-username-requirements-when-creating-a-vm)。
   * 选择创建的资源组（此处为 `DemoResourceGroup`）。
   * 选择 Azure 数据中心的**位置**（此处为 `West US`）。
   * 单击“确定”以保存这些值。 

6. 根据以下建议选择要部署的 VM 的大小：
   * 如果计划在本地开发 VHD，大小则无关紧要。 请考虑使用一个较小的 VM。
   * 如果计划在 Azure 中开发映像，请考虑对所选映像使用一个建议的 VM 大小。
   * 有关定价信息，请参阅门户上显示的“建议定价层”选择器。 其中会显示发布者提供的三个建议大小。 （此处的发布者为 Microsoft。）

   ![新 VM 的“大小”边栏选项卡](./media/publishvm_015.png)

7. 在“设置”边栏选项卡中，将“使用托管磁盘”选项设置为“否”。  这样，便可以手动管理新 VHD。 （在“设置”边栏选项卡中，还可以更改其他存储和网络选项，例如，在“磁盘类型”中选择“高级(SSD)”。）单击“确定”以继续。

    ![新 VM 的“设置”边栏选项卡](./media/publishvm_016.png)

8. 单击“摘要”查看所做的选择。 看到“验证通过”消息后，单击“确定”。

    ![新 VM 的“摘要”边栏选项卡](./media/publishvm_017.png)

Azure 随即开始预配所指定的虚拟机。  可以单击左侧的“虚拟机”选项卡来跟踪预配进度。  创建虚拟机后，状态将更改为“正在运行”。  此时，可以[连接到该虚拟机](./cpp-connect-vm.md)。


## <a name="next-steps"></a>后续步骤

如果在创建基于 Azure 的新 VHD 时遇到问题，请参阅[在创建 VHD 期间出现的常见问题](./cpp-common-vhd-creation-issues.md)。  否则，接下来必须[连接到在 Azure 上创建的 VM](./cpp-connect-vm.md)。 
