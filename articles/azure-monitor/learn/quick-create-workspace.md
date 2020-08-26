---
title: 在 Azure 门户中创建 Log Analytics 工作区 | Microsoft Docs
description: 了解如何在 Azure 门户中创建 Log Analytics 工作区，以启用管理解决方案以及从云和本地环境进行的数据收集。
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 05/26/2020
ms.openlocfilehash: f86cc5e3edc659a233066fcf2544ed4a5019d0f9
ms.sourcegitcommit: a76ff927bd57d2fcc122fa36f7cb21eb22154cfa
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/28/2020
ms.locfileid: "87324278"
---
# <a name="create-a-log-analytics-workspace-in-the-azure-portal"></a>在 Azure 门户中创建 Log Analytics 工作区
使用“Log Analytics 工作区”菜单通过 Azure 门户创建 Log Analytics 工作区。 Log Analytics 工作区是适用于 Azure Monitor 日志数据的唯一环境。 每个工作区都有其自己的数据存储库和配置，并且数据源和解决方案均配置为将其数据存储在特定工作区中。 如果打算从以下源收集数据，则需要 Log Analytics 工作区：

* 订阅中的 Azure 资源
* 受 System Center Operations Manager 监视的本地计算机
* Configuration Manager 中的设备集合 
* Azure 存储中的诊断或日志数据

对于其他源，如环境中的 Azure VM 和 Windows VM 或 Linux VM，请参阅以下主题：

*  [从 Azure 虚拟机收集数据](./quick-collect-azurevm.md) 
*  [从混合 Linux 计算机收集数据](./quick-collect-linux-computer.md)
*  [从混合 Windows 计算机收集数据](quick-collect-windows-computer.md)

如果没有 Azure 订阅，请在开始之前创建一个[免费帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

## <a name="sign-in-to-azure-portal"></a>登录到 Azure 门户
在 [https://portal.azure.com](https://portal.azure.com) 中登录 Azure 门户。 

## <a name="create-a-workspace"></a>创建工作区
1. 在 Azure 门户中，单击“所有服务”。 在资源列表中，键入“Log Analytics”。 开始键入时，会根据输入筛选该列表。 选择“Log Analytics 工作区”。

    ![Azure 门户](media/quick-create-workspace/azure-portal-01.png)
  
2. 单击“添加”，然后为以下各项选择选项：

   * 为新的 Log Analytics 工作区提供名称，如 DefaultLAWorkspace。 此名称在所有 Azure Monitor 订阅中必须是全局唯一的。
   * 如果选择的默认值不合适，请从下拉列表中选择要链接到的**订阅**。
   * 对于**资源组**，选择要使用已设置的现有资源组，还是要创建一个新资源组。  
   * 选择可用**位置**。  有关详细信息，请参阅[可在哪些区域中使用 Log Analytics](https://azure.microsoft.com/regions/services/)，并在“搜索产品”字段中搜索 Azure Monitor。  
   * 如果在 2018 年 4 月 2 日后创建的新订阅中创建工作区，则它将自动使用“每 GB”定价计划，并且不提供用于选择定价层的选项。  如果是为 4 月 2 日之前创建的现有订阅创建工作区，或者是为绑定到现有企业协议 (EA) 注册的订阅创建工作区，则可以选择首选定价层。  有关特定层的详细信息，请参阅 [Log Analytics 定价详细信息](https://azure.microsoft.com/pricing/details/log-analytics/)。

        ![创建 Log Analytics 资源边栏选项卡](media/quick-create-workspace/create-loganalytics-workspace-02.png)  

3. 在“Log Analytics 工作区”窗格上提供所需信息后，单击“确定” 。  

在验证信息和创建工作区时，可以在菜单中的“通知”下面跟踪操作进度。 

## <a name="troubleshooting"></a>疑难解答
如果创建了一个工作区，该工作区已在过去 14 天内删除且处于[软删除状态](../platform/delete-workspace.md#soft-delete-behavior)，那么该操作可能会有不同的结果，具体取决于你的工作区配置：
1. 如果你提供的工作区名称、资源组、订阅和区域与已删除的工作区中的相同，则将恢复你的工作区，包括其数据、配置和连接的代理。
2. 如果你使用相同的工作区名称，但提供不同的资源组、订阅或区域，则将收到错误“此工作区名称已被使用。请尝试输入其他名称。” 若要替换软删除，同时永久删除你的工作区并创建新的同名工作区，请按照以下步骤，先恢复工作区再执行永久删除：
   - [恢复](../platform/delete-workspace.md#recover-workspace)工作区
   - [永久删除](../platform/delete-workspace.md#permanent-workspace-delete)工作区
   - 使用相同的工作区名称创建新的工作区

## <a name="next-steps"></a>后续步骤
现在，你已有可用的工作区，可以配置监视遥测收集、运行日志搜索分析该数据，以及添加管理解决方案以提供其他数据和分析见解。 

* 若要启用通过 Azure 诊断或 Azure 存储从 Azure 资源收集数据，请参阅[收集要在 Log Analytics 中使用的 Azure 服务日志和指标](../platform/resource-logs.md#send-to-log-analytics-workspace)。  
* [将 System Center Operations Manager 添加为数据源](../platform/om-agents.md)以从报告 Operations Manager 管理组的代理收集数据并将其存储在 Log Analytics 工作区中。 
* 连接 [Configuration Manager](../platform/collect-sccm.md) 以导入作为层次结构中集合成员的计算机。  
* 查看可用的[监视解决方案](../insights/solutions.md)以及如何从工作区添加或删除解决方案。

