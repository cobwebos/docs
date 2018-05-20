---
title: 使用 Azure 自动化发现在计算机上安装的软件 | Microsoft Docs
description: 使用清单发现在环境中的计算机上安装的软件。
services: automation
keywords: 清单, 自动化, 更改, 跟踪
author: jennyhunter-msft
ms.author: jehunte
ms.date: 04/11/2018
ms.topic: tutorial
ms.service: automation
ms.component: change-inventory-management
ms.custom: mvc
manager: carmonm
ms.openlocfilehash: cb876a8d8019f5a2a7232c3093c6f64a7b2730e1
ms.sourcegitcommit: d28bba5fd49049ec7492e88f2519d7f42184e3a8
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/11/2018
---
# <a name="discover-what-software-is-installed-on-your-azure-and-non-azure-machines"></a>发现在 Azure 计算机和非 Azure 计算机上安装的软件

本教程介绍如何发现在环境中安装的软件。 可以收集和查看清单，了解计算机上的软件、文件、Linux 守护程序、Windows 服务和 Windows 注册表项。 跟踪计算机的配置有助于查明环境中的操作问题，更好地了解计算机的状态。

本教程介绍如何执行下列操作：

> [!div class="checklist"]
> * 启用此解决方案
> * 载入 Azure VM
> * 载入非 Azure VM
> * 查看已安装软件
> * 在清单日志中搜索已安装软件

## <a name="prerequisites"></a>先决条件

要完成本教程，需要：

