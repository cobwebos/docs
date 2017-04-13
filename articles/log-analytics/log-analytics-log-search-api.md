---
title: "Log Analytics 日志搜索 REST API | Microsoft Docs"
description: "本指南提供的基本教程介绍了如何使用 Operations Management Suite (OMS) 中的 Log Analytics 搜索 REST API，提供的示例向你演示如何使用这些命令。"
services: log-analytics
documentationcenter: 
author: bandersmsft
manager: carmonm
editor: 
ms.assetid: b4e9ebe8-80f0-418e-a855-de7954668df7
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/12/2017
ms.author: banders
translationtype: Human Translation
ms.sourcegitcommit: b12f823d723b013755fc868b883faefa2072eb75
ms.openlocfilehash: 9b21fed003f96dbf7ebd72d6f46fff91acbf039e
ms.lasthandoff: 12/02/2016


---
# <a name="log-analytics-log-search-rest-api"></a>Log Analytics 日志搜索 REST API
本指南提供使用 Log Analytics 搜索 REST API 的基本教程，包括示例。 Log Analytics 是 Operations Management Suite (OMS) 的一部分。

> [!NOTE]
> Log Analytics 之前称为 Operational Insights（操作见解），这就是在资源提供程序中使用此名称的原因。
>
>

## <a name="overview-of-the-log-search-rest-api"></a>日志搜索 REST API 概述
Log Analytics 搜索 REST API 为 RESTful，可通过 Azure Resource Manager API 访问。 本文提供通过 [ARMClient](https://github.com/projectkudu/ARMClient)（可简化 Azure Resource Manager API 调用的开源命令行工具）访问 API 的示例。 使用 ARMClient 是访问 Log Analytics 搜索 API 的多种选项之一。 另一个选项是使用 Operational Insights 的 Azure PowerShell 模块，其中包含了用于访问搜索的 cmdlet。 借助这些工具，可以利用 Azure Resource Manager API 对 OMS 工作区进行调用并执行它们所包含的搜索命令。 API 以 JSON 格式输出搜索结果，从而允许以编程方式使用多种不同的方法搜索结果。

可以通过 [.NET 的库](https://msdn.microsoft.com/library/azure/dn910477.aspx)和 [REST API](https://msdn.microsoft.com/library/azure/mt163658.aspx) 使用 Azure Resource Manager。 若要了解详细信息，请查看链接的网页。

> [!NOTE]
> 如果使用 `|measure count()` 或 `distinct` 之类的聚合命令，对搜索的每次调用可返回最多 500000 个记录。 不包含聚合命令的搜索返回最多 5000 个记录。
>
>

## <a name="basic-log-analytics-search-rest-api-tutorial"></a>Log Analytics 搜索 REST API 基本教程
### <a name="to-use-armclient"></a>使用 ARMClient
1. 安装 [Chocolatey](https://chocolatey.org/)，它是适用于 Windows 的开源软件包管理器。 以管理员身份打开命令提示符窗口，然后运行以下命令：

    ```
    @powershell -NoProfile -ExecutionPolicy unrestricted -Command "iex ((new-object net.webclient).DownloadString('https://chocolatey.org/install.ps1'))" && SET PATH=%PATH%;%ALLUSERSPROFILE%\chocolatey\bin
    ```
2. 通过运行以下命令安装 ARMClient：

    ```
    choco install armclient
    ```

### <a name="to-perform-a-search-using-armclient"></a>使用 ARMClient 执行搜索
1. 使用 Microsoft 帐户或者工作或学校帐户登录：

    ```
    armclient login
    ```

    登录成功后，会列出与给定帐户绑定的所有订阅：

    ```
    PS C:\Users\SampleUserName> armclient login
    Welcome YourEmail@ORG.com (Tenant: zzzzzzzz-zzzz-zzzz-zzzz-zzzzzzzzzzzz)
    User: YourEmail@ORG.com, Tenant: zzzzzzzz-zzzz-zzzz-zzzz-zzzzzzzzzzzz (Example org)
    There are 3 subscriptions
    Subscription xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx (Example Name 1)
    Subscription xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx (Example Name 2)
    Subscription xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx (Example Name 3)
    ```
2. 获取 Operations Management Suite 工作区：

    ```
    armclient get /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/OI-Default-East-US/providers/Microsoft.OperationalInsights/workspaces?api-version=2015-03-20
    ```

    Get 调用成功后，会输出与订阅绑定的所有工作区：

    ```
    {
    "value": [
    {
      "properties": {
    "source": "External",
    "customerId": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
    "portalUrl": "https://eus.login.mms.microsoft.com/SignIn.aspx?returnUrl=https%3a%2f%2feus.mms.microsoft.com%2fMain.aspx%3fcid%xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
      },
      "id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourcegroups/oi-default-east-us/providers/microsoft.operationalinsights/workspaces/{WORKSPACE NAME}",
      "name": "{WORKSPACE NAME}",
      "type": "Microsoft.OperationalInsights/workspaces",
      "location": "East US"
       ]
    }
    ```
3. 创建搜索变量：

    ```
    $mySearch = "{ 'top':150, 'query':'Error'}";
    ```
4. 使用新的搜索变量进行搜索：

    ```
    armclient post /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/OI-Default-East-US/providers/Microsoft.OperationalInsights/workspaces/{WORKSPACE NAME}/search?api-version=2015-03-20 $mySearch
    ```

## <a name="log-analytics-search-rest-api-reference-examples"></a>Log Analytics 搜索 REST API 参考示例
以下示例演示了如何使用搜索 API。

### <a name="search---actionread"></a>搜索 - 操作/读取
**示例 Url：**

```
    /subscriptions/{SubscriptionId}/resourceGroups/{ResourceGroupId}/providers/Microsoft.OperationalInsights/workspaces/{WorkspaceName}/search?api-version=2015-03-20
```

**请求：**

```
    $savedSearchParametersJson =
    {
      "top":150,
      "highlight":{
        "pre":"{[hl]}",
        "post":"{[/hl]}"
      },
      "query":"*",
      "start":"2015-02-04T21:03:29.231Z",
      "end":"2015-02-11T21:03:29.231Z"
    }
    armclient post /subscriptions/{Subscription ID}/resourceGroups/OI-Default-East-US/providers/Microsoft.OperationalInsights/workspaces/{Workspace ID}/search?api-version=2015-03-20 $searchParametersJson
```
下表介绍了可用属性。

| **属性** | **说明** |
| --- | --- |
| top |要返回的最大结果数。 |
| highlight |包含 pre 和 post 参数，通常用于突出显示匹配的字段 |
| pre |将给定字符串作为前缀添加到匹配的字段。 |
| post |将给定字符串追加到匹配的字段。 |
| query |用于收集并返回结果的搜索查询。 |
| start |要查找结果的开始时间窗口。 |
| end |要查找结果的结束时间窗口。 |

**响应：**

```
    {
      "id" : "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
      "__metadata" : {
        "resultType" : "raw",
        "total" : 1455,
        "top" : 150,
        "StartTime" : "2015-02-11T21:09:07.0345815Z",
        "Status" : "Successful",
        "LastUpdated" : "2015-02-11T21:09:07.331463Z",
        "CoreResponses" : [],
        "sort" : [{
          "name" : "TimeGenerated",
          "order" : "desc"
        }],
        "requestTime" : 450
      },
      "value": [{
        "SourceSystem" : "OpsManager",
        "TimeGenerated" : "2015-02-07T14:07:33Z",
        "Source" : "SideBySide",
        "EventLog" : "Application",
        "Computer" : "BAMBAM",
        "EventCategory" : 0,
        "EventLevel" : 1,
        "EventLevelName" : "Error",
        "UserName" : "N/A",
        "EventID" : 78,
        "MG" : "00000000-0000-0000-0000-000000000001",
        "TimeCollected" : "2015-02-07T14:10:04.69Z",
        "ManagementGroupName" : "AOI-5bf9a37f-e841-462b-80d2-1d19cd97dc40",
        "id" : "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
        "Type" : "Event",
        "__metadata" : {
          "Type" : "Event",
          "TimeGenerated" : "2015-02-07T14:07:33Z",
          "highlighting" : {
          "EventLevelName" : ["{[hl]}Error{[/hl]}"]
        }
      }]
    ],
            "start" : "2015-02-04T21:03:29.231Z",
            "end" : "2015-02-11T21:03:29.231Z"
          }
        }
      }]
    }
```

### <a name="searchid---actionread"></a>搜索/{ID} - 操作/读取
**请求已保存搜索的内容：**

```
    armclient post /subscriptions/{SubId}/resourceGroups/{ResourceGroupId}/providers/Microsoft.OperationalInsights/workspaces/{WorkspaceName}/search/{SearchId}?api-version=2015-03-20
```

> [!NOTE]
> 当搜索返回“Pending”状态时，则可以通过此 API 轮询已更新的结果。 6 分钟后，将从缓存中清除搜索结果，并返回 HTTP Gone。 如果初始搜索请求立即返回“Successful”状态，结果不会添加到缓存，导致此 API 在查询时返回 HTTP Gone。 HTTP 200 结果的内容采用与仅含有已更新值的初始搜索请求相同的格式。
>
>

### <a name="saved-searches"></a>保存的搜索
**请求已保存搜索的列表：**

```
    armclient post /subscriptions/{SubId}/resourceGroups/{ResourceGroupId}/providers/Microsoft.OperationalInsights/workspaces/{WorkspaceName}/savedSearches?api-version=2015-03-20
```

支持的方法：GET PUT DELETE

支持的收集方法：GET

下表介绍了可用属性。

| 属性 | 说明 |
| --- | --- |
| ID |唯一标识符。 |
| ETag |**修补时必需**。 由服务器在每次写入时进行更新。 值必须等于当前存储的值或“*”才会进行更新。 对于旧/无效值返回 409。 |
| properties.query |**必需**。 搜索查询。 |
| properties.displayName |**必需**。 查询的用户定义显示名称。 |
| properties.category |**必需**。 查询的用户定义类别。 |

> [!NOTE]
> 当针对工作区中保存的搜索进行轮询时，Log Analytics 搜索 API 当前返回用户创建的已保存搜索。 该 API 不返回解决方案提供的已保存搜索。
>
>

### <a name="create-saved-searches"></a>创建保存的搜索
**请求：**

```
    $savedSearchParametersJson = "{'properties': { 'Category': 'myCategory', 'DisplayName':'myDisplayName', 'Query':'* | measure Count() by Source', 'Version':'1'  }"
    armclient put /subscriptions/{Subscription ID}/resourceGroups/OI-Default-East-US/providers/Microsoft.OperationalInsights/workspaces/{Workspace ID}/savedSearches/thisIsMyId?api-version=2015-03-20 $savedSearchParametersJson
```

### <a name="delete-saved-searches"></a>删除保存的搜索
**请求：**

```
    armclient delete /subscriptions/{Subscription ID}/resourceGroups/OI-Default-East-US/providers/Microsoft.OperationalInsights/workspaces/{Workspace ID}/savedSearches/thisIsMyId?api-version=2015-03-20
```

### <a name="update-saved-searches"></a>更新保存的搜索
 **请求：**

```
    $savedSearchParametersJson = "{'etag': 'W/`"datetime\'2015-04-16T23%3A35%3A35.3182423Z\'`"', 'properties': { 'Category': 'myCategory', 'DisplayName':'myDisplayName', 'Query':'* | measure Count() by Source', 'Version':'1'  }"
    armclient put /subscriptions/{Subscription ID}/resourceGroups/OI-Default-East-US/providers/Microsoft.OperationalInsights/workspaces/{Workspace ID}/savedSearches/thisIsMyId?api-version=2015-03-20 $savedSearchParametersJson
```

### <a name="metadata---json-only"></a>元数据 - 仅限 JSON
下面介绍的方法适用于查看在工作区中收集的数据的所有日志类型的字段。 例如，如果想要知道事件类型中是否存在名为“Computer”的字段，可使用此查询来检查。

**对字段的请求：**

```
    armclient get /subscriptions/{SubId}/resourceGroups/{ResourceGroupId}/providers/Microsoft.OperationalInsights/workspaces/{WorkspaceName}/schema?api-version=2015-03-20
```

**响应：**

```
    {
      "__metadata" : {
        "schema" : {
          "name" : "Example Name",
          "version" : 2
        },
        "resultType" : "schema",
        "requestTime" : 35
      },
      "value" : [{
          "name" : "MG",
          "displayName" : "MG",
          "type" : "Guid",
          "facetable" : true,
          "display" : false,
          "ownerType" : ["PerfHourly", "ProtectionStatus", "Capacity_SMBUtilizationByHost", "Capacity_ArrayUtilization", "Capacity_SMBShareUtilization", "SQLAssessmentRecommendation", "Event", "ConfigurationChange", "ConfigurationAlert", "ADAssessmentRecommendation", "ConfigurationObject", "ConfigurationObjectProperty"]
        }, {
          "name" : "ManagementGroupName",
          "displayName" : "ManagementGroupName",
          "type" : "String",
          "facetable" : true,
          "display" : true,
          "ownerType" : ["PerfHourly", "ProtectionStatus", "Event", "ConfigurationChange", "ConfigurationAlert", "W3CIISLog", "AlertHistory", "Recommendation", "Alert", "SecurityEvent", "UpdateAgent", "RequiredUpdate", "ConfigurationObject", "ConfigurationObjectProperty"]
        }
      ]
    }
```

下表介绍了可用属性。

| **属性** | **说明** |
| --- | --- |
| 名称 |字段名称。 |
| displayName |字段的显示名称。 |
| type |字段值的类型。 |
| facetable |当前“indexed”、“stored”和“facet”属性的组合。 |
| display |当前“display”属性。 当字段在搜索中可见时为 True。 |
| ownerType |减少为只属于上载 IP 的类型。 |

## <a name="optional-parameters"></a>可选参数
以下信息介绍了可用的可选参数。

### <a name="highlighting"></a>Highlighting
“Highlight”参数是一个可选参数，可以使用它请求搜索子系统（会在其响应中包含一组标记）。

这些标记指示突出显示的开始和结束文本，这些文本与搜索查询中提供的术语匹配。
可以指定搜索用于包含突出显示的术语的开始和结束标记。

**示例搜索查询**

```
    $savedSearchParametersJson =
    {
      "top":150,
      "highlight":{
        "pre":"{[hl]}",
        "post":"{[/hl]}"
      },
      "query":"*",
      "start":"2015-02-04T21:03:29.231Z",
      "end":"2015-02-11T21:03:29.231Z"
    }
    armclient post /subscriptions/{Subscription ID}/resourceGroups/OI-Default-East-US/providers/Microsoft.OperationalInsights/workspaces/{Workspace ID}/search?api-version=2015-03-20 $searchParametersJson
```

**示例结果：**

```
    {
        "TimeGenerated":
        "2015-05-18T23:55:59Z", "Source":
        "EventLog": "Application",
        "Computer": "smokedturkey.net",
        "EventCategory": 0,
        "EventLevel":1,
        "EventLevelName":
        "Error"
        "Manager ", "__metadata":
        {
            "Type": "Event",
            "TimeGenerated": "2015-05-18T23:55:59Z",
            "highlighting": {
                "EventLevelName":
                ["{[hl]}Error{[/hl]}"]
            }
        }
    }
```

请注意，上述结果中包含了一条已添加前缀和后缀的错误消息。

## <a name="computer-groups"></a>计算机组
计算机组是特殊的已保存搜索，会返回一组计算机。  可以在其他查询中使用计算机组，以将结果限制为组中的计算机。  计算机组使用 Group 标记以及计算机值实现为已保存搜索。

下面是一个计算机组的示例响应。

```
    "etag": "W/\"datetime'2016-04-01T13%3A38%3A04.7763203Z'\"",
    "properties": {
        "Category": "My Computer Groups",
        "DisplayName": "My Computer Group",
        "Query": "srv* | Distinct Computer",
        "Tags": [{
            "Name": "Group",
            "Value": "Computer"
          }],
    "Version": 1
    }
```

### <a name="retrieving-computer-groups"></a>检索计算机组
若要检索计算机组，请结合使用 Get 方法和组 ID。

```
armclient get /subscriptions/{Subscription ID}/resourceGroups/{Resource Group Name}/providers/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Group ID}`?api-version=2015-03-20
```

### <a name="creating-or-updating-a-computer-group"></a>创建或更新计算机组
若要创建计算机组，请结合使用 Put 方法和唯一的已保存搜索 ID。 如果使用现有的计算机组 ID，将修改该 ID。 在 Log Analytics 门户中创建计算机组时，将通过组和名称创建 ID。

用于组定义的查询必须返回一组计算机，才能使组正常工作。  建议以 `| Distinct Computer` 作为查询结尾，以确保返回正确数据。

已保存搜索的定义必须包含名为“Group”的标记以及计算机值，才能使搜索归类为计算机组。

```
    $etag=Get-Date -Format yyyy-MM-ddThh:mm:ss.msZ
    $groupName="My Computer Group"
    $groupQuery = "Computer=srv* | Distinct Computer"
    $groupCategory="My Computer Groups"
    $groupID = "My Computer Groups | My Computer Group"

    $groupJson = "{'etag': 'W/`"datetime\'" + $etag + "\'`"', 'properties': { 'Category': '" + $groupCategory + "', 'DisplayName':'"  + $groupName + "', 'Query':'" + $groupQuery + "', 'Tags': [{'Name': 'Group', 'Value': 'Computer'}], 'Version':'1'  }"

    armclient put /subscriptions/{Subscription ID}/resourceGroups/{Resource Group Name}/providers/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/$groupId`?api-version=2015-03-20 $groupJson
```

### <a name="deleting-computer-groups"></a>删除计算机组
若要删除计算机组，请结合使用 Delete 方法和组 ID。

```
armclient delete /subscriptions/{Subscription ID}/resourceGroups/{Resource Group Name}/providers/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/$groupId`?api-version=2015-03-20
```


## <a name="next-steps"></a>后续步骤
* 了解[日志搜索](log-analytics-log-searches.md)，使用自定义字段作为条件生成查询。

