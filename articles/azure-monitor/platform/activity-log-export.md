---
title: 导出 Azure 活动日志
description: 将 Azure 活动日志导出到存储，以便存档或 Azure 事件中心导出到 Azure 之外。
author: bwren
services: azure-monitor
ms.topic: conceptual
ms.date: 01/23/2020
ms.author: bwren
ms.subservice: logs
ms.openlocfilehash: edaa585ffb3448a80b021aa924a9d654ac829931
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/05/2020
ms.locfileid: "78379529"
---
# <a name="export-azure-activity-log-to-storage-or-azure-event-hubs"></a>将 Azure 活动日志导出到存储或 Azure 事件中心

> [!IMPORTANT]
> 用于将 Azure 活动日志发送到 Azure 存储和 Azure 事件中心的方法已更改为[诊断设置](diagnostic-settings.md)。 本文介绍不推荐使用的旧方法。 请参阅更新到[Azure 活动日志集合和导出](diagnostic-settings-legacy.md)进行比较。


[Azure 活动日志](platform-logs-overview.md)提供对 Azure 订阅中发生的订阅级别事件的见解。 除了查看 Azure 门户中的活动日志，或者将其复制到 Log Analytics 工作区（其中可以使用 Azure Monitor 收集的其他数据进行分析），可以创建日志配置文件，将活动日志存档到 Azure 存储帐户，或将其流式传输到 事件中心。

## <a name="archive-activity-log"></a>存档活动日志
如果要将日志数据保留超过90天（对保留策略具有完全控制），以进行审核、静态分析或备份，将活动日志存档到存储帐户非常有用。 如果只需将事件保留90天或更短的时间，则无需设置到存储帐户的存档，因为活动日志事件保留在 Azure 平台90天内。

## <a name="stream-activity-log-to-event-hub"></a>将活动日志流式传输到事件中心
[Azure 事件中心](/azure/event-hubs/)是一种数据流式处理平台和事件引入服务，每秒可接收和处理数百万事件。 可以使用任何实时分析提供程序或批处理/存储适配器转换和存储发送到事件中心的数据。 可以通过两种方式将流式传输功能用于活动日志：
* **流式传输到第三方日志记录和遥测系统**：一段时间后，Azure 事件中心的流式传输就会成为一种机制，用于将活动日志通过管道传输到第三方 SIEM 和 Log Analytics 解决方案。
* **生成自定义遥测和日志记录平台**：如果已经有一个自定义生成的遥测平台，或者正想生成一个，则可利用事件中心高度可缩放的发布-订阅功能，灵活地引入活动日志。

## <a name="prerequisites"></a>先决条件

### <a name="storage-account"></a>存储帐户
如果要存档活动日志，则需要[创建一个存储帐户](../../storage/common/storage-account-create.md)（如果尚未安装）。 不应使用存储在其中的其他非监视数据的现有存储帐户，以便您可以更好地控制对监视数据的访问。 不过，如果还将日志和指标存档到存储帐户，则可以选择使用同一个存储帐户将所有监视数据都保存在一个中央位置。

只要配置设置的用户同时拥有两个订阅的相应 RBAC 访问权限，存储帐户就不必位于发出日志的的订阅中。 

> [!TIP]
> 请参阅[配置 Azure 存储防火墙和虚拟网络](https://docs.microsoft.com/azure/storage/common/storage-network-security#exceptions)，以提供对受保护的虚拟网络后面的存储帐户的访问权限。

### <a name="event-hubs"></a>事件中心
如果要将活动日志发送到事件中心，则需要[创建一个事件中心](../../event-hubs/event-hubs-create.md)（如果还没有）。 如果以前已将活动日志事件流式传输到此事件中心命名空间，则会重复使用该事件中心。

共享访问策略定义了流式传输机制具有的权限。 流式传输到事件中心需要“管理”、“发送”和“侦听”权限。 可以在事件中心命名空间的 "配置" 选项卡下的 Azure 门户中创建或修改事件中心命名空间的共享访问策略。

若要更新活动日志的日志配置文件以包含流式处理，必须对该事件中心授权规则具有 ListKey 权限。 只要配置设置的用户同时拥有两个订阅的相应 RBAC 访问权限并且这两个订阅都在同一个 AAD 租户中，事件中心命名空间就不必与发出日志的订阅位于同一订阅中。

通过[创建日志配置文件](#create-a-log-profile)，将活动日志流式传输到事件中心。

## <a name="create-a-log-profile"></a>创建日志配置文件
定义如何使用**日志配置文件**导出 Azure 活动日志。 每个 Azure 订阅只能有一个日志配置文件。 可以通过门户中的 "活动日志" 边栏选项卡中的 "**导出**" 选项配置这些设置。 也可以[使用 Azure 监视器 REST API](https://msdn.microsoft.com/library/azure/dn931927.aspx)、PowerShell cmdlet 或 CLI 以编程方式对其进行配置。

日志配置文件定义以下各项。

**应将活动日志发送到的位置。** 目前可用的选项为存储帐户或事件中心。

**应发送哪些事件类别。** 日志配置文件和活动日志事件中的*类别*含义不同。 在日志配置文件中，"*类别*" 表示操作类型（写入、删除、操作）。 在活动日志事件中，"*类别*" 属性表示事件的来源或类型（例如，"管理"、"ServiceHealth" 和 "警报"）。

**应导出哪些区域（位置）。** 你应包括所有位置，因为活动日志中的许多事件都是全局事件。

**应将活动日志保留在存储帐户中的时间长度。** 保留期为 0 天表示永久保留日志。 否则，该值可以是介于1到365之间的任意天数。

如果设置了保留策略，但禁止将日志存储在存储帐户中，则保留策略无效。 保留策略按天应用，因此在一天结束时 (UTC)，会删除当天已超过保留策略期限的日志。 例如，假设保留策略的期限为一天，则在今天开始时，会删除前天的日志。 删除过程从午夜 (UTC) 开始，但请注意，可能最多需要 24 小时才能将日志从存储帐户中删除。


> [!IMPORTANT]
> 如果未注册 Microsoft Insights 资源提供程序，则在创建日志配置文件时可能会收到错误。 若要注册此提供程序，请参阅[Azure 资源提供程序和类型](../../azure-resource-manager/management/resource-providers-and-types.md)。


### <a name="create-log-profile-using-the-azure-portal"></a>使用 Azure 门户创建日志配置文件

在 Azure 门户中使用 "**导出到事件中心**" 选项创建或编辑日志配置文件。

1. 从 Azure 门户的 " **Azure Monitor** " 菜单中，选择 "**活动日志**"。
3. 单击“诊断设置”。

   ![诊断设置](media/diagnostic-settings-subscription/diagnostic-settings.png)

4. 单击紫色横幅了解旧体验。

    ![旧体验](media/diagnostic-settings-subscription/legacy-experience.png)

3. 在出现的边栏选项卡中，指定以下内容：
   * 包含要导出的事件的区域。 你应选择 "所有区域"，以确保不会错过关键事件，因为活动日志是一个全局（非区域）日志，因此大多数事件都没有关联的区域。
   * 如果要写入存储帐户，请执行以下操作：
       * 要将事件保存到的存储帐户。
       * 要在存储中保留这些事件的天数。 设置为 0 天将永久保留日志。
   * 如果要写入事件中心，请执行以下操作：
       * 要在其中创建事件中心来流式传输这些事件的服务总线命名空间。

     ![“导出活动日志”边栏选项卡](./media/activity-logs-overview/activity-logs-portal-export-blade.png)


4. 单击“保存”保存这些设置。 这些设置会即时应用到订阅。


### <a name="configure-log-profile-using-powershell"></a>使用 PowerShell 配置日志配置文件

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

如果日志配置文件已存在，首先需要删除现有的日志配置文件，然后再创建一个新的日志配置文件。

1. 使用 `Get-AzLogProfile` 确定日志配置文件是否存在。  如果日志配置文件已存在，请记下*name*属性。

1. 使用 `Remove-AzLogProfile` 通过 *name* 属性的值删除日志配置文件。

    ```powershell
    # For example, if the log profile name is 'default'
    Remove-AzLogProfile -Name "default"
    ```

3. 使用 `Add-AzLogProfile` 创建新的日志配置文件：

    ```powershell
    Add-AzLogProfile -Name my_log_profile -StorageAccountId /subscriptions/s1/resourceGroups/myrg1/providers/Microsoft.Storage/storageAccounts/my_storage -serviceBusRuleId /subscriptions/s1/resourceGroups/Default-ServiceBus-EastUS/providers/Microsoft.ServiceBus/namespaces/mytestSB/authorizationrules/RootManageSharedAccessKey -Location global,westus,eastus -RetentionInDays 90 -Category Write,Delete,Action
    ```

    | 属性 | 必需 | 说明 |
    | --- | --- | --- |
    | 名称 |是 |日志配置文件的名称。 |
    | StorageAccountId |是 |应将活动日志保存到其中的存储帐户的资源 ID。 |
    | serviceBusRuleId |是 |服务总线命名空间（需在其中创建事件中心）的服务总线规则 ID。 这是一个字符串，格式为： `{service bus resource ID}/authorizationrules/{key name}`。 |
    | Location |是 |要为其收集活动日志事件的逗号分隔区域的列表。 |
    | RetentionInDays |是 |事件在存储帐户中的保留天数，介于1到365之间。 值为零时，将无限期存储日志。 |
    | 类别 |是 |应收集的事件类别的逗号分隔列表。 可能的值包括 "_写入_"、"_删除_" 和 "_操作_"。 |

### <a name="example-script"></a>示例脚本
下面是一个示例 PowerShell 脚本，用于创建将活动日志写入存储帐户和事件中心的日志配置文件。

   ```powershell
   # Settings needed for the new log profile
   $logProfileName = "default"
   $locations = (Get-AzLocation).Location
   $locations += "global"
   $subscriptionId = "<your Azure subscription Id>"
   $resourceGroupName = "<resource group name your event hub belongs to>"
   $eventHubNamespace = "<event hub namespace>"

   # Build the service bus rule Id from the settings above
   $serviceBusRuleId = "/subscriptions/$subscriptionId/resourceGroups/$resourceGroupName/providers/Microsoft.EventHub/namespaces/$eventHubNamespace/authorizationrules/RootManageSharedAccessKey"

   # Build the storage account Id from the settings above
   $storageAccountId = "/subscriptions/$subscriptionId/resourceGroups/$resourceGroupName/providers/Microsoft.Storage/storageAccounts/$storageAccountName"

   Add-AzLogProfile -Name $logProfileName -Location $locations -ServiceBusRuleId $serviceBusRuleId
   ```


### <a name="configure-log-profile-using-azure-cli"></a>使用 Azure CLI 配置日志配置文件

如果日志配置文件已存在，首先需要删除现有日志配置文件，然后创建新的日志配置文件。

1. 使用 `az monitor log-profiles list` 确定日志配置文件是否存在。
2. 使用 `az monitor log-profiles delete --name "<log profile name>` 通过 *name* 属性的值删除日志配置文件。
3. 使用 `az monitor log-profiles create` 创建新的日志配置文件：

   ```azurecli-interactive
   az monitor log-profiles create --name "default" --location null --locations "global" "eastus" "westus" --categories "Delete" "Write" "Action"  --enabled false --days 0 --service-bus-rule-id "/subscriptions/<YOUR SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.EventHub/namespaces/<EVENT HUB NAME SPACE>/authorizationrules/RootManageSharedAccessKey"
   ```

    | 属性 | 必需 | 说明 |
    | --- | --- | --- |
    | name |是 |日志配置文件的名称。 |
    | storage-account-id |是 |活动日志应保存到的存储帐户的资源 ID。 |
    | 位置 |是 |要为其收集活动日志事件的空格分隔区域列表。 可以使用 `az account list-locations --query [].name` 查看订阅的所有区域列表。 |
    | 天 |是 |事件应保留的天数，介于1到365之间。 值为零时，将无限期（永久）存储日志。  如果为零，则 enabled 参数应设置为 false。 |
    |enabled | 是 |True 或 False。  用于启用或禁用保留策略。  如果为 True，则 days 参数必须为大于 0 的值。
    | 类别 |是 |应收集的事件类别的空格分隔列表。 可能值包括：Write、Delete 和 Action。 |



## <a name="next-steps"></a>后续步骤

* [了解有关活动日志的更多信息](../../azure-resource-manager/management/view-activity-logs.md)
* [将活动日志收集到 Azure Monitor 日志中](activity-log-collect.md)
