---
title: 导出 Azure 活动日志
description: 导出 Azure 活动日志到存储的存档或 Azure 事件中心为 Azure 外部导出。
author: bwren
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 05/20/2019
ms.author: bwren
ms.subservice: logs
ms.openlocfilehash: acf2526e79519e610614dc5217efbfe5e327b90f
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/13/2019
ms.locfileid: "66248139"
---
# <a name="export-azure-activity-log-to-storage-or-azure-event-hubs"></a>将 Azure 活动日志导出到存储或 Azure 事件中心
[Azure 活动日志](activity-logs-overview.md)深入了解发生在 Azure 订阅中的订阅级别事件。 除了在 Azure 门户中查看活动日志，或将其复制到其中进行与 Azure Monitor 收集其他数据分析的 Log Analytics 工作区，您可以创建活动日志存档到 Azure 存储帐户或流式传输到一个日志配置文件 事件中心。

## <a name="archive-activity-log"></a>存档活动日志
存档活动日志到存储帐户是你想要保留超过 90 天 （具有完全控制的保留策略） 进行审核、 静态分析或备份日志数据的情况下很有用。 如果您只需将事件保留 90 天或更少则不需要设置存档到存储帐户，因为活动日志事件在 Azure 平台中保留 90 天。

## <a name="stream-activity-log-to-event-hub"></a>Stream 活动记录到事件中心
[Azure 事件中心](/azure/event-hubs/)是一个流式处理平台和事件引入服务，可以接收的数据和进程数以百万计的每秒事件数。 可以使用任何实时分析提供程序或批处理/存储适配器转换和存储发送到事件中心的数据。 你可能会用于活动日志流式传输功能的两种方法是：
* **流式传输到第三方日志记录和遥测系统**：一段时间后，Azure 事件中心的流式传输就会成为一种机制，用于将活动日志通过管道传输到第三方 SIEM 和日志分析解决方案。
* **生成自定义遥测和日志记录平台**：如果已经有一个自定义生成的遥测平台，或者正想生成一个，则可利用事件中心高度可缩放的发布-订阅功能，灵活地引入活动日志。 

## <a name="prerequisites"></a>必备组件

### <a name="storage-account"></a>存储帐户
如果要存档活动日志，则需要[创建存储帐户](../../storage/common/storage-quickstart-create-account.md)如果还没有一个。 不应使用具有其他、 非监视数据，以便可以更好地控制访问监视数据存储在其中的现有存储帐户。 如果也要存档诊断日志和到存储帐户的指标，尽管您可以选择使用该相同的存储帐户将保留在一个中心位置的所有监视数据。

只要配置设置的用户同时拥有两个订阅的相应 RBAC 访问权限，存储帐户就不必位于发出日志的的订阅中。
> [!NOTE]
>  当前无法将数据存档到受保护虚拟网络后面的存储帐户。

### <a name="event-hubs"></a>事件中心
如果要将活动日志发送到事件中心，则需要[创建事件中心](../../event-hubs/event-hubs-create.md)如果还没有一个。 如果以前对此事件中心命名空间的活动日志事件流式传输，然后将重用该事件中心。

共享访问策略定义了流式传输机制具有的权限。 流式传输到事件中心需要管理、 发送和侦听权限。 可以创建或修改事件中心命名空间在 Azure 门户中配置选项卡下的事件中心命名空间的共享的访问策略。

若要更新活动日志的日志配置文件，包括流式传输，必须在事件中心授权规则拥有 ListKey 权限。 只要配置设置的用户同时拥有两个订阅的相应 RBAC 访问权限并且这两个订阅都在同一个 AAD 租户中，事件中心命名空间就不必与发出日志的订阅位于同一订阅中。

