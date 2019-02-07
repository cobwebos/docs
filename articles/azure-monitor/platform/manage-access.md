---
title: 管理 Azure Log Analytics 和 OMS 门户中的工作区 | Microsoft 文档
description: 可以通过对用户、帐户、工作区和 Azure 帐户使用各种管理任务来管理 Azure Log Analytics 和 OMS 门户中的工作区。
services: log-analytics
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: ''
ms.assetid: d0e5162d-584b-428c-8e8b-4dcaa746e783
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 11/13/2018
ms.author: magoedte
ms.openlocfilehash: 32a31a87bacbb13cd3b2cb4561ac04e54d51ba46
ms.sourcegitcommit: de32e8825542b91f02da9e5d899d29bcc2c37f28
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/02/2019
ms.locfileid: "55656747"
---
# <a name="manage-workspaces"></a>管理工作区

若要管理对 Log Analytics 的访问，需要对工作区执行各种管理任务。 本文提供管理工作区的建议和流程。 工作区实际上是包含帐户信息和帐户简单配置信息的容器。 你或组织中的其他成员可以使用多个工作区，管理收集自所有或部分 IT 基础结构的不同数据集。

若要创建工作区，需要：

1. 一个 Azure 订阅。
2. 选择工作区名称。
3. 将工作区与订阅和资源组之一相关联。
4. 选择地理位置。

## <a name="determine-the-number-of-workspaces-you-need"></a>确定所需工作区数
工作区是一种 Azure 资源，也是 Azure 门户中收集、聚合、分析和呈现数据的容器。

你可以为每个 Azure 订阅创建多个工作区，并且你可以访问多个工作区，并且可以轻松查询这些工作区。 本部分介绍有助于创建多个工作区的情况。

工作区目前提供：