* Azure 订阅。 如果还没有帐户，可以[激活 MSDN 订户权益](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/)或注册[免费帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。
* [自动化帐户](automation-offering-get-started.md)，用于保存观察程序、操作 Runbook 和观察程序任务。
* 要载入的[虚拟机](../virtual-machines/windows/quick-create-portal.md)。

## <a name="log-in-to-azure"></a>登录 Azure

通过 http://portal.azure.com 登录到 Azure 门户。

## <a name="enable-change-tracking-and-inventory"></a>启用更改跟踪和清单

就本教程来说，首先需要启用更改跟踪和清单。 如果以前已经启用了**更改跟踪**解决方案，则此步骤不是必需的。

导航到你的自动化帐户，在“配置管理”下选择“清单”。

选择 Log Analytics 工作区和自动化帐户，然后单击“启用”以启用此解决方案。 启用此解决方案最长需要 15 分钟的时间。

![清单载入配置横幅](./media/automation-tutorial-installed-software/enableinventory.png)

若要启用此解决方案，请配置要使用的位置、Log Analytics 工作区和自动化帐户，然后单击“启用”。 如果这些字段灰显，则意味着已经为 VM 启用了其他自动化解决方案，因此必须使用同一工作区和自动化帐户。

[Log Analytics](../log-analytics/log-analytics-overview.md?toc=%2fazure%2fautomation%2ftoc.json) 工作区用于收集由功能和服务（如清单）生成的数据。
工作区提供了一个位置来查看和分析来自多个数据源的数据。

启用解决方案最多可能需要 15 分钟。 在此期间，不应关闭浏览器窗口。
启用该解决方案后，VM 中有关已安装软件和更改的信息会流向 Log Analytics。
这些数据需花费 30 分钟到 6 小时的时间才能用于分析。

## <a name="onboard-a-vm"></a>载入 VM

在你的自动化帐户中，导航到“配置管理”下的“清单”。

选择“+ 添加 Azure VM”，这将打开“虚拟机”页面并允许从列表中选择现有 VM。 选择要载入的 VM。 在打开的页面上，单击“启用”以在 VM 上启用此解决方案。 Microsoft 管理代理部署到 VM，并配置代理来与你在启用解决方案时配置的 Log Analytics 工作区进行通信。 可能需要花费几分钟时间才能完成载入。 此时，可以从列表中选择一台新的 VM 并载入另一 VM。

## <a name="onboard-a-non-azure-machine"></a>载入非 Azure 计算机

若要添加非 Azure 计算机，请根据你的操作系统安装适用于 [Windows](../log-analytics/log-analytics-agent-windows.md) 或 [Linux](automation-linux-hrw-install.md) 的代理。 安装代理后，导航到你的自动化帐户，然后转到“配置管理”下的“清单”。 单击“管理计算机”时，可以看到向你的 Log Analytics 工作区进行报告且尚未启用此解决方案的计算机的列表。 选择适合你的环境的选项。

* **在所有可用计算机上启用** - 此选项在当前向你的 Log Analytics 工作区进行报告的所有计算机上启用此解决方案。
* **在所有可用计算机以及将来的计算机上启用** - 此选项在向你的 Log Analytics 工作区进行报告的所有计算机上启用此解决方案，并且会在将来添加到该工作区的所有计算机上启用此解决方案。
* **在所选计算机上启用** - 此选项仅在你选择的计算机上启用此解决方案。

![管理计算机](./media/automation-tutorial-installed-software/manage-machines.png)

## <a name="view-installed-software"></a>查看已安装软件

启用更改跟踪和清单解决方案以后，即可在“清单”页查看结果。

从你的自动化帐户中，在“配置管理”下选择“清单”。

在“清单”页上单击“软件”选项卡。

“软件”选项卡上有一个表，其中列出了已发现的软件。 软件按软件名称和版本分组。

可在表中查看每个软件记录的高级详细信息。 这些详细信息包括：软件名称、版本、发布者、上次刷新时间（由组中的计算机报告的最近刷新时间）、计算机数（装有该软件的计算机的计数）。

![软件清单](./media/automation-tutorial-installed-software/inventory-software.png)

单击一行即可查看软件记录的属性以及装有该软件的计算机的名称。

若要查找特定的软件或软件组，可以在软件列表正上方的文本框中搜索。
可以使用筛选器根据软件名称、版本或发布者进行搜索。

例如，搜索“Contoso”会返回名称、发布者或版本中包含“Contoso”的所有软件。

## <a name="search-inventory-logs-for-installed-software"></a>在清单日志中搜索已安装软件

清单生成发送到 Log Analytics 的日志数据。 若要通过运行查询来搜索日志，请选择“清单”窗口顶部的“Log Analytics”。

清单数据存储在 **ConfigurationData** 类型下。
以下示例 Log Analytics 查询返回 Publisher 等于“Microsoft Corporation”的清单结果。

```loganalytics
ConfigurationData
| where ConfigDataType == "Software"
| where Publisher == "Microsoft Corporation"
| summarize arg_max(TimeGenerated, *) by SoftwareName, Computer
```

若要详细了解如何在 Log Analytics 中运行和搜索日志文件，请参阅 [Azure Log Analytics](https://docs.loganalytics.io/index)。

### <a name="single-machine-inventory"></a>单个计算机的清单

若要查看单个计算机的软件清单，可以访问 Azure VM 资源页中的“清单”，也可以使用 Log Analytics 筛选出相应的计算机。
以下 Log Analytics 查询示例返回名为 ContosoVM 的计算机的软件列表。

```loganalytics
ConfigurationData
| where ConfigDataType == "Software"
| summarize arg_max(TimeGenerated, *) by SoftwareName, CurrentVersion
| where Computer =="ContosoVM"
| render table
| summarize by Publisher, SoftwareName
```

## <a name="next-steps"></a>后续步骤

本教程介绍了如何查看软件清单，例如，如何：

> [!div class="checklist"]
> * 启用此解决方案
> * 载入 Azure VM
> * 载入非 Azure VM
> * 查看已安装软件
> * 在清单日志中搜索已安装软件

继续阅读更改跟踪和清单解决方案的概述可以了解其详细信息。

> [!div class="nextstepaction"]
> [变更管理和清单解决方案](automation-change-tracking.md)