---
title: 存档 Azure 活动日志
description: 存档 Azure 活动日志，将其长期保留在存储帐户中。
author: johnkemnetz
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 06/07/2018
ms.author: johnkem
ms.component: activitylog
ms.openlocfilehash: a519cd242b88916d1a11df47c0b7450594848ef5
ms.sourcegitcommit: a06c4177068aafc8387ddcd54e3071099faf659d
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/09/2018
ms.locfileid: "37920543"
---
# <a name="archive-the-azure-activity-log"></a>存档 Azure 活动日志
本文介绍如何使用 Azure 门户、PowerShell Cmdlet 或跨平台 CLI 将 [**Azure 活动日志**](monitoring-overview-activity-logs.md)存档到存储帐户中。 此选项适用于对保留时长超过 90 天的活动日志进行审核、静态分析或备份（对保留策略具备完全控制权限）。 如果只需将事件保留 90 天或更短的时间，则不需设置到存储帐户的存档，因为在不启用存档的情况下，活动日志事件保留在 Azure 平台中的时间是 90 天。

> [!WARNING]
> 存储帐户中日志数据的格式将在 2018 年 11 月 1 日更改为 JSON Lines。 [请参阅此文章来了解此影响，以及如何通过更新工具来处理新格式。](./monitor-diagnostic-logs-append-blobs.md) 
>
> 

