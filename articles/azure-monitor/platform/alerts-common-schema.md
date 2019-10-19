---
title: Azure monitor 警报的常见警报架构
description: 了解常见的警报架构、为什么应使用它以及如何启用它
ms.service: azure-monitor
ms.subservice: alerts
ms.topic: conceptual
author: anantr
ms.author: robb
ms.date: 03/14/2019
ms.openlocfilehash: ff5c0d96bd4bc87f36a69ca7e8014e118ac7b7bf
ms.sourcegitcommit: ae461c90cada1231f496bf442ee0c4dcdb6396bc
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/17/2019
ms.locfileid: "72552457"
---
# <a name="common-alert-schema"></a>常见警报架构

本文介绍常见的警报架构是什么，以及使用它的好处，以及如何启用它。

## <a name="what-is-the-common-alert-schema"></a>什么是常见警报架构？

常见的警报架构会在 Azure 中标准化警报通知的使用体验。 如今，Azure 中的三种警报类型（指标、日志和活动日志）都有自己的电子邮件模板、webhook 架构等。使用常见的警报架构，你现在可以接收具有一致架构的警报通知。

任何警报实例都描述**受影响的资源**和**警报的原因**，并且以下部分中的常见架构介绍了这些实例：
* **Essentials**：一组**标准化字段**，适用于所有警报类型，这些字段用于描述警报所**处的资源**以及其他常见的警报元数据（例如严重性或说明）。 
* **警报上下文**：一组用于描述**警报原因的**字段，其中的字段**根据警报类型**而变化。 例如，指标警报在警报上下文中具有类似于 "指标名称" 和 "指标" 值的字段，而活动日志警报则包含有关生成警报的事件的信息。 

我们从客户那里了解到的典型集成方案涉及到根据某些透视（例如资源组）将警报实例路由到相关团队，之后负责团队开始处理。 使用常见的警报架构，可以通过利用重要字段，使警报类型具有标准化的路由逻辑，使相关团队可以更进一步地调查上下文字段。

这意味着，可能会有更少的集成，从而使管理和维护这些任务的过程_更加_简单。 此外，以后的警报有效负载根据（例如，自定义、诊断扩充等）只会在常见架构中出现。

## <a name="what-enhancements-does-the-common-alert-schema-bring"></a>常见警报架构引入了哪些增强功能？

常见的警报架构将主要在警报通知中进行清单。 下面列出了你将看到的增强功能：

| 行动 | 增强功能|
|:---|:---|
| 短信 | 适用于所有警报类型的一致短信模板。 |
| 电子邮件 | 一个一致且详细的电子邮件模板，使您可以轻松地快速诊断问题。 嵌入的深层链接到门户上的警报实例以及受影响的资源确保你可以快速跳转到修正过程。 |
| Webhook/逻辑应用/Azure Function/Automation Runbook | 适用于所有警报类型的一致性 JSON 结构，使你能够轻松地跨不同的警报类型构建集成。 |

新的架构还将在立即 Azure 门户和 Azure 移动应用中实现更丰富的警报消耗体验。 

[详细了解 Webhook/逻辑应用/Azure Functions/自动化 Runbook 的架构定义。](https://aka.ms/commonAlertSchemaDefinitions)

> [!NOTE]
> 以下操作不支持常见的警报架构： ITSM 连接器。

## <a name="how-do-i-enable-the-common-alert-schema"></a>如何实现启用常见的警报架构？

你可以在门户和 REST API 上通过操作组选择加入或选择退出常见警报架构。 切换到新架构的切换在操作级别。 例如，你必须单独选择加入电子邮件操作和 webhook 操作。

> [!NOTE]
> 1. 默认情况下，以下警报类型支持通用架构（无需选择）：
>     * 智能检测警报
> 1. 以下警报类型当前不支持通用架构：
>     * [用于 VM 的 Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/insights/vminsights-overview)生成的警报
>     * [Azure 成本管理](https://docs.microsoft.com/azure/billing/billing-cost-management-budget-scenario)生成的警报

### <a name="through-the-azure-portal"></a>通过 Azure 门户

![常见警报架构选择加入](media/alerts-common-schema/portal-opt-in.png)

1. 打开操作组中的任何现有或新操作。 
1. 选择 "是" 进行切换，以启用常见的警报架构，如所示。

### <a name="through-the-action-groups-rest-api"></a>通过操作组 REST API

你还可以使用[操作组 API](https://docs.microsoft.com/rest/api/monitor/actiongroups)选择使用常见的警报架构。 进行[创建或更新](https://docs.microsoft.com/rest/api/monitor/actiongroups/createorupdate)REST API 调用时，可以将 "useCommonAlertSchema" 标志设置为 "true" （对于选择加入）或 "false" （以选择退出），以执行以下任何操作-电子邮件/webhook/逻辑应用/Azure Function/automation runbook。

例如，以下针对[create 或 update](https://docs.microsoft.com/rest/api/monitor/actiongroups/createorupdate) REST API 的请求正文将执行以下操作：

* 为电子邮件操作 "John Doe 的电子邮件" 启用常见警报架构
* 为电子邮件操作 "Jane Smith 的电子邮件" 禁用常见的警报架构
* 启用 webhook 操作 "示例 webhook" 的通用警报架构

```json
{
  "properties": {
    "groupShortName": "sample",
    "enabled": true,
    "emailReceivers": [
      {
        "name": "John Doe's email",
        "emailAddress": "johndoe@email.com",
        "useCommonAlertSchema": true
      },
      {
        "name": "Jane Smith's email",
        "emailAddress": "janesmith@email.com",
        "useCommonAlertSchema": false
      }
    ],
    "smsReceivers": [
      {
        "name": "John Doe's mobile",
        "countryCode": "1",
        "phoneNumber": "1234567890"
      },
      {
        "name": "Jane Smith's mobile",
        "countryCode": "1",
        "phoneNumber": "0987654321"
      }
    ],
    "webhookReceivers": [
      {
        "name": "Sample webhook",
        "serviceUri": "http://www.example.com/webhook",
        "useCommonAlertSchema": true
      }
    ]
  },
  "location": "Global",
  "tags": {}
}
```





## <a name="next-steps"></a>后续步骤

- [Webhook/逻辑应用/Azure Functions/自动化 Runbook 的常见警报架构定义。](https://aka.ms/commonAlertSchemaDefinitions)
- [了解如何创建利用通用警报架构处理所有警报的逻辑应用。](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-common-schema-integrations) 



