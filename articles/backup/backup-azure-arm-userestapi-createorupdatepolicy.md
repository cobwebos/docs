---
title: Azure 备份：使用 REST API 创建备份策略
description: 使用 REST API 管理备份策略（计划和保留期）
ms.reviewer: pullabhk
author: dcurwin
manager: carmonm
keywords: REST API; Azure VM 备份; Azure VM 还原
ms.service: backup
ms.topic: conceptual
ms.date: 08/21/2018
ms.author: dacurwin
ms.assetid: 5ffc4115-0ae5-4b85-a18c-8a942f6d4870
ms.openlocfilehash: 8b812ea053cb8e9da7cd3ef021ab6b74196d36ca
ms.sourcegitcommit: 0f54f1b067f588d50f787fbfac50854a3a64fff7
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/12/2019
ms.locfileid: "68954966"
---
# <a name="create-azure-recovery-services-backup-policies-using-rest-api"></a>使用 REST API 创建 Azure 恢复服务备份策略

[策略 REST API 文档](https://docs.microsoft.com/rest/api/backup/protectionpolicies/createorupdate)中概述了为 Azure 恢复服务保管库创建备份策略的步骤。 让我们参考此文档来为 Azure VM 备份创建策略。

## <a name="backup-policy-essentials"></a>备份策略概要

- 备份策略是按保管库创建的。
- 可为以下工作负荷的备份创建备份策略
  - Azure VM
  - Azure VM 中的 SQL
  - Azure 文件共享
- 可将一个策略分配到多个资源。 可以使用一个 Azure VM 备份策略来保护多个 Azure VM。
- 策略由两个部分组成
  - 编制进行备份的时间
  - 保留每个备份应保留多长时间。
- 可将计划定义为带有特定时间点的“每日”或“每周”计划。
- 可以针对“每日”、“每周”、“每月”、“每年”备份点定义保留期。
- “每周”是指在特定的星期日期进行备份，“每月”是指在特定的月份日期进行备份，“每年”是指在特定的年份日期进行备份。
- “每月”、“每年”备份点的保留期称为“LongTermRetention”。
- 创建保管库后, 还会创建名为 "DefaultPolicy" 的 Azure VM 备份的策略, 并将其用于备份 Azure Vm。

若要创建或更新 Azure 备份策略，请使用以下 *PUT* 操作

```http
PUT https://management.azure.com/Subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.RecoveryServices/vaults/{vaultName}/backupPolicies/{policyName}?api-version=2016-12-01
```

`{policyName}` 和 `{vaultName}` 在 URI 中提供。 其他信息在请求正文中提供。

## <a name="create-the-request-body"></a>创建请求正文

例如，若要为 Azure VM 备份创建策略，需在请求正文中包含以下组成部分。

|名称  |必填  |类型  |描述  |
|---------|---------|---------|---------|
|properties     |   True      |  ProtectionPolicy:[AzureIaaSVMProtectionPolicy](https://docs.microsoft.com/rest/api/backup/protectionpolicies/createorupdate#azureiaasvmprotectionpolicy)      | ProtectionPolicyResource 属性        |
|标记     |         | Object        |  资源标记       |

有关请求正文中的完整定义列表，请参阅[备份策略 REST API 文档](https://docs.microsoft.com/rest/api/backup/protectionpolicies/createorupdate)。

### <a name="example-request-body"></a>示例请求正文

以下请求正文定义 Azure VM 备份的备份策略。

该策略指出：

- 在每个星期一、星期三、星期四上午 10:00（太平洋标准时间）创建每周备份。
- 将每个星期一、星期三、星期四创建的备份保留一周。
- 将每个月的第一个星期三和第三个星期四创建的备份保留两个月（取代以前的保留条件，如果有）。
- 将 2 月和 11 月的第四个星期一和第四个星期四创建的备份保留四年（取代以前的保留条件，如果有）。

```json
{
  "properties": {
    "backupManagementType": "AzureIaasVM",
    "timeZone": "Pacific Standard Time",
    "schedulePolicy": {
      "schedulePolicyType": "SimpleSchedulePolicy",
      "scheduleRunFrequency": "Weekly",
      "scheduleRunTimes": [
        "2018-01-24T10:00:00Z"
      ],
      "scheduleRunDays": [
        "Monday",
        "Wednesday",
        "Thursday"
      ]
    },
    "retentionPolicy": {
      "retentionPolicyType": "LongTermRetentionPolicy",
      "weeklySchedule": {
        "daysOfTheWeek": [
          "Monday",
          "Wednesday",
          "Thursday"
        ],
        "retentionTimes": [
          "2018-01-24T10:00:00Z"
        ],
        "retentionDuration": {
          "count": 1,
          "durationType": "Weeks"
        }
      },
      "monthlySchedule": {
        "retentionScheduleFormatType": "Weekly",
        "retentionScheduleWeekly": {
          "daysOfTheWeek": [
            "Wednesday",
            "Thursday"
          ],
          "weeksOfTheMonth": [
            "First",
            "Third"
          ]
        },
        "retentionTimes": [
          "2018-01-24T10:00:00Z"
        ],
        "retentionDuration": {
          "count": 2,
          "durationType": "Months"
        }
      },
      "yearlySchedule": {
        "retentionScheduleFormatType": "Weekly",
        "monthsOfYear": [
          "February",
          "November"
        ],
        "retentionScheduleWeekly": {
          "daysOfTheWeek": [
            "Monday",
            "Thursday"
          ],
          "weeksOfTheMonth": [
            "Fourth"
          ]
        },
        "retentionTimes": [
          "2018-01-24T10:00:00Z"
        ],
        "retentionDuration": {
          "count": 4,
          "durationType": "Years"
        }
      }
    }
  }
}
```

> [!IMPORTANT]
> 计划和保留期的时间格式仅支持日期时间， 不支持单独的时间格式。

## <a name="responses"></a>Responses

创建/更新备份策略是一项[异步操作](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-async-operations)。 这意味着，此操作会创建另一个需要单独跟踪的操作。

它将返回两个响应：创建另一个操作时为 202（已接受），该操作完成时为 200（正常）。

|名称  |类型  |描述  |
|---------|---------|---------|
|200 正常     |    [Protection PolicyResource](https://docs.microsoft.com/rest/api/backup/protectionpolicies/createorupdate#protectionpolicyresource)     |  确定       |
|202 已接受     |         |     已接受    |

### <a name="example-responses"></a>示例响应

提交创建或更新策略的 *PUT* 请求后，初始响应为 202 (Accepted)，以及 location 标头或 Azure-async-header。

```http
HTTP/1.1 202 Accepted
Pragma: no-cache
Retry-After: 60
Azure-AsyncOperation: https://management.azure.com/Subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/SwaggerTestRg/providers/Microsoft.RecoveryServices/vaults/testVault/backupPolicies/testPolicy1/operations/00000000-0000-0000-0000-000000000000?api-version=2016-06-01
X-Content-Type-Options: nosniff
x-ms-request-id: db785be0-bb20-4598-bc9f-70c9428b170b
x-ms-client-request-id: e1f94eef-9b2d-45c4-85b8-151e12b07d03; e1f94eef-9b2d-45c4-85b8-151e12b07d03
Strict-Transport-Security: max-age=31536000; includeSubDomains
x-ms-ratelimit-remaining-subscription-writes: 1199
x-ms-correlation-request-id: db785be0-bb20-4598-bc9f-70c9428b170b
x-ms-routing-request-id: SOUTHINDIA:20180521T073907Z:db785be0-bb20-4598-bc9f-70c9428b170b
Cache-Control: no-cache
Date: Mon, 21 May 2018 07:39:06 GMT
Location: https://management.azure.com/Subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/SwaggerTestRg/providers/Microsoft.RecoveryServices/vaults/testVault/backupPolicies/testPolicy1/operationResults/00000000-0000-0000-0000-000000000000?api-version=2016-06-01
X-Powered-By: ASP.NET
```

然后，可以使用 location 标头或 Azure-AsyncOperation 标头以及简单的 *GET* 命令跟踪生成的操作。

```http
GET https://management.azure.com/Subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/SwaggerTestRg/providers/Microsoft.RecoveryServices/vaults/testVault/backupPolicies/testPolicy1/operationResults/00000000-0000-0000-0000-000000000000?api-version=2016-06-01
```

操作完成后，它将在响应正文中返回 200 (OK) 和策略内容。

```json
{
  "id": "/Subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/SwaggerTestRg/providers/Microsoft.RecoveryServices/vaults/testVault/backupPolicies/testPolicy1",
  "name": "testPolicy1",
  "type": "Microsoft.RecoveryServices/vaults/backupPolicies",
  "properties": {
    "backupManagementType": "AzureIaasVM",
    "schedulePolicy": {
      "schedulePolicyType": "SimpleSchedulePolicy",
      "scheduleRunFrequency": "Weekly",
      "scheduleRunDays": [
        "Monday",
        "Wednesday",
        "Thursday"
      ],
      "scheduleRunTimes": [
        "2018-01-24T10:00:00Z"
      ],
      "scheduleWeeklyFrequency": 0
    },
    "retentionPolicy": {
      "retentionPolicyType": "LongTermRetentionPolicy",
      "weeklySchedule": {
        "daysOfTheWeek": [
          "Monday",
          "Wednesday",
          "Thursday"
        ],
        "retentionTimes": [
          "2018-01-24T10:00:00Z"
        ],
        "retentionDuration": {
          "count": 1,
          "durationType": "Weeks"
        }
      },
      "monthlySchedule": {
        "retentionScheduleFormatType": "Weekly",
        "retentionScheduleWeekly": {
          "daysOfTheWeek": [
            "Wednesday",
            "Thursday"
          ],
          "weeksOfTheMonth": [
            "First",
            "Third"
          ]
        },
        "retentionTimes": [
          "2018-01-24T10:00:00Z"
        ],
        "retentionDuration": {
          "count": 2,
          "durationType": "Months"
        }
      },
      "yearlySchedule": {
        "retentionScheduleFormatType": "Weekly",
        "monthsOfYear": [
          "February",
          "November"
        ],
        "retentionScheduleWeekly": {
          "daysOfTheWeek": [
            "Monday",
            "Thursday"
          ],
          "weeksOfTheMonth": [
            "Fourth"
          ]
        },
        "retentionTimes": [
          "2018-01-24T10:00:00Z"
        ],
        "retentionDuration": {
          "count": 4,
          "durationType": "Years"
        }
      }
    },
    "timeZone": "Pacific Standard Time",
    "protectedItemsCount": 0
  }
}
```

如果已使用某个策略来保护某个项，对该策略进行任何更新都会导致[修改](backup-azure-arm-userestapi-backupazurevms.md#changing-the-policy-of-protection)所有此类关联项的保护。

## <a name="next-steps"></a>后续步骤

[为不受保护的 Azure VM 启用保护](backup-azure-arm-userestapi-backupazurevms.md)。

有关 Azure 备份 REST API 的详细信息，请参阅以下文档：

- [Azure 恢复服务提供程序 REST API](/rest/api/recoveryservices/)
- [Azure REST API 入门](/rest/api/azure/)