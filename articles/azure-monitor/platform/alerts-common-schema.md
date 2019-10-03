---
title: Azure Monitor 警报的通用警报架构
description: 了解通用警报架构、应该使用它的原因以及启用它的方法
author: anantr
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 03/14/2019
ms.author: robb
ms.subservice: alerts
ms.openlocfilehash: 9b142e00543d425b73c4102914bba2dd92c75b8b
ms.sourcegitcommit: 6fe40d080bd1561286093b488609590ba355c261
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/01/2019
ms.locfileid: "71702892"
---
# <a name="common-alert-schema"></a>常见警报架构

本文介绍通用警报架构是什么、使用它的好处以及启用它的方法。

## <a name="what-is-the-common-alert-schema"></a>通用警报架构是什么？

通用警报架构将目前 Azure 中的警报通知的使用体验进行了标准化。 从历史上看，目前 Azure 中的三种警报类型（指标、日志、活动日志）一直有其自己的电子邮件模板、Webhook 架构等。现在，使用通用警报架构时可以收到架构一致的警报通知。

任何警报实例都会描述**受影响的资源**和**警报原因**。这些实例在以下部分的通用架构中进行介绍：
* **概要**：一组通用于所有警报类型的**标准化字段**，描述对**什么资源**发出警报，此外还有其他通用警报元数据（例如，严重性或说明）。 
* **警报上下文**：一组用于描述**警报原因**的字段，此外还有**因警报类型**而异的字段。 例如，指标警报在警报上下文中会有指标名称和指标值之类的字段，而活动日志警报会包含生成警报的事件的相关信息。 

我们从客户处听到的典型集成方案涉及将警报实例基于某个中心（例如资源组）路由到相关团队，然后该负责团队就开始处理它。 有了通用警报架构以后，可以使用重要字段，将上下文字段保留原样，方便相关团队进一步进行调查，这样就可以跨警报类型实现路由逻辑的标准化。

这意味着可以减少集成数，大大简化其管理和维护过程。 另外，未来的警报有效负载扩充（例如，自定义、诊断扩充等）只会出现在通用架构中。

## <a name="what-enhancements-does-the-common-alert-schema-bring"></a>通用警报架构带来哪些增强功能？

通用警报架构将主要呈现在警报通知中。 你将要看到的增强功能已列在下面：

| 操作 | 增强功能|
|:---|:---|
| 短信 | 一个适用于所有警报类型的一致的 SMS 模板。 |
| Email | 一个一致且详细的电子邮件模板，可以轻松快捷地诊断问题。 嵌入式深度链接指向门户中的警报实例和受影响的资源，确保你可以快速跳转到修正过程。 |
| Webhook/逻辑应用/Azure Functions/自动化 Runbook | 一个适用于所有警报类型的一致的 JSON 结构，用于轻松地跨不同警报类型构建集成。 |

有了这个新的架构，在不久的将来还可以跨 Azure 门户和 Azure 移动应用实现更丰富的警报使用体验。 

[详细了解 Webhook/逻辑应用/Azure Functions/自动化 Runbook 的架构定义。](https://aka.ms/commonAlertSchemaDefinitions)

> [!NOTE]
> 以下操作不支持通用警报架构：ITSM 连接器。

## <a name="how-do-i-enable-the-common-alert-schema"></a>如何启用通用警报架构？

可以使用操作组通过门户和 REST API 选择加入通用警报架构，或者选择退出它。 可以在操作级别通过切换转到新的架构。 例如，对于电子邮件操作和 Webhook 操作，必须单独选择加入。

> [!NOTE]
> 1. 以下警报类型默认支持通用架构（不需选择加入）：
>     * 智能检测警报
> 1. 以下警报类型目前不支持通用架构：
>     * 由[用于 VM 的 Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/insights/vminsights-overview) 生成的警报
>     * 由 [Azure 成本管理](https://docs.microsoft.com/azure/billing/billing-cost-management-budget-scenario)生成的警报

### <a name="through-the-azure-portal"></a>通过 Azure 门户

![选择加入通用警报架构](media/alerts-common-schema/portal-opt-in.png)

1. 在操作组中打开现有的或新的操作。 
1. 选择“是”，通过切换启用通用警报架构，如图所示。

### <a name="through-the-action-groups-rest-api"></a>通过操作组 REST API

也可使用[操作组 API](https://docs.microsoft.com/rest/api/monitor/actiongroups) 来选择加入通用警报架构。 进行[创建或更新](https://docs.microsoft.com/rest/api/monitor/actiongroups/createorupdate) REST API 调用时，可以针对下述任意操作将标记 "useCommonAlertSchema" 设置为 true（选择加入）或 false（选择退出）：电子邮件/Webhook/逻辑应用/Azure Functions/自动化 Runbook。

例如，针对[创建或更新](https://docs.microsoft.com/rest/api/monitor/actiongroups/createorupdate) REST API 设置的以下请求正文会执行以下操作：

* 针对电子邮件操作“John Doe 的电子邮件”启用通用警报架构
* 针对电子邮件操作“Jane Smith 的电子邮件”禁用通用警报架构
* 针对 Webhook 操作“示例 Webhook”启用通用警报架构

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

- [Webhook/逻辑应用/Azure Functions/自动化 Runbook 的通用警报架构定义。](https://aka.ms/commonAlertSchemaDefinitions)
- [了解如何创建一个逻辑应用，利用通用警报架构来处理所有警报。](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-common-schema-integrations) 