## <a name="prerequisites"></a>先决条件
在开始之前，需要[创建存储帐户](../storage/common/storage-create-storage-account.md#create-a-storage-account)，将活动日志存档到其中。 强烈建议用户不要使用其中存储了其他非监视数据的现有存储帐户，以便更好地控制监视数据所需的访问权限。 但是，如果还要将诊断日志和指标存档到存储帐户，则也可将该存储帐户用于活动日志，使得所有监视数据都位于一个中心位置。 只要配置设置的用户同时拥有两个订阅的相应 RBAC 访问权限，存储帐户就不必与订阅发出日志位于同一订阅中。

> [!NOTE]
>  当前无法将数据存档到安全虚拟网络中的存储帐户。

## <a name="log-profile"></a>日志配置文件
若要使用下述任意方法存档活动日志，可为订阅设置**日志配置文件**。 日志配置文件定义已存储或流式传输的事件的类型，以及输出 - 存储帐户和/或事件中心。 它还定义存储在存储帐户中的事件的保留策略（需保留的天数）。 如果将保留策略设置为零，事件将无限期存储。 如果不想让事件无限期存储，可将保留策略设置为 1 到 2147483647 之间的任何值。 保留策略按天应用，因此在一天结束时 (UTC)，会删除当天已超过保留策略期限的日志。 例如，假设保留策略的期限为一天，则在今天开始时，会删除前天的日志。 删除过程从 UTC 晚上 12 点开始，但请注意，可能需要最多 24 小时才能将日志从存储帐户中删除。 [可在此处了解日志配置文件的详细信息](monitoring-overview-activity-logs.md#export-the-activity-log-with-a-log-profile)。 

## <a name="archive-the-activity-log-using-the-portal"></a>使用门户存档活动日志
1. 在门户中，单击左侧导航中的“活动日志”链接。 如果看不到活动日志的链接，请先单击“所有服务”链接。
   
    ![导航到“活动日志”边栏选项卡](media/monitoring-archive-activity-log/act-log-portal-navigate.png)
2. 在边栏选项卡顶部单击“导出”。
   
    ![单击“导出”按钮](media/monitoring-archive-activity-log/act-log-portal-export-button.png)
3. 在显示的边栏选项卡中，选中与“导出到存储帐户”相对应的框，并选择存储帐户。
   
    ![设置存储帐户](media/monitoring-archive-activity-log/act-log-portal-export-blade.png)
4. 使用滑块或文本框，定义活动日志事件在存储帐户中的保留天数（0 至 365 天）。 要让数据无限期保留在存储帐户中，可将此数值设置为零。 如果需要输入大于 365 的天数，请使用 PowerShell 或 CLI 方法，如下所述。
5. 单击“ **保存**”。

## <a name="archive-the-activity-log-via-powershell"></a>通过 PowerShell 存档活动日志

   ```powershell
   # Settings needed for the new log profile
   $logProfileName = "default"
   $locations = (Get-AzureRmLocation).Location
   $locations += "global"
   $subscriptionId = "<your Azure subscription Id>"
   $resourceGroupName = "<resource group name your storage account belongs to>"
   $storageAccountName = "<your storage account name>"

   # Build the storage account Id from the settings above
   $storageAccountId = "/subscriptions/$subscriptionId/resourceGroups/$resourceGroupName/providers/Microsoft.Storage/storageAccounts/$storageAccountName"

   Add-AzureRmLogProfile -Name $logProfileName -Location $locations -StorageAccountId $storageAccountId
   ```

| 属性 | 必选 | 说明 |
| --- | --- | --- |
| StorageAccountId |是 |应该将活动日志保存到其中的存储帐户的资源 ID。 |
| 位置 |是 |要为其收集活动日志事件的逗号分隔区域的列表。 可以使用 `(Get-AzureRmLocation).Location` 查看订阅的所有区域列表。 |
| RetentionInDays |否 |事件的保留天数，介于 1 到 2147483647 之间。 值为零时，将无限期（永久）存储日志。 |
| 类别 |否 |应收集的事件类别的逗号分隔列表。 可能值包括：Write、Delete 和 Action。  如果未提供，则假定所有可能的值 |

## <a name="archive-the-activity-log-via-cli"></a>通过 CLI 存档活动日志

   ```azurecli-interactive
   az monitor log-profiles create --name "default" --location null --locations "global" "eastus" "westus" --categories "Delete" "Write" "Action"  --enabled false --days 0 --storage-account-id "/subscriptions/<YOUR SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.Storage/storageAccounts/<STORAGE ACCOUNT NAME>"
   ```

| 属性 | 必选 | 说明 |
| --- | --- | --- |
| 名称 |是 |日志配置文件的名称。 |
| storage-account-id |是 |应该将活动日志保存到其中的存储帐户的资源 ID。 |
| 位置 |是 |要为其收集活动日志事件的空格分隔区域列表。 可以使用 `az account list-locations --query [].name` 查看订阅的所有区域列表。 |
| days |是 |事件的保留天数，介于 1 到 2147483647 之间。 值为零时，将无限期（永久）存储日志。  如果为零，则启用的参数应设置为 true。 |
|已启用 | 是 |True 或 False。  用于启用或禁用保留策略。  如果为 True，则 days 参数必须为大于 0 的值。
| categories |是 |应收集的事件类别的空格分隔列表。 可能值包括：Write、Delete 和 Action。 |

## <a name="storage-schema-of-the-activity-log"></a>活动日志的存储架构
设置存档以后，一旦出现活动日志事件，就会在存储帐户中创建存储容器。 容器中的 blob 在活动日志和诊断日志中采用相同的命名约定，如下所示：

```
insights-operational-logs/name=default/resourceId=/SUBSCRIPTIONS/{subscription ID}/y={four-digit numeric year}/m={two-digit numeric month}/d={two-digit numeric day}/h={two-digit 24-hour clock hour}/m=00/PT1H.json
```

例如，blob 名称可以为：

```
insights-operational-logs/name=default/resourceId=/SUBSCRIPTIONS/s1id1234-5679-0123-4567-890123456789/y=2016/m=08/d=22/h=18/m=00/PT1H.json
```

每个 PT1H.json blob 都包含一个 JSON blob，其中的事件为在 blob URL 中指定的小时（例如 h=12）内发生的。 在当前的小时内发生的事件将附加到 PT1H.json 文件。 分钟值始终为 00 (m=00)，因为活动日志事件按小时细分成单个 blob。

在 PT1H.json 文件中，每个事件都按以下格式存储在“records”数组中：

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


| 元素名称 | 说明 |
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
| 级别 |事件的级别。 以下值之一：“Critical”、“Error”、“Warning”、“Informational”和“Verbose” |
| location |位置所在的区域（或全局）。 |
| 属性 |`<Key, Value>` 对集合（即字典），描述事件的详细信息。 |

> [!NOTE]
> 这些属性的属性和使用情况各不相同，具体取决于资源。
> 
> 

## <a name="next-steps"></a>后续步骤
* [下载 blob 进行分析](../storage/blobs/storage-quickstart-blobs-dotnet.md)
* [将活动日志流式传输到事件中心](monitoring-stream-activity-logs-event-hubs.md)
* [详细了解活动日志](monitoring-overview-activity-logs.md)

