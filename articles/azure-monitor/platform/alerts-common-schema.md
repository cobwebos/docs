---
title: Azure 监视器将发出警报的常见警报架构
description: 了解为何应使用它以及如何启用它的常见警报架构
author: anantr
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 03/14/2019
ms.author: anantr
ms.component: alerts
ms.openlocfilehash: be38f5d3bbf4fe3d90fc78feab8774595b5338f2
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/28/2019
ms.locfileid: "67447669"
---
# <a name="common-alert-schema"></a>常见警报架构

本指南介绍了常见的警报架构是什么，使用它以及如何启用它的好处。

## <a name="what-is-the-common-alert-schema"></a>什么是常见的警报架构？

常见警报架构标准化目前在 Azure 中的警报通知的使用体验。 从历史上看，他们自己的电子邮件模板、 webhook 架构等，都遇到了三种警报类型在 Azure 中的立即 （指标、 日志和活动日志）。常见警报架构后，现在可以接收警报通知的一致架构。

任何警报实例描述**受到了影响的资源**并**警报的原因**，这些实例将在以下各节中的常见架构描述：
* **Essentials**:一套**标准化字段**、 跨所有警报类型，介绍了常见**哪项资源**警报是针对以及其他常见警报元数据 （例如，严重或说明）。 
* **警报上下文**:一组描述的字段**的警报会导致**，具有不同的字段**根据警报类型**。 例如，指标警报将具有字段，例如的指标名称和警报上下文中的指标值，而活动日志警报会具有有关事件生成警报的信息。 

我们从客户处听到的典型集成方案涉及到相关团队根据一些透视 （例如，资源组），其后负责团队会在其开始工作的警报实例的路由。 常见警报架构后，您可以具有标准化的路由逻辑跨的警报类型通过利用重要的字段中，保留可供考虑的团队以做进一步调查的上下文字段。

这意味着，你可能具有较少的集成，使管理和维护它们的过程_很多_更简单的任务。 此外，将来的警报有效负载 （例如，自定义、 诊断扩充等） 的正将仅显示在公用架构中。

## <a name="what-enhancements-does-the-common-alert-schema-bring"></a>常见警报架构会带来哪些增强？

常见警报架构将主要表现在警报通知。 下面列出了你将看到的增强功能：

| 操作 | 增强功能|
|:---|:---|
| SMS | 用于所有警报类型的一致的 SMS 模板。 |
| Email | 一致且详细的电子邮件模板，从而可以轻松地诊断问题一目了然。 嵌入的深度链接到门户和受影响的资源上的警报实例确保，你可以快速跳转到修正过程。 |
| Webhook/逻辑应用/Azure 函数/自动化 Runbook | 一致 JSON 结构的所有警报类型，以便您可以轻松地跨不同的警报类型生成集成。 |

新架构还可以实现更丰富的警报消费体验在 Azure 门户和 Azure 移动应用在不久的将来。 

[了解有关 Webhook/逻辑应用/Azure 函数/自动化 Runbook 的架构定义的详细信息。](https://aka.ms/commonAlertSchemaDefinitions)

> [!NOTE]
> 以下操作不支持常见的警报架构：ITSM 连接器。

## <a name="how-do-i-enable-the-common-alert-schema"></a>如何启用常见警报架构？

可以选择加入或退出到常见的警报架构通过操作组，在这两个门户和 REST API。 在操作级别存在开关以切换到新的架构。 例如，您必须单独选择加入的电子邮件操作和 webhook 操作。

> [!NOTE]
> 1. 以下警报类型默认情况下支持的常见架构 (不选择在所需):
>     * 智能检测警报
> 1. 以下警报类型目前不支持公用架构：
>     * 生成的警报[Vm 的 Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/insights/vminsights-overview)
>     * 生成的警报[Azure 成本管理](https://docs.microsoft.com/azure/billing/billing-cost-management-budget-scenario)

### <a name="through-the-azure-portal"></a>通过 Azure 门户

![常见警报架构选择加入](media/alerts-common-schema/portal-opt-in.png)

1. 打开一个操作组中的任何现有或新的操作。 
1. 选择是开关以启用常见警报架构所示。

### <a name="through-the-action-groups-rest-api"></a>通过操作组 REST API

此外可以使用[操作组 API](https://docs.microsoft.com/rest/api/monitor/actiongroups)可以选择将常见的警报架构。 同时让[创建或更新](https://docs.microsoft.com/rest/api/monitor/actiongroups/createorupdate)REST API 调用时，可以设置标志"useCommonAlertSchema"为 'true' （若要选择加入） 或 false （若要选择退出） 的任何以下操作的电子邮件/webhook/逻辑应用/Azure 函数/自动化 runbook。

例如，以下请求正文对所做[创建或更新](https://docs.microsoft.com/rest/api/monitor/actiongroups/createorupdate)REST API 将执行以下操作：

* 启用电子邮件操作"John Doe 的电子邮件"的常见警报架构
* 禁用电子邮件操作"Jane smith 撰写的电子邮件"的常见警报架构
* 启用 webhook 操作"示例 webhook"的常见警报架构

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

- [通用警报架构定义的 Webhook/逻辑应用/Azure 函数/自动化 Runbook。](https://aka.ms/commonAlertSchemaDefinitions)
- [了解如何创建逻辑应用，可利用常见的警报架构来处理所有警报。](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-common-schema-integrations) 