* 数据存储的地理位置
* 数据隔离，以定义不同的用户访问权限
* 设置配置的范围，如[定价层](https://docs.microsoft.com/azure/azure-monitor/platform/manage-cost-storage#changing-pricing-tier)、[保留期](https://docs.microsoft.com/azure/azure-monitor/platform/manage-cost-storage#change-the-data-retention-period)和[数据上限](https://docs.microsoft.com/azure/azure-monitor/platform/manage-cost-storage#daily-cap) 

从使用角度来看，我们建议尽量减少创建的工作区数量。 这可带来更轻松快捷的管理和查询体验。 但是，基于上述特征，在以下情况下可能需要创建多个工作区：

* 贵公司是全球性公司，因数据所有权和合规性需要将数据存储于特定区域。
* 正在使用 Azure，并希望通过让工作区与它所管理的 Azure 资源位于同一区域，避免产生出站数据传输费用。
* 希望通过在其自己的 Azure 订阅中为每个部门或业务组创建一个工作区，根据使用量将费用分配到各个部门或业务组。
* 公司是托管服务提供商，需要为所管理的每位客户单独保留 Log Analytics 数据，即与其他客户的数据分开保存。
* 管理多个客户，并希望每个客户/部门/业务组能够看到自己的数据，但看不到他人的数据。

使用 Windows 代理收集数据时，可[将每个代理配置为向一个或多个工作区报告](../../azure-monitor/platform/agent-windows.md)。

如果使用 System Center Operations Manager，每个 Operations Manager 管理组仅可以连接一个工作区。 可以在 Operations Manager 管理的计算机上安装 Microsoft Monitoring Agent，并使代理向 Operations Manager 和不同 Log Analytics 工作区报告。

## <a name="workspace-information"></a>工作区信息

可以在 Azure 门户中查看有关工作区的详细信息。 

1. 如果尚未登录 [Azure 门户](https://portal.azure.com)，请先登录。

2. 在 Azure 门户中，单击“所有服务”。 在资源列表中，键入“Log Analytics”。 开始键入时，会根据输入筛选该列表。 选择“Log Analytics”。  

    ![Azure 门户](media/manage-access/azure-portal-01.png)  

3. 在 Log Analytics 订阅窗格中选择一个工作区。

4. 工作区页会显示有关入门、配置的详细信息，以及其他信息的链接。  

    ![工作区详细信息](./media/manage-access/workspace-overview-page.png)  

## <a name="manage-accounts-and-users"></a>管理帐户和用户
每个工作区可有多个与其关联的帐户，每个帐户可访问多个工作区。 通过 [Azure 基于角色的访问权限](../../role-based-access-control/role-assignments-portal.md)来管理访问权限。 这些访问权限同时适用于 Azure 门户和 API 访问。


以下活动也需要 Azure 权限：

| 操作                                                          | 所需 Azure 权限 | 说明 |
|-----------------------------------------------------------------|--------------------------|-------|
| 添加和删除管理解决方案                        | `Microsoft.Resources/deployments/*` <br> `Microsoft.OperationalInsights/*` <br> `Microsoft.OperationsManagement/*` <br> `Microsoft.Automation/*` <br> `Microsoft.Resources/deployments/*/write` | 需要在资源组或订阅级别授予这些权限。 |
| 更改定价层                                       | `Microsoft.OperationalInsights/workspaces/*/write` | |
| 查看*备份* 和 *Site Recovery* 解决方案磁贴中的数据 | 管理员/共同管理员 | 访问通过经典部署模型部署的资源 |
| 在 Azure 门户中创建工作区                        | `Microsoft.Resources/deployments/*` <br> `Microsoft.OperationalInsights/workspaces/*` ||


### <a name="managing-access-to-log-analytics-using-azure-permissions"></a>使用 Azure 权限管理对 Log Analytics 的访问
若要使用 Azure 权限授予对 Log Analytics 工作区的访问权限，请执行[使用角色分配来管理对 Azure 订阅资源的访问权限](../../role-based-access-control/role-assignments-portal.md)中的步骤。

Azure 有两个内置的适用于 Log Analytics 的用户角色：
- Log Analytics 读者
- Log Analytics 参与者

Log Analytics 读者角色的成员可以：
- 查看和搜索所有监视数据 
- 查看监视设置，包括查看 Azure 诊断在所有 Azure 资源上的配置。

Log Analytics 读者角色包括以下 Azure 操作：

| Type    | 权限 | 说明 |
| ------- | ---------- | ----------- |
| 操作 | `*/read`   | 能够查看所有 Azure 资源和资源配置。 包括查看： <br> 虚拟机扩展状态 <br> Azure 诊断在资源上的配置 <br> 所有资源的所有属性和设置 |
| 操作 | `Microsoft.OperationalInsights/workspaces/analytics/query/action` | 能够执行 Log Search v2 查询 |
| 操作 | `Microsoft.OperationalInsights/workspaces/search/action` | 能够执行 Log Search v1 查询 |
| 操作 | `Microsoft.Support/*` | 能够打开支持案例 |
|非操作 | `Microsoft.OperationalInsights/workspaces/sharedKeys/read` | 防止读取工作区密钥，该密钥是使用数据集合 API 和安装代理所必需的。 这可以防止用户向工作区添加新资源 |


Log Analytics 参与者角色的成员可以：
- 像 Log Analytics 读者一样读取所有监视数据  
- 创建和配置自动化帐户  
- 添加和删除管理解决方案    
    > [!NOTE] 
    > 若要成功执行最后两个操作，需要在资源组或订阅级别授予此权限。  

- 读取存储帐户密钥   
- 从 Azure 存储配置日志集合  
- 编辑 Azure 资源的监视设置，包括
  - 将 VM 扩展添加到 VM
  - 在所有 Azure 资源上配置 Azure 诊断

> [!NOTE] 
> 可以使用此功能向虚拟机添加虚拟机扩展，获取对虚拟机的完全控制。

Log Analytics 参与者角色包括以下 Azure 操作：

| 权限 | 说明 |
| ---------- | ----------- |
| `*/read`     | 能够查看所有资源和资源配置。 包括查看： <br> 虚拟机扩展状态 <br> Azure 诊断在资源上的配置 <br> 所有资源的所有属性和设置 |
| `Microsoft.Automation/automationAccounts/*` | 能够创建和配置 Azure 自动化帐户，包括添加和编辑 runbook |
| `Microsoft.ClassicCompute/virtualMachines/extensions/*` <br> `Microsoft.Compute/virtualMachines/extensions/*` | 添加、更新和删除虚拟机扩展，包括 Microsoft Monitoring Agent 扩展和 OMS Agent for Linux 扩展 |
| `Microsoft.ClassicStorage/storageAccounts/listKeys/action` <br> `Microsoft.Storage/storageAccounts/listKeys/action` | 查看存储帐户密钥。 将 Log Analytics 配置为从 Azure 存储帐户读取日志所必需 |
| `Microsoft.Insights/alertRules/*` | 添加、更新和删除警报规则 |
| `Microsoft.Insights/diagnosticSettings/*` | 添加、更新和删除 Azure 资源上的诊断设置 |
| `Microsoft.OperationalInsights/*` | 添加、更新和删除 Log Analytics 工作区的配置 |
| `Microsoft.OperationsManagement/*` | 添加和删除管理解决方案 |
| `Microsoft.Resources/deployments/*` | 创建和删除部署。 添加和删除解决方案、工作区和自动化帐户所必需 |
| `Microsoft.Resources/subscriptions/resourcegroups/deployments/*` | 创建和删除部署。 添加和删除解决方案、工作区和自动化帐户所必需 |

若要添加和删除用户角色的用户，必须拥有 `Microsoft.Authorization/*/Delete` 和 `Microsoft.Authorization/*/Write` 权限。

使用这些角色为用户提供不同范围的访问权限：
- 订阅 - 访问订阅中的所有工作区
- 资源组 - 访问资源组中的所有工作区
- 资源 - 仅访问指定工作区

我们建议你在资源级别（工作区）执行分配，以确保准确的访问控制。  使用[自定义角色](../../role-based-access-control/custom-roles.md)，创建具有所需的特定权限的角色。

## <a name="next-steps"></a>后续步骤
* 请参阅 [Log Analytics 代理概述](../../azure-monitor/platform/log-analytics-agent.md)，以从数据中心或其他云环境中的计算机收集数据。
* 请参阅[收集有关 Azure 虚拟机的数据](../../azure-monitor/learn/quick-collect-azurevm.md)，以配置 Azure VM 的数据收集。  
* [从解决方案库中添加 Log Analytics 解决方案](../../azure-monitor/insights/solutions.md)，以添加功能和收集数据。

