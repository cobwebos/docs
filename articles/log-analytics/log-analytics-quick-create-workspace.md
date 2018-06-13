---
title: 在 Azure Log Analytics 中创建工作区 | Microsoft Docs
description: 了解如何创建 Log Analytics 工作区，以启用管理解决方案以及从云和本地环境进行的数据收集。
services: log-analytics
documentationcenter: log-analytics
author: MGoedtel
manager: carmonm
editor: ''
ms.assetid: ''
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/02/2018
ms.author: magoedte
ms.openlocfilehash: e05d620a457edf427c946366a9caca471ac8531e
ms.sourcegitcommit: 6fcd9e220b9cd4cb2d4365de0299bf48fbb18c17
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/05/2018
ms.locfileid: "30833126"
---
# <a name="create-a-log-analytics-workspace-in-the-azure-portal"></a>在 Azure 门户中创建 Log Analytics 工作区
在 Azure 门户中可以设置 Log Analytics 工作区，该工作区是一个具有其自己的数据存储库、数据源和解决方案的独特 Log Analytics 环境。  如果要从以下源中收集数据，本文中所述的步骤是必需的：

* 订阅中的 Azure 资源
* 受 System Center Operations Manager 监视的本地计算机
* System Center Configuration Manager 中的设备集合 
* Azure 存储中的诊断或日志数据

对于其他源，如环境中的 Azure VM 和 Windows 或 Linux 计算机，请参阅以下主题：

*  [收集 Azure 虚拟机相关数据](log-analytics-quick-collect-azurevm.md) 
*  [收集 Linux 计算机相关数据](log-analytics-quick-collect-linux-computer.md)
*  [收集 Windows 计算机相关数据](log-analytics-quick-collect-windows-computer.md)

如果你还没有 Azure 订阅，可以在开始前创建一个 [免费帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

## <a name="log-in-to-azure-portal"></a>登录到 Azure 门户
通过 [https://portal.azure.com](https://portal.azure.com) 登录到 Azure 门户。 

## <a name="create-a-workspace"></a>创建工作区
1. 在 Azure 门户中，单击“所有服务”。 在资源列表中，键入“Log Analytics”。 开始键入时，会根据输入筛选该列表。 选择“Log Analytics”。<br><br> ![Azure 门户](media/log-analytics-quick-collect-azurevm/azure-portal-01.png)<br><br>  
2. 单击“创建”，然后为以下各项选择选项：

  * 为新的 OMS 工作区提供名称，如 DefaultLAWorkspace。 
  * 如果选择的默认值不合适，请从下拉列表中选择要链接到的**订阅**。
  * 对于**资源组**，选择要使用已设置的现有资源组，还是要创建一个新资源组。  
  * 选择可用**位置**。  如需其他信息，请参阅[提供 Log Analytics 的区域](https://azure.microsoft.com/regions/services/)。
  * 如果在 2018 年 4 月 2 日后创建的新订阅中创建工作区，则它将自动使用“每 GB”定价计划，并且不提供用于选择定价层的选项。  如果是为 4 月 2 日之前创建的现有订阅创建工作区，或者是为绑定到现有 EA 注册的订阅创建工作区，则可以选择首选定价层。  有关特定层的其他信息，请参阅 [Log Analytics 定价详细信息](https://azure.microsoft.com/pricing/details/log-analytics/)。

        ![Create Log Analytics resource blade](media/log-analytics-quick-collect-azurevm/create-loganalytics-workspace-02.png)<br>  

3. 在“OMS 工作区”窗格上提供所需信息后，单击“确定”。  

在验证信息和创建工作区时，可以在菜单中的“通知”下面跟踪操作进度。 

## <a name="next-steps"></a>后续步骤
现在，你已有可用的工作区，可以配置监视遥测收集、运行日志搜索分析该数据，以及添加管理解决方案以提供其他数据和分析见解。 

* 若要启用通过 Azure 诊断或 Azure 存储从 Azure 资源收集数据，请参阅[在 Log Analytics 中收集要使用的 Azure 服务日志和指标](log-analytics-azure-storage.md)。  
* [将 System Center Operations Manager 添加为数据源](log-analytics-om-agents.md)以从报告 Operations Manager 管理组的代理收集数据并将其存储在 Log Analytics 工作区存储库中。 
* 连接 [Configuration Manager](log-analytics-sccm.md) 以导入作为层次结构中集合成员的计算机。  
* 查看可用的[管理解决方案](/log-analytics-add-solutions.md)以及如何从工作区添加或删除解决方案。

