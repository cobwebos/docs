---
title: 管理 VM 上的 Azure 自动化清单收集 | Microsoft Docs
description: 本文介绍如何管理 VM 上的清单收集。
services: automation
ms.subservice: change-inventory-management
keywords: 清单, 自动化, 更改, 跟踪
ms.date: 01/28/2020
ms.topic: conceptual
ms.openlocfilehash: d237b016b8f3430ed0b28becd2712bd0c41d17b4
ms.sourcegitcommit: 0b80a5802343ea769a91f91a8cdbdf1b67a932d3
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/25/2020
ms.locfileid: "83830610"
---
# <a name="manage-inventory-collection-from-vms"></a>管理 VM 上的清单收集

你可从计算机的资源页为 Azure VM 启用清单跟踪。 可在计算机上收集和查看以下清单信息：

- Windows 更新、Windows 应用程序、服务、文件和注册表项
- Linux 软件包、守护程序和文件

Azure 自动化更改跟踪和清单提供基于浏览器的用户界面，可用于设置和配置清单收集。

## <a name="before-you-begin"></a>开始之前

如果没有 Azure 订阅，可以[创建一个免费帐户](https://azure.microsoft.com/free/)。

本文假定你有一个要启用更改跟踪和清单功能的 VM。 如果你没有 Azure VM，则可以[创建 VM](../virtual-machines/windows/quick-create-portal.md)。

## <a name="sign-in-to-the-azure-portal"></a>登录到 Azure 门户

登录 [Azure 门户](https://portal.azure.com/)。

## <a name="enable-inventory-collection-from-the-vm-resource-page"></a>从 VM 资源页启用清单收集

1. 在 Azure 门户的左窗格中，选择“虚拟机”。
2. 在 VM 列表中选择计算机。
3. 在“操作”下方的“资源”菜单中选择“清单”  。
4. 选择用于存储数据日志的 Log Analytics 工作区。
    如果该区域没有可用工作区，则会提示创建默认工作区和自动化帐户。
5. 若要开始启用计算机，请选择“启用”。

   ![查看载入选项](./media/automation-vm-inventory/inventory-onboarding-options.png)

    状态栏会通知已启用更改跟踪和清单功能。 此过程最多可能需要 15 分钟。 在此期间，可以关闭窗口，也可以让窗口保持打开状态。启用功能后，它会通知你。 可以从通知窗格监视部署状态。

   ![查看清单](./media/automation-vm-inventory/inventory-onboarded.png)

部署完成后，状态栏消失。 系统仍在收集清单数据，这些数据可能还不可见。 完整收集数据可能需要 24 小时。

## <a name="configure-your-inventory-settings"></a>配置清单设置

默认情况下，为收集配置了软件、Windows 服务和 Linux 守护程序。 若要收集 Windows 注册表和文件清单，需配置清单收集设置。

1. 在清单页，单击页面顶部的“编辑设置”。
2. 若要添加新的收集设置，请选择“Windows 注册表”、“Windows 文件”或“Linux 文件”选项卡，转到要添加的设置类别  。
3. 选择相应的类别，然后单击页面顶部的“添加”。

以下部分提供可以为各种类别配置的各个属性的相关信息。

### <a name="windows-registry"></a>Windows 注册表

|properties  |说明  |
|---------|---------|
|已启用     | 确定是否应用了设置        |
|项名称     | 要跟踪的文件的友好名称        |
|组     | 一个组名，用于对文件进行逻辑分组        |
|Windows 注册表项   | 用于查看文件的路径，例如：“HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Explorer\User Shell Folders\Common Startup”      |

### <a name="windows-files"></a>Windows 文件

|properties  |说明  |
|---------|---------|
|已启用     | 如果应用了设置，则为 True，否则为 False。        |
|项名称     | 要跟踪的文件的易记名称。        |
|组     | 用于对文件进行逻辑分组的组名。       |
|输入路径     | 用于查看文件的路径，例如，c:\temp\myfile.tx。

### <a name="linux-files"></a>Linux 文件

|properties  |说明  |
|---------|---------|
|已启用     | 如果应用了设置，则为 True，否则为 False。        |
|项名称     | 要跟踪的文件的易记名称。        |
|组     | 用于对文件进行逻辑分组的组名。        |
|输入路径     | 用于查看文件的路径，例如，/etc/*.conf。       |
|路径类型     | 要跟踪的项的类型。 值为“文件”和“目录”。        |
|递归     | 在查找要跟踪的项时，如果使用了递归，则为 True，否则为 False。        |
|使用 Sudo     | 如果在查看项时使用了 sudo，则为 True，否则为 False。         |
|链接     | 指示在遍历目录时如何处理符号链接的值。 可能的值包括： <br> 忽略 - 忽略符号链接，不包括引用的文件/目录<br>追随 - 在递归期间追随符号链接，并且包含引用的文件/目录<br>管理 - 追随符号链接并允许修改对返回内容的处置方式      |

## <a name="manage-machine-groups"></a>管理计算机组

使用清单，可以在 Azure Monitor 日志中创建和查看计算机组。 计算机组是由 Azure Monitor 日志中的查询定义的计算机集合。

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

要查看计算机组，请在清单页面上选择“计算机组”选项卡。

![在库存页面上查看计算机组](./media/automation-vm-inventory/inventory-machine-groups.png)

从列表中选择一个计算机组将打开“计算机组”页面。 此页面显示有关计算机组的详细信息。 这些详细信息包括用来定义组的 Log Analytics 查询。 此页面的底部显示了属于该组的计算机的分页列表。

![查看计算机组页面](./media/automation-vm-inventory/machine-group-page.png)

单击“+ 克隆”可以克隆计算机组。 必须为组提供新名称以及别名。 此时可以更改定义。 更改查询后，单击“验证查询”来预览将被选择的计算机。 当对组感到满意时，单击“创建”来创建计算机组。

如果希望创建新的计算机组，请单击“+ 创建计算机组”。 此按钮将打开“创建计算机组”页，可在其中定义新组。 单击“创建”以创建组。

![创建新的计算机组](./media/automation-vm-inventory/create-new-group.png)

## <a name="disconnect-your-vm-from-management"></a>断开 VM 与管理界面的连接

若要从更改跟踪和清单管理界面中删除 VM，请执行以下操作：

1. 在 Azure 门户的左窗格中选择“Log Analytics”，然后选择为 VM 启用更改跟踪和清单功能时使用过的工作区。
2. 在“Log Analytics”页，打开“资源”菜单。
3. 在“工作区数据源”下选择“虚拟机”。
4. 在列表中选择要断开连接的 VM。 该计算机有一个绿色复选标记，位于“OMS 连接”列中“此工作区”的旁边 。

   >[!NOTE]
   >Operations Management Suite (OMS) 现在称为 Azure Monitor 日志。
   
5. 在下一页的顶部，单击“断开连接”。
6. 在确认窗口中，单击“是”断开计算机与管理界面的连接。

## <a name="next-steps"></a>后续步骤

* 有关使用此功能的详细信息，请参阅[管理更改跟踪和清单](change-tracking-file-contents.md)。
* 若要详细了解如何跟踪软件更改，请参阅[使用更改跟踪来跟踪环境中的软件更改](../log-analytics/log-analytics-change-tracking.md)。
* 若要排查该功能的常见问题，请参阅[排查更改跟踪和清单问题](troubleshoot/change-tracking.md)。