---
title: Azure 活动日志
description: 查看 Azure 活动日志，并将其发送到 Azure Monitor 日志、Azure 事件中心和 Azure 存储。
author: bwren
services: azure-monitor
ms.topic: conceptual
ms.date: 06/12/2020
ms.author: bwren
ms.subservice: logs
ms.openlocfilehash: e6fb2f09200e42f7ad7781716bb83ab418134509
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/20/2020
ms.locfileid: "86516135"
---
# <a name="azure-activity-log"></a>Azure 活动日志
活动日志是 Azure 中的一个[平台日志](platform-logs-overview.md)，可用于深入了解订阅级事件。 这包括何时修改了资源或何时启动了虚拟机等信息。 可以在 Azure 门户中查看活动日志，或在 PowerShell 和 CLI 中检索条目。 若要获得其他功能，应创建诊断设置，以便将活动日志发送到[Azure Monitor 日志](data-platform-logs.md)、Azure 事件中心转发到 azure 外部或用于存档的 azure 存储。 本文详细介绍了如何查看活动日志，以及如何将其发送到不同的目标。

有关创建诊断设置的详细信息，请参阅[创建诊断设置以将平台日志和指标发送到不同的目标](diagnostic-settings.md)。

> [!NOTE]
> 活动日志中的条目是系统生成的，不能更改或删除。

## <a name="view-the-activity-log"></a>查看活动日志
可以从 Azure 门户中的大多数菜单访问活动日志。 从中打开它的菜单确定了其初始筛选器。 如果从 "**监视**" 菜单中打开该筛选器，则订阅上将只有唯一的筛选器。 如果从资源菜单中打开该筛选器，则筛选器将设置为该资源。 您始终可以更改筛选器以查看所有其他条目。 单击 "**添加筛选**器" 将其他属性添加到筛选器。

![查看活动日志](./media/activity-logs-overview/view-activity-log.png)

有关活动日志类别的说明，请参阅[Azure 活动日志事件架构](activity-log-schema.md#categories)。

### <a name="view-change-history"></a>查看更改历史记录

对于某些事件，你可以查看更改历史记录，其中显示了在该事件时间内发生了哪些更改。 从活动日志中选择要深入了解的事件。 选择“更改历史记录（预览）”选项卡以查看与该事件相关的任何更改。

![更改事件的历史记录列表](media/activity-logs-overview/change-history-event.png)

如果有与事件关联的任何更改，你将看到可选择的更改列表。 此时将打开“更改历史记录（预览）”页面。 在此页上，可以看到对资源所做的更改。 在下面的示例中，你不仅可以看到 VM 更改了大小，还可以查看在更改之前以前的 VM 大小以及更改的内容。 若要了解有关更改历史记录的详细信息，请参阅[获取资源更改](../../governance/resource-graph/how-to/get-resource-changes.md)。

![显示差异的更改历史记录页](media/activity-logs-overview/change-history-event-details.png)


### <a name="other-methods-to-retrieve-activity-log-events"></a>检索活动日志事件的其他方法
你还可以使用以下方法来访问活动日志事件。

- 使用 [Get-AzLog](/powershell/module/az.monitor/get-azlog) cmdlet 从 PowerShell 中检索活动日志。 请参阅[Azure Monitor PowerShell 示例](../samples/powershell-samples.md#retrieve-activity-log)。
- 使用 [az 监视活动-日志](/cli/azure/monitor/activity-log)从 CLI 中检索活动日志。  请参阅 [Azure 监视器 CLI 示例](../samples/cli-samples.md#view-activity-log)。
- 使用 [Azure Monitor REST API](/rest/api/monitor/) 从 REST 客户端检索活动日志。 


## <a name="send-to-log-analytics-workspace"></a>发送到 Log Analytics 工作区
 将活动日志发送到 Log Analytics 工作区，以启用[Azure Monitor 日志](data-platform-logs.md)的功能，其中包括以下内容：

- 使活动日志数据与 Azure Monitor 收集的其他监视数据产生关联。
- 将来自多个 Azure 订阅和租户的活动日志合并到同一位置一起进行分析。
- 使用日志查询来执行复杂分析，并深入了解活动日志条目。
- 将日志警报与活动条目配合使用，从而可以使用更复杂的警报逻辑。
- 将活动日志条目存储 90 天以上。
- Log Analytics 工作区中存储的活动日志数据不产生数据引入或数据保留费用。

创建用于将活动日志发送到 Log Analytics 工作区的[诊断设置](diagnostic-settings.md)。 可以将任何单个订阅中的活动日志发送到最多五个工作区。 跨租户收集日志需要[Azure Lighthouse](../../lighthouse/index.yml)。

Log Analytics 工作区中的活动日志数据存储在名为*AzureActivity*的表中，您可以使用[Log Analytics](../log-query/get-started-portal.md)中的[日志查询](../log-query/log-query-overview.md)来检索。 此表的结构因[日志条目类别](activity-log-schema.md)而异。 有关表属性的说明，请参阅[Azure Monitor 数据参考](/azure/azure-monitor/reference/tables/azureactivity)。

例如，若要查看每个类别的活动日志记录计数，请使用以下查询。

```kusto
AzureActivity
| summarize count() by Category
```

若要检索 "管理" 类别中的所有记录，请使用以下查询。

```kusto
AzureActivity
| where Category == "Administrative"
```


## <a name="send-to-azure-event-hubs"></a>发送到 Azure 事件中心
将活动日志发送到 Azure 事件中心，以在 Azure 外部发送条目，例如，发送到第三方 SIEM 或其他 Log analytics 解决方案。 事件中心的活动日志事件以 JSON 格式使用，其中 `records` 元素包含每个有效负载中的记录。 架构依赖于类别，并在[存储帐户和事件中心的架构](activity-log-schema.md)中进行了介绍。

下面是活动日志的事件中心的示例输出数据：

``` JSON
{
    "records": [
        {
            "time": "2019-01-21T22:14:26.9792776Z",
            "resourceId": "/subscriptions/s1/resourceGroups/MSSupportGroup/providers/microsoft.support/supporttickets/115012112305841",
            "operationName": "microsoft.support/supporttickets/write",
            "category": "Write",
            "resultType": "Success",
            "resultSignature": "Succeeded.Created",
            "durationMs": 2826,
            "callerIpAddress": "111.111.111.11",
            "correlationId": "c776f9f4-36e5-4e0e-809b-c9b3c3fb62a8",
            "identity": {
                "authorization": {
                    "scope": "/subscriptions/s1/resourceGroups/MSSupportGroup/providers/microsoft.support/supporttickets/115012112305841",
                    "action": "microsoft.support/supporttickets/write",
                    "evidence": {
                        "role": "Subscription Admin"
                    }
                },
                "claims": {
                    "aud": "https://management.core.windows.net/",
                    "iss": "https://sts.windows.net/72f988bf-86f1-41af-91ab-2d7cd011db47/",
                    "iat": "1421876371",
                    "nbf": "1421876371",
                    "exp": "1421880271",
                    "ver": "1.0",
                    "http://schemas.microsoft.com/identity/claims/tenantid": "00000000-0000-0000-0000-000000000000",
                    "http://schemas.microsoft.com/claims/authnmethodsreferences": "pwd",
                    "http://schemas.microsoft.com/identity/claims/objectidentifier": "2468adf0-8211-44e3-95xq-85137af64708",
                    "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/upn": "admin@contoso.com",
                    "puid": "20030000801A118C",
                    "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/nameidentifier": "9vckmEGF7zDKk1YzIY8k0t1_EAPaXoeHyPRn6f413zM",
                    "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname": "John",
                    "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname": "Smith",
                    "name": "John Smith",
                    "groups": "cacfe77c-e058-4712-83qw-f9b08849fd60,7f71d11d-4c41-4b23-99d2-d32ce7aa621c,31522864-0578-4ea0-9gdc-e66cc564d18c",
                    "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name": " admin@contoso.com",
                    "appid": "c44b4083-3bq0-49c1-b47d-974e53cbdf3c",
                    "appidacr": "2",
                    "http://schemas.microsoft.com/identity/claims/scope": "user_impersonation",
                    "http://schemas.microsoft.com/claims/authnclassreference": "1"
                }
            },
            "level": "Information",
            "location": "global",
            "properties": {
                "statusCode": "Created",
                "serviceRequestId": "50d5cddb-8ca0-47ad-9b80-6cde2207f97c"
            }
        }
    ]
}
```


## <a name="send-to--azure-storage"></a>发送到 Azure 存储
如果要将日志数据保留超过90天以用于审核、静态分析或备份，请将活动日志发送到 Azure 存储帐户。 如果只需将事件保留 90 天或更短的时间，则无需设置为存档到存储帐户，因为活动日志事件保留在 Azure 平台中的时间是 90 天。

将活动日志发送到 Azure 时，一旦发生事件，就会在存储帐户中创建一个存储容器。 容器中的 blob 使用以下命名约定：

```
insights-activity-logs/resourceId=/SUBSCRIPTIONS/{subscription ID}/y={four-digit numeric year}/m={two-digit numeric month}/d={two-digit numeric day}/h={two-digit 24-hour clock hour}/m=00/PT1H.json
```

例如，特定的 blob 的名称可能类似于以下内容：

```
insights-logs-networksecuritygrouprulecounter/resourceId=/SUBSCRIPTIONS/00000000-0000-0000-0000-000000000000/y=2020/m=06/d=08/h=18/m=00/PT1H.json
```

每个 PT1H.json blob 都包含一个 JSON blob，其中的事件为在 blob URL 中指定的小时（例如 h=12）内发生的。 在当前的小时内发生的事件将附加到 PT1H.json 文件。 分钟值始终为 00 (m=00)，因为资源日志事件按小时细分成单个 blob。

每个事件都存储在具有以下格式的文件中的 PT1H.js：使用通用顶级架构，但对于每个类别都是唯一的，如[活动日志架构](activity-log-schema.md)中所述。

``` JSON
{ "time": "2020-06-12T13:07:46.766Z", "resourceId": "/SUBSCRIPTIONS/00000000-0000-0000-0000-000000000000/RESOURCEGROUPS/MY-RESOURCE-GROUP/PROVIDERS/MICROSOFT.COMPUTE/VIRTUALMACHINES/MV-VM-01", "correlationId": "0f0cb6b4-804b-4129-b893-70aeeb63997e", "operationName": "Microsoft.Resourcehealth/healthevent/Updated/action", "level": "Information", "resultType": "Updated", "category": "ResourceHealth", "properties": {"eventCategory":"ResourceHealth","eventProperties":{"title":"This virtual machine is starting as requested by an authorized user or process. It will be online shortly.","details":"VirtualMachineStartInitiatedByControlPlane","currentHealthStatus":"Unknown","previousHealthStatus":"Unknown","type":"Downtime","cause":"UserInitiated"}}}
```


## <a name="legacy-collection-methods"></a>旧收集方法
本部分介绍用于收集诊断设置之前使用的活动日志的旧方法。 如果使用这些方法，应考虑转换为诊断设置，以便为资源日志提供更好的功能和一致性。

### <a name="log-profiles"></a>日志配置文件
日志配置文件是用于将活动日志发送到 Azure 存储或事件中心的旧版方法。 请使用以下过程，以继续使用日志配置文件或将其禁用（如果准备迁移到诊断设置）。

1. 从 Azure 门户上的 Azure Monitor 菜单中，选择“活动日志” 。
3. 单击“诊断设置”。

   ![诊断设置](media/diagnostic-settings-subscription/diagnostic-settings.png)

4. 单击紫色横幅了解旧版体验。

    ![旧版体验](media/diagnostic-settings-subscription/legacy-experience.png)



### <a name="configure-log-profile-using-powershell"></a>使用 PowerShell 配置日志配置文件

如果日志配置文件已存在，首先需要删除现有日志配置文件，然后创建新的日志配置文件。

1. 使用 `Get-AzLogProfile` 确定日志配置文件是否存在。  如果存在日志配置文件，请记下 *name* 属性。

1. 使用 `Remove-AzLogProfile` 通过 *name* 属性的值删除日志配置文件。

    ```powershell
    # For example, if the log profile name is 'default'
    Remove-AzLogProfile -Name "default"
    ```

3. 使用 `Add-AzLogProfile` 创建新的日志配置文件：

    ```powershell
    Add-AzLogProfile -Name my_log_profile -StorageAccountId /subscriptions/s1/resourceGroups/myrg1/providers/Microsoft.Storage/storageAccounts/my_storage -serviceBusRuleId /subscriptions/s1/resourceGroups/Default-ServiceBus-EastUS/providers/Microsoft.ServiceBus/namespaces/mytestSB/authorizationrules/RootManageSharedAccessKey -Location global,westus,eastus -RetentionInDays 90 -Category Write,Delete,Action
    ```

    | properties | 必须 | 说明 |
    | --- | --- | --- |
    | 名称 |是 |日志配置文件的名称。 |
    | StorageAccountId |否 |应该将活动日志保存到其中的存储帐户的资源 ID。 |
    | serviceBusRuleId |否 |服务总线命名空间（需在其中创建事件中心）的服务总线规则 ID。 这是采用以下格式的字符串：`{service bus resource ID}/authorizationrules/{key name}`。 |
    | 位置 |是 |要为其收集活动日志事件的逗号分隔区域的列表。 |
    | RetentionInDays |是 |事件应在存储帐户中保留的天数，介于 1 和 365 之间。 值为零时，将无限期存储日志。 |
    | 类别 |否 |应收集的事件类别的逗号分隔列表。 可能的值包括 "_写入_"、"_删除_" 和 "_操作_"。 |

### <a name="example-script"></a>示例脚本
以下示例 PowerShell 脚本创建一个日志配置文件，用于将活动日志写入到存储帐户和事件中心。

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

    | properties | 必须 | 说明 |
    | --- | --- | --- |
    | name |是 |日志配置文件的名称。 |
    | storage-account-id |是 |活动日志应保存到的存储帐户的资源 ID。 |
    | locations |是 |要为其收集活动日志事件的空格分隔区域列表。 可以使用 `az account list-locations --query [].name` 查看订阅的所有区域列表。 |
    | days |是 |活动的保留天数，介于 1 到 365 之间。 值为零时，将无限期（永久）存储日志。  如果为零，则启用的参数应设置为 false。 |
    |已启用 | 是 |判断正误。  用于启用或禁用保留策略。  如果为 True，则 days 参数必须为大于 0 的值。
    | categories |是 |应收集的事件类别的空格分隔列表。 可能值包括：Write、Delete 和 Action。 |


### <a name="log-analytics-workspace"></a>Log Analytics 工作区
将活动日志发送到 Log Analytics 工作区的传统方法是在工作区配置中连接日志。 

1. 在 Azure 门户的“Log Analytics 工作区”菜单中，选择要收集活动日志的工作区。
1. 在工作区的菜单的“工作区数据源”部分，选择“Azure 活动日志”。 
1. 单击要连接的订阅。

    ![工作区](media/activity-log-collect/workspaces.png)

1. 单击“连接”，将订阅中的活动日志连接到所选工作区。 如果订阅已连接到其他工作区，请先单击“断开连接”将其断开连接。

    ![连接工作区](media/activity-log-collect/connect-workspace.png)


若要禁用该设置，请执行相同步骤，然后单击“断开连接”，以从工作区中删除该订阅。

### <a name="data-structure-changes"></a>数据结构更改
诊断设置发送的数据与用于将活动日志发送到*AzureActivity*表的结构的某些更改的旧方法相同。

更新后的架构中已经弃用了下表中的列。 这些列仍存在于 AzureActivity 中，但不会包含任何数据。 这些列的替换项并不是新的，而是包含了与已弃用列相同的数据。 它们采用了一种不同的格式，所以你可能需要对使用它们的日志查询进行修改。 

| 已弃用的列 | 替换列 |
|:---|:---|
| ActivityStatus    | ActivityStatusValue    |
| ActivitySubstatus | ActivitySubstatusValue |
| OperationName     | OperationNameValue     |
| ResourceProvider  | ResourceProviderValue  |

> [!IMPORTANT]
> 在有些情况下，这些列中的值可能全部都是大写。 如果你的某个查询包含这些列，应使用 [= ~ 运算符](/azure/kusto/query/datatypes-string-operators)来执行不区分大小写的比较。

以下列已添加到更新后架构的 AzureActivity 中：

- Authorization_d
- Claims_d
- Properties_d

## <a name="activity-log-analytics-monitoring-solution"></a>Activity Log Analytics 监视解决方案
Azure Log Analytics 监视解决方案不久就会被弃用，并将替换为一个使用 Log Analytics 工作区中更新后架构的工作簿。 如果已经启用了该解决方案，则仍可继续使用它，但它只能在你使用旧版设置收集活动日志时使用。 



### <a name="use-the-solution"></a>使用解决方案
可以在 Azure 门户的“监视器”菜单中访问监视解决方案。 在“见解”部分选择“更多”，打开包含解决方案磁贴的“概览”页  。 “Azure 活动日志”磁贴显示工作区中 **AzureActivity** 记录的计数。

![Azure 活动日志磁贴](media/collect-activity-logs/azure-activity-logs-tile.png)


单击“Azure 活动日志”磁贴，打开“Azure 活动日志”视图。 视图包含下表中的可视化部件。 每个部件按照指定时间范围列出了匹配该部件条件的最多 10 个项。 可通过单击部件底部的“查看全部”运行返回所有匹配记录的日志查询。

![Azure 活动日志仪表板](media/collect-activity-logs/activity-log-dash.png)


### <a name="enable-the-solution-for-new-subscriptions"></a>为新订阅启用该解决方案
不久之后，你将无法再使用 Azure 门户将 Activity Logs Analytics 解决方案添加到你的订阅。 您可以使用以下过程和资源管理器模板来添加它。 

1. 将以下 json 复制到名为 ActivityLogTemplate.json 的文件中。

    ```json
    {
    "$schema": "https://schema.management.azure.com/schemas/2014-04-01-preview/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "workspaceName": {
            "type": "String",
            "defaultValue": "my-workspace",
            "metadata": {
              "description": "Specifies the name of the workspace."
            }
        },
        "location": {
            "type": "String",
            "allowedValues": [
              "east us",
              "west us",
              "australia central",
              "west europe"
            ],
            "defaultValue": "australia central",
            "metadata": {
              "description": "Specifies the location in which to create the workspace."
            }
        }
      },
        "resources": [
        {
            "type": "Microsoft.OperationalInsights/workspaces",
            "name": "[parameters('workspaceName')]",
            "apiVersion": "2015-11-01-preview",
            "location": "[parameters('location')]",
            "properties": {
                "features": {
                    "searchVersion": 2
                }
            }
        },
        {
            "type": "Microsoft.OperationsManagement/solutions",
            "apiVersion": "2015-11-01-preview",
            "name": "[concat('AzureActivity(', parameters('workspaceName'),')')]",
            "location": "[parameters('location')]",
            "dependsOn": [
                "[resourceId('microsoft.operationalinsights/workspaces', parameters('workspaceName'))]"
            ],
            "plan": {
                "name": "[concat('AzureActivity(', parameters('workspaceName'),')')]",
                "promotionCode": "",
                "product": "OMSGallery/AzureActivity",
                "publisher": "Microsoft"
            },
            "properties": {
                "workspaceResourceId": "[resourceId('microsoft.operationalinsights/workspaces', parameters('workspaceName'))]",
                "containedResources": [
                    "[concat(resourceId('microsoft.operationalinsights/workspaces', parameters('workspaceName')), '/views/AzureActivity(',parameters('workspaceName'))]"
                ]
            }
        },
        {
          "type": "Microsoft.OperationalInsights/workspaces/datasources",
          "kind": "AzureActivityLog",
          "name": "[concat(parameters('workspaceName'), '/', subscription().subscriptionId)]",
          "apiVersion": "2015-11-01-preview",
          "location": "[parameters('location')]",
          "dependsOn": [
              "[parameters('WorkspaceName')]"
          ],
          "properties": {
              "linkedResourceId": "[concat(subscription().Id, '/providers/microsoft.insights/eventTypes/management')]"
          }
        }
      ]
    }    
    ```

2. 使用以下 PowerShell 命令部署该模板：

    ```PowerShell
    Connect-AzAccount
    Select-AzSubscription <SubscriptionName>
    New-AzResourceGroupDeployment -Name activitysolution -ResourceGroupName <ResourceGroup> -TemplateFile <Path to template file>
    ```



## <a name="next-steps"></a>后续步骤

* [阅读平台日志的概述](platform-logs-overview.md)
* [创建诊断设置以将活动日志发送到其他目标](diagnostic-settings.md)