Stream 到事件中心通过活动日志[创建日志配置文件](#create-a-log-profile)。

## <a name="create-a-log-profile"></a>创建日志配置文件
定义如何将 Azure 活动日志是使用导出**日志配置文件**。 每个 Azure 订阅只能有一个日志配置文件。 可通过配置这些设置**导出**在门户中的活动日志边栏选项卡中的选项。 也可以[使用 Azure 监视器 REST API](https://msdn.microsoft.com/library/azure/dn931927.aspx)、PowerShell cmdlet 或 CLI 以编程方式对其进行配置。

日志配置文件定义以下各项。

**应发送活动日志的位置。** 目前，可用选项为存储帐户或事件中心。

**应发送哪些事件类别。** 含义*类别*在日志配置文件和活动日志事件是不同。 在日志配置文件*类别*表示操作类型 （写入、 删除、 操作）。 在活动日志事件，*类别*"* 属性是表示源或事件 （例如，管理、 ServiceHealth，和警报） 的类型。

**应该导出哪些区域 （位置）。** 由于活动日志中的许多事件都是全局事件，则应包含所有位置。

**活动日志应保留多长的存储帐户中。** 保留期为 0 天表示永久保留日志。 如果不需永久保留，则可将该值设置为 1 到 2147483647 之间的任意天数。

如果设置了保留策略，但禁止将日志存储在存储帐户中，保留策略产生任何影响。 保留策略按天应用，因此在一天结束时 (UTC)，会删除当天已超过保留策略期限的日志。 例如，假设保留策略的期限为一天，则在今天开始时，会删除前天的日志。 删除过程从午夜 (UTC) 开始，但请注意，可能最多需要 24 小时才能将日志从存储帐户中删除。



> [!WARNING]
> 存储帐户中日志数据的格式已在 2018 年 11 月 1 日更改为 JSON Lines。 [请参阅此文章来了解此影响，以及如何通过更新工具来处理新格式。](diagnostic-logs-append-blobs.md)
>
>

### <a name="create-log-profile-using-the-azure-portal"></a>创建使用 Azure 门户中的日志配置文件

创建或编辑的日志配置文件**导出到事件中心**Azure 门户中的选项。

1. 从**监视器**在 Azure 门户中，选择菜单**导出到事件中心**。

    ![门户中的“导出”按钮](media/activity-log-export/portal-export.png)

3. 在显示的边栏选项卡，指定以下项：
   * 具有要导出的事件的区域。 应选择所有区域以确保不丢失关键事件，因为活动日志是全局的 （非区域性） 日志，因此大多数事件不具有与其关联的区域。 
   * 如果你想要写入到存储帐户：
       * 要将事件保存存储帐户。
       * 您想要保留这些事件存储中的天数。 设置为 0 天将永久保留日志。
   * 如果你想要写入到事件中心：
       * 服务总线 Namespace 你想要流式传输这些事件创建事件中心。

     ![“导出活动日志”边栏选项卡](./media/activity-logs-overview/activity-logs-portal-export-blade.png)


4. 单击“保存”  保存这些设置。 这些设置将立即应用于你的订阅。


### <a name="configure-log-profile-using-powershell"></a>配置日志配置文件使用 PowerShell

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

如果日志配置文件已存在，首先需要删除现有日志配置文件，然后创建一个新。

1. 使用 `Get-AzLogProfile` 确定日志配置文件是否存在。  如果存在日志配置文件，请注意*名称*属性。

1. 使用 `Remove-AzLogProfile` 通过 *name* 属性的值删除日志配置文件。

    ```powershell
    # For example, if the log profile name is 'default'
    Remove-AzLogProfile -Name "default"
    ```

3. 使用 `Add-AzLogProfile` 创建新的日志配置文件：

    ```powershell
    Add-AzLogProfile -Name my_log_profile -StorageAccountId /subscriptions/s1/resourceGroups/myrg1/providers/Microsoft.Storage/storageAccounts/my_storage -serviceBusRuleId /subscriptions/s1/resourceGroups/Default-ServiceBus-EastUS/providers/Microsoft.ServiceBus/namespaces/mytestSB/authorizationrules/RootManageSharedAccessKey -Location global,westus,eastus -RetentionInDays 90 -Category Write,Delete,Action
    ```

    | 属性 | 需要 | 描述 |
    | --- | --- | --- |
    | Name |是 |日志配置文件的名称。 |
    | StorageAccountId |否 |应在其中保存活动日志的存储帐户的资源 ID。 |
    | serviceBusRuleId |否 |服务总线命名空间（需在其中创建事件中心）的服务总线规则 ID。 这是一个字符串使用格式： `{service bus resource ID}/authorizationrules/{key name}`。 |
    | Location |是 |要为其收集活动日志事件的逗号分隔区域的列表。 |
    | RetentionInDays |是 |事件的保留在存储帐户中，介于 1 和 2147483647 之间天数。 值为零时，将无限期存储日志。 |
    | Category |否 |应收集的事件类别的逗号分隔列表。 可能的值为_编写_，_删除_，并_操作_。 |

### <a name="example-script"></a>示例脚本
下面是示例 PowerShell 脚本以创建将活动日志写入到存储帐户和事件中心的日志配置文件。

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


### <a name="configure-log-profile-using-azure-cli"></a>配置日志配置文件使用 Azure CLI

如果日志配置文件已存在，首先需要删除现有日志配置文件，然后创建新的日志配置文件。

1. 使用 `az monitor log-profiles list` 确定日志配置文件是否存在。
2. 使用 `az monitor log-profiles delete --name "<log profile name>` 通过 *name* 属性的值删除日志配置文件。
3. 使用 `az monitor log-profiles create` 创建新的日志配置文件：

   ```azurecli-interactive
   az monitor log-profiles create --name "default" --location null --locations "global" "eastus" "westus" --categories "Delete" "Write" "Action"  --enabled false --days 0 --service-bus-rule-id "/subscriptions/<YOUR SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.EventHub/namespaces/<EVENT HUB NAME SPACE>/authorizationrules/RootManageSharedAccessKey"
   ```

    | 属性 | 需要 | 描述 |
    | --- | --- | --- |
    | name |是 |日志配置文件的名称。 |
    | storage-account-id |是 |活动日志应保存到的存储帐户的资源 ID。 |
    | locations |是 |要为其收集活动日志事件的空格分隔区域列表。 可以使用 `az account list-locations --query [].name` 查看订阅的所有区域列表。 |
    | days |是 |事件的保留天数，介于 1 到 365 之间。 值为零时，将无限期（永久）存储日志。  如果为零，则启用的参数应设置为 true。 |
    |enabled | 是 |True 或 False。  用于启用或禁用保留策略。  如果为 True，则 days 参数必须为大于 0 的值。
    | categories |是 |应收集的事件类别的空格分隔列表。 可能值包括：Write、Delete 和 Action。 |



## <a name="activity-log-schema"></a>活动日志架构
是否发送到 Azure 存储或事件中心，则活动日志数据将写入到 JSON 中使用以下格式。

``` JSON
{
    "records": [
        {
            "time": "2015-01-21T22:14:26.9792776Z",
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
                    "http://schemas.microsoft.com/identity/claims/tenantid": "1e8d8218-c5e7-4578-9acc-9abbd5d23315 ",
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
此 JSON 中的元素是下表中所述。

| 元素名称 | 描述 |
| --- | --- |
| time |处理与事件对应的请求的 Azure 服务生成事件时的时间戳。 |
| resourceId |受影响资源的资源 ID。 |
| operationName |操作的名称。 |
| category |操作的类别，例如 写入、读取和操作。 |
| resultType |结果的类型，例如 成功、失败和启动 |
| resultSignature |取决于资源类型。 |
| durationMs |操作持续时间，以毫秒为单位 |
| callerIpAddress |执行操作（UPN 声明或 SPN 声明，具体取决于可用性）的用户的 IP 地址。 |
| correlationId |通常为字符串格式的 GUID。 共享 correlationId 的事件属于同一 uber 操作。 |
| identity |描述授权和声明的 JSON blob。 |
| authorization |包含事件的 RBAC 属性的 Blob。 通常包括“action”、“role”和“scope”属性。 |
| level |事件的级别。 以下值之一：_关键_，_错误_，_警告_，_条信息性_，和_详细_ |
| location |位置所在的区域（或全局）。 |
| properties |`<Key, Value>` 对集合（即字典），描述事件的详细信息。 |

> [!NOTE]
> 属性和使用情况的这些属性可以资源而异。



## <a name="next-steps"></a>后续步骤

* [了解有关活动日志的更多信息](../../azure-resource-manager/resource-group-audit.md)
* [将活动日志收集到 Azure Monitor 日志](activity-log-collect.md)
