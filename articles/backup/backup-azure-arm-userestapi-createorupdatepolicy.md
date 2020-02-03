---
title: 使用 REST API 创建备份策略
description: 在本文中，你将了解如何使用 REST API 创建和管理备份策略（计划和保留期）。
ms.topic: conceptual
ms.date: 08/21/2018
ms.assetid: 5ffc4115-0ae5-4b85-a18c-8a942f6d4870
ms.openlocfilehash: 0718ebc3612f53f1c2cc279096dd92de69bb5ef6
ms.sourcegitcommit: 42517355cc32890b1686de996c7913c98634e348
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/02/2020
ms.locfileid: "76963846"
---
# <a name="create-azure-recovery-services-backup-policies-using-rest-api"></a>使用 REST API 创建 Azure 恢复服务备份策略

[策略 REST API 文档](/rest/api/backup/protectionpolicies/createorupdate)中概述了为 Azure 恢复服务保管库创建备份策略的步骤。 让我们参考此文档来为 Azure VM 备份创建策略。

## <a name="create-or-update-a-policy"></a>创建或更新策略

若要创建或更新 Azure 备份策略，请使用以下 *PUT* 操作

```http
PUT https://management.azure.com/Subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.RecoveryServices/vaults/{vaultName}/backupPolicies/{policyName}?api-version=2019-05-13
```

`{policyName}` 和 `{vaultName}` 在 URI 中提供。 其他信息在请求正文中提供。

## <a name="create-the-request-body"></a>创建请求正文

例如，若要为 Azure VM 备份创建策略，需在请求正文中包含以下组成部分。

|名称  |需要  |类型  |Description  |
|---------|---------|---------|---------|
|properties     |   正确      |  ProtectionPolicy:[AzureIaaSVMProtectionPolicy](/rest/api/backup/protectionpolicies/createorupdate#azureiaasvmprotectionpolicy)      | ProtectionPolicyResource 属性        |
|标记     |         | 对象        |  资源标记       |

有关请求正文中的完整定义列表，请参阅[备份策略 REST API 文档](/rest/api/backup/protectionpolicies/createorupdate)。

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

## <a name="responses"></a>响应

创建/更新备份策略是一项[异步操作](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-async-operations)。 这意味着，此操作会创建另一个需要单独跟踪的操作。

当创建另一个操作时，它将返回两个响应：202（接受），然后在该操作完成时返回200（确定）。

|名称  |类型  |Description  |
|---------|---------|---------|
|200 正常     |    [Protection PolicyResource](/rest/api/backup/protectionpolicies/createorupdate#protectionpolicyresource)     |  确定       |
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
Location: https://management.azure.com/Subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/SwaggerTestRg/providers/Microsoft.RecoveryServices/vaults/testVault/backupPolicies/testPolicy1/operationResults/00000000-0000-0000-0000-000000000000?api-version=2019-05-13
X-Powered-By: ASP.NET
```

然后通过简单的 GET 命令并使用 location 标头或 Azure-AsyncOperation 标头跟踪生成的操作。

```http
GET https://management.azure.com/Subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/SwaggerTestRg/providers/Microsoft.RecoveryServices/vaults/testVault/backupPolicies/testPolicy1/operationResults/00000000-0000-0000-0000-000000000000?api-version=2019-05-13
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
