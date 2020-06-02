---
title: 使用 Azure 自动化发现 VM 上安装的软件 | Microsoft Docs
description: 本文将介绍在环境中的 VM 上安装的软件。
services: automation
keywords: 清单, 自动化, 更改跟踪
ms.date: 04/11/2018
ms.topic: tutorial
ms.subservice: change-inventory-management
ms.custom: mvc
ms.openlocfilehash: d4acecbc6d1a1d7f617b0da95da1b97dc5a3dd75
ms.sourcegitcommit: 493b27fbfd7917c3823a1e4c313d07331d1b732f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/21/2020
ms.locfileid: "83743671"
---
# <a name="discover-what-software-is-installed-on-your-vms"></a>发现 VM 上安装的软件

本教程介绍如何使用 Azure 自动化更改跟踪和清单功能来找出环境中安装的软件。 可以收集并查看清单，了解计算机上的软件、文件、Linux 守护程序、Windows 服务和 Windows 注册表项。 跟踪计算机的配置有助于查明环境中的操作问题，更好地了解计算机的状态。

本教程介绍如何执行下列操作：

> [!div class="checklist"]
> * 启用更改跟踪和库存
> * 启用 Azure VM
> * 启用非 Azure VM
> * 查看已安装软件
> * 在清单日志中搜索已安装的软件

## <a name="prerequisites"></a>先决条件

要完成本教程，需要：

* Azure 订阅。 如果还没有帐户，可以[激活 MSDN 订户权益](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/)或注册[免费帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。
* [自动化帐户](automation-offering-get-started.md)，用于保存观察程序、操作 runbook 和观察程序任务。
* 要启用该功能的[虚拟机](../virtual-machines/windows/quick-create-portal.md)。

## <a name="log-in-to-azure"></a>登录 Azure

通过 https://portal.azure.com 登录到 Azure 门户。

## <a name="enable-change-tracking-and-inventory"></a>启用更改跟踪和库存

就本教程来说，首先需要启用更改跟踪和清单。 如果以前启用了该功能，则无需执行此步骤。

>[!NOTE]
>如果字段灰显，则意味着已为 VM 启用其他自动化功能，因此必须使用同一工作区和自动化帐户。

1. 导航到自动化帐户，在“配置管理”下选择“清单”或“更改跟踪”  。

2. 选择 [Log Analytics](../log-analytics/log-analytics-overview.md?toc=%2fazure%2fautomation%2ftoc.json) 工作区。 此工作区将收集由更改跟踪和清单等功能生成的数据。 工作区提供了一个位置来查看和分析来自多个数据源的数据。

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

3. 选择要使用的自动化帐户。

4. 配置部署位置。

5. 单击“启用”为 VM 部署该功能。 

    ![清单配置横幅](./media/automation-tutorial-installed-software/enableinventory.png)

在设置期间，将使用适用于 Windows 的 Log Analytics 代理和[混合 Runbook 辅助角色](automation-hybrid-runbook-worker.md)来预配 VM。 启用更改跟踪和清单最多可能需要 15 分钟。 在此期间，不应关闭浏览器窗口。

启用该功能后，VM 中有关已安装软件和更改的信息会流向 Azure Monitor 日志。 这些数据需花费 30 分钟到 6 小时的时间才能用于分析。

## <a name="add-an-azure-vm-to-change-tracking-and-inventory"></a>添加要启用更改跟踪和清单的 Azure VM

1. 在自动化帐户中，导航到“配置管理”下的“清单”或“更改跟踪”  。

2. 选择“+ 添加 Azure VM”。

3. 从 VM 列表中选择 VM。 

4. 单击“启用”以在 VM 上启用更改跟踪和清单。 适用于 Windows 的 Log Analytics 代理已部署到 VM，并将 VM 配置为与 Log Analytics 工作区通信。 设置操作可能需要几分钟的时间。 

5. 此时，如果需要，可以从列表中选择一个新的 VM 来启用该功能。

## <a name="add-a-non-azure-machine-to-change-tracking-and-inventory"></a>添加要启用更改跟踪和清单的非 Azure 计算机

若要为非 Azure 计算机启用该功能，请执行以下操作：

1. 根据你的操作系统安装[适用于 Windows 的 Log Analytics 代理](../azure-monitor/platform/agent-windows.md)或[适用于 Linux 的 Log Analytics 代理](automation-linux-hrw-install.md)。 

2. 导航到自动化帐户，然后转到“配置管理”下的“清单”或“更改跟踪”  。 

3. 单击“管理计算机”。 系统会显示向 Log Analytics 工作区进行报告且尚未启用更改跟踪和清单的计算机的列表。 选择适合你的环境的选项：

    * **在所有可用计算机上启用** - 此选项在当前向 Log Analytics 工作区进行报告的所有计算机上启用此功能。
    * **在所有可用计算机以及将来的计算机上启用** - 此选项在向 Log Analytics 工作区进行报告的所有计算机上启用此功能，并且会在将来添加到该工作区的所有计算机上启用此功能。
    * **在所选计算机上启用** - 此选项仅在你选择的计算机上启用此功能。

    ![管理计算机](./media/automation-tutorial-installed-software/manage-machines.png)

## <a name="view-installed-software"></a>查看已安装软件

启用更改跟踪和清单功能后，即可在“清单”页中查看结果。

1. 在你的自动化帐户中，选择“配置管理”下的“清单”。

2. 在“清单”页上单击“软件”选项卡。

3. 请注意一个表，其中列出了已发现的软件。 软件按软件名称和版本分组。 可在表中查看每个软件记录的高级详细信息。 这些详细信息包括：软件名称、版本、发布者、上次刷新时间（由组中的计算机报告的最近刷新时间）、计算机数（装有该软件的计算机的计数）。

    ![软件清单](./media/automation-tutorial-installed-software/inventory-software.png)

4. 单击一行即可查看软件记录的属性以及装有该软件的计算机的名称。

5. 若要查找特定的软件或软件组，可以在软件列表正上方的文本框中搜索。
可以使用筛选器根据软件名称、版本或发布者进行搜索。 例如，搜索“Contoso”会返回名称、发布者或版本中包含“Contoso”的所有软件 。

## <a name="search-inventory-logs-for-installed-software"></a>在清单日志中搜索已安装的软件

更改跟踪和清单会生成发送到 Azure Monitor 日志的日志数据。 若要通过运行查询来搜索日志，请选择“清单”页顶部的“Log Analytics”。 清单数据存储在 `ConfigurationData` 类型下。

以下示例 Log Analytics 查询将返回发布者 Microsoft Corporation 的清单结果。

```loganalytics
ConfigurationData
| where ConfigDataType == "Software"
| where Publisher == "Microsoft Corporation"
| summarize arg_max(TimeGenerated, *) by SoftwareName, Computer
```

若要详细了解如何在 Azure Monitor 日志中运行和搜索日志文件，请参阅 [Azure Monitor 日志](../azure-monitor/log-query/log-query-overview.md)。

## <a name="see-the-software-inventory-for-a-single-machine"></a>查看单个计算机的软件清单

若要查看单个计算机的软件清单，可以访问 Azure VM 资源页中的“清单”，也可以使用 Azure Monitor 日志筛选出相应的计算机。 以下 Log Analytics 查询示例返回名为“ContosoVM”的计算机的软件列表。

```loganalytics
ConfigurationData
| where ConfigDataType == "Software"
| summarize arg_max(TimeGenerated, *) by SoftwareName, CurrentVersion
| where Computer =="ContosoVM"
| render table
| summarize by Publisher, SoftwareName
```

## <a name="next-steps"></a>后续步骤

本教程介绍了如何查看软件清单：

> [!div class="checklist"]
> * 启用更改跟踪和库存
> * 启用 Azure VM
> * 启用非 Azure VM
> * 查看已安装软件
> * 在清单日志中搜索已安装的软件

继续阅读更改跟踪和清单功能的概述可以了解其详细信息。

> [!div class="nextstepaction"]
> [更改跟踪和清单概述](change-tracking.md)
