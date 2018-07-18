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
ms.devlang: na
ms.topic: conceptual
ms.date: 05/17/2018
ms.author: magoedte
ms.component: na
ms.openlocfilehash: 3b4e0f978cc7d23d0157b78fd2dff27096d2768b
ms.sourcegitcommit: 5892c4e1fe65282929230abadf617c0be8953fd9
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/29/2018
ms.locfileid: "37133215"
---
# <a name="manage-workspaces"></a>管理工作区

若要管理对 Log Analytics 的访问，需要对工作区执行各种管理任务。 本文提供管理工作区的最佳实践建议和过程。 工作区实际上是包含帐户信息和帐户简单配置信息的容器。 你或组织中的其他成员可以使用多个工作区，管理收集自所有或部分 IT 基础结构的不同数据集。

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
* 设置配置的范围，如保留期和数据上限

从使用角度来看，我们建议创建尽可能少的工作区。 它使管理和查询体验更轻松、更快捷。 但是，基于上述特征，在以下情况下可能需要创建多个工作区：

* 贵公司是全球性公司，因数据所有权和合规性需要将数据存储于特定区域。
* 正在使用 Azure，并希望通过让工作区与它所管理的 Azure 资源位于同一区域，避免产生出站数据传输费用。
* 希望根据使用情况将费用分配到不同部门或业务组。 为每个部门或业务组在其自己的 Azure 订阅中创建工作区时。
* 公司是托管服务提供商，需要为所管理的每位客户单独保留 Log Analytics 数据，即与其他客户的数据分开保存。
* 管理多个客户，并希望每个客户/部门/业务组能够看到自己的数据，而不是他人的数据。

使用 Windows 代理收集数据时，可[将每个代理配置为向一个或多个工作区报告](log-analytics-windows-agents.md)。

如果使用 System Center Operations Manager，每个 Operations Manager 管理组仅可以连接一个工作区。 可以在 Operations Manager 管理的计算机上安装 Microsoft Monitoring Agent，并使代理向 Operations Manager 和不同 Log Analytics 工作区报告。

### <a name="workspace-information"></a>工作区信息

可以在 Azure 门户中查看有关工作区的详细信息。 

#### <a name="view-workspace-information-in-the-azure-portal"></a>在 Azure 门户中查看工作区信息

1. 如果尚未登录 [Azure 门户](https://portal.azure.com)，请使用 Azure 订阅登录。
2. 在“中心”菜单中，单击“更多服务”，并在资源列表中，键入“Log Analytics”。 开始键入时，会根据输入筛选该列表。 单击“Log Analytics”。  
    ![Azure 中心](./media/log-analytics-manage-access/hub.png)  
3. 在 Log Analytics 订阅边栏选项卡中选择一个工作区。
4. 工作区边栏选项卡会显示有关工作区的详细信息，以及其他信息的链接。  
    ![工作区详细信息](./media/log-analytics-manage-access/workspace-details.png)  


## <a name="manage-accounts-and-users"></a>管理帐户和用户
每个工作区可有多个与其关联的帐户，每个帐户可访问多个工作区。 通过 [Azure 基于角色的访问权限](../active-directory/role-based-access-control-configure.md)来管理访问权限。 这些访问权限同时适用于 Azure 门户和 API 访问。


以下活动也需要 Azure 权限：

| 操作                                                          | 所需 Azure 权限 | 说明 |
|-----------------------------------------------------------------|--------------------------|-------|
| 添加和删除管理解决方案                        | `Microsoft.Resources/deployments/*` <br> `Microsoft.OperationalInsights/*` <br> `Microsoft.OperationsManagement/*` <br> `Microsoft.Automation/*` <br> `Microsoft.Resources/deployments/*/write` | 需要在资源组或订阅级别授予这些权限。 |
| 更改定价层                                       | `Microsoft.OperationalInsights/workspaces/*/write` | |
| 查看*备份* 和 *Site Recovery* 解决方案磁贴中的数据 | 管理员/共同管理员 | 访问通过经典部署模型部署的资源 |
| 在 Azure 门户中创建工作区                        | `Microsoft.Resources/deployments/*` <br> `Microsoft.OperationalInsights/workspaces/*` ||


### <a name="managing-access-to-log-analytics-using-azure-permissions"></a>使用 Azure 权限管理对 Log Analytics 的访问
若要使用 Azure 权限授予对 Log Analytics 工作区的访问权限，请执行[使用角色分配来管理对 Azure 订阅资源的访问权限](../active-directory/role-based-access-control-configure.md)中的步骤。

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

我们建议你在资源级别（工作区）执行分配，以确保准确的访问控制。  使用[自定义角色](../active-directory/role-based-access-control-custom-roles.md)，创建具有所需的特定权限的角色。

## <a name="link-an-existing-workspace-to-an-azure-subscription"></a>将现有工作区链接到 Azure 订阅
2016 年 9 月 26 日之后创建的所有工作区必须在创建时链接到 Azure 订阅。 此日期之前创建的工作区必须在登录时链接到某工作区。 从 Azure 门户创建工作区或者将工作区链接到 Azure 订阅时，Azure Active Directory 会链接为组织帐户。

### <a name="to-link-a-workspace-to-an-azure-subscription-in-the-azure-portal"></a>将工作区链接到 Azure 门户中的 Azure 订阅
1. 登录到 [Azure 门户](http://portal.azure.com)。
2. 浏览到 **Log Analytics**，并选择它。
3. 随即会看到现有工作区列表。 单击“添加”。  
   ![工作区列表](./media/log-analytics-manage-access/manage-access-link-azure01.png)
4. 在“OMS 工作区”下，单击“或链接现有的”。  
   ![链接现有的](./media/log-analytics-manage-access/manage-access-link-azure02.png)
5. 单击“配置所需设置”。  
   ![配置所需设置](./media/log-analytics-manage-access/manage-access-link-azure03.png)
6. 随即会看到尚未链接到 Azure 帐户的工作区列表。 选择工作区。  
   ![选择工作区](./media/log-analytics-manage-access/manage-access-link-azure04.png)
7. 如果需要，可以更改以下项的值：
   * 订阅
   * 资源组
   * 位置
   * 定价层  
     ![更改值](./media/log-analytics-manage-access/manage-access-link-azure05.png)
8. 单击“确定”。 工作区现已链接到 Azure 帐户。

> [!NOTE]
> 如果找不到要链接的工作区，则 Azure 订阅无法访问使用 OMS 门户创建的工作区。  要通过 OMS 门户授予此帐户的访问权限，请参阅[将用户添加到现有工作区](#add-a-user-to-an-existing-workspace)。
>
>

## <a name="upgrade-a-workspace-to-a-paid-plan"></a>将工作区升级为付费计划
有三个用于 OMS 的工作区计划类型：**免费**、**独立**和 **OMS**。  如果使用免费计划，则每天发送到 Log Analytics 的数据大小限制为 500 MB。  如果超量，需要将工作区更改为付费计划，避免无法收集超出此限制的数据。 可以随时更改计划类型。  有关 OMS 定价的详细信息，请参阅[定价详细信息](https://www.microsoft.com/en-us/cloud-platform/operations-management-suite-pricing)。

### <a name="using-entitlements-from-an-oms-subscription"></a>使用 OMS 订阅中的权利
若要使用通过购买用于 System Center 的 OMS E1、OMS E2 OMS 或 OMS 附加设备所获得的权利，请选择 OMS Log Analytics 的 OMS  计划。

购买 OMS 订阅时，相应权利将添加到企业协议。 依据本协议创建的任何 Azure 订阅都可以使用这些权利。 这些订阅的所有工作区都使用 OMS 权利。

若要确保工作区的使用情况适用于 OMS 订阅中的权利，需要：

1. 在 Azure 订阅（包含 OMS 订阅的企业协议的一部分）中创建工作区
2. 为工作区选择 *OMS* 计划

> [!NOTE]
> 如果工作区创建于 2016 年 9 月 26 日之前，并且 Log Analytics 定价计划为*高级*，此工作区将使用用于 System Center 的 OMS 附加设备中的权利。 更改为 *OMS* 定价层也可使用这些权利。
>
>

OMS 订阅权利在 Azure 或 OMS 门户中不可见。 可在企业门户中看到权利和使用情况。  

如果需要更改工作区链接到的 Azure 订阅，可以使用 Azure PowerShell [Move-AzureRmResource](https://msdn.microsoft.com/library/mt652516.aspx) cmdlet。

### <a name="using-azure-commitment-from-an-enterprise-agreement"></a>使用企业协议中的 Azure 承诺
如果没有 OMS 订阅，则需单独为 OMS 的每个组件付费，使用量会显示在 Azure 帐单上。

如果在 Azure 订阅链接到的企业许可登记表上有 Azure 资金承诺，Log Analytics 的使用量都会自动从资金承诺余额抵扣。

如果需要更改工作区链接到的 Azure 订阅，可以使用 Azure PowerShell [Move-AzureRmResource](https://msdn.microsoft.com/library/mt652516.aspx) cmdlet。  

### <a name="change-a-workspace-to-a-paid-pricing-tier-in-the-azure-portal"></a>在 Azure 门户中将工作区更改为付费定价层
1. 登录到 [Azure 门户](http://portal.azure.com)。
2. 浏览到 **Log Analytics**，并选择它。
3. 随即会看到现有工作区列表。 选择工作区。  
4. 在工作区边栏选项卡中“常规”下面，单击“定价层”。  
5. 在“定价层”下面单击选择一个定价层，并单击“选择”。  
    ![选择计划](./media/log-analytics-manage-access/manage-access-change-plan03.png)
6. 刷新 Azure 门户中的视图后，可看到所选层的已更新**定价层**。  
    ![更新的计划](./media/log-analytics-manage-access/manage-access-change-plan04.png)

> [!NOTE]
> 如果工作区关联到自动化帐户，则在选择“单独(按 GB)”定价层之前，必须删除任何“自动化和控制”解决方案，并取消自动化帐户的关联。 在工作区边栏选项卡的“常规”下，单击“解决方案”查看和删除解决方案。 若要取消自动化帐户的关联，请在“定价层”边栏选项卡上单击自动化帐户的名称。
>
>

### <a name="change-a-workspace-to-a-paid-pricing-tier-in-the-oms-portal"></a>在 OMS 门户中将工作区更改为付费定价层

要通过 OMS 门户更改定价层，必须具有 Azure 订阅。

1. 在 OMS 门户中，单击“设置”磁贴。
2. 单击“帐户”选项卡，并单击“Azure 订阅和数据计划”选项卡。
3. 单击要使用的定价层。
4. 单击“ **保存**”。  
   ![订阅和数据计划](./media/log-analytics-manage-access/subscription-tab.png)

新数据计划会显示网页顶部的 OMS 门户功能区。

![OMS 功能区](./media/log-analytics-manage-access/data-plan-changed.png)


## <a name="change-how-long-log-analytics-stores-data"></a>更改 Log Analytics 的数据存储期限

在免费定价层中，Log Analytics 会提供过去 7 天的数据。
在标准定价层中，Log Analytics 会提供过去 30 天的数据。
在高级定价层中，Log Analytics 会提供过去 365 天的数据。
在独立和 OMS 定价层中，Log Analytics 默认会提供过去 31 天的数据。

使用独立定价层和 OMS 定价层时，可将数据保留长达 2 年（730 天）之久。 如果数据的存储超过默认的 31 天，将产生数据保留费。 有关价格详细信息，请参阅[超额费用](https://azure.microsoft.com/pricing/details/log-analytics/)。

若要更改数据保留期长度，请参阅[通过在 Log Analytics 中控制数据量和保留期来管理成本](log-analytics-manage-cost-storage.md)。


## <a name="delete-a-log-analytics-workspace"></a>删除 Log Analytics 工作区
删除 Log Analytics 工作区时，会在 30 天内从 Log Analytics 服务中删除与工作区相关的所有数据。

如果是管理员并且存在多个关联到工作区的用户，则会断开这些用户和该工作区之间的关联。 如果这些用户与其他工作区关联，他们可以继续通过其他工作区使用 Log Analytics。 但是，如果他们未与其他工作区关联，则需要创建工作区才能使用该服务。 若要删除工作区，请参阅[删除 Azure Log Analytics 工作区](log-analytics-manage-del-workspace.md)

## <a name="next-steps"></a>后续步骤
* 请参阅[使用 Log Analytics 从环境中的计算机收集数据](log-analytics-concept-hybrid.md)，以从数据中心或其他云环境中的计算机收集数据。
* 请参阅[收集有关 Azure 虚拟机的数据](log-analytics-quick-collect-azurevm.md)，以配置 Azure VM 的数据收集。  
* [从解决方案库中添加 Log Analytics 解决方案](log-analytics-add-solutions.md)，以添加功能和收集数据。

