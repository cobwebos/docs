---
title: 使用 Azure 虚拟机进行高级自动缩放 | Microsoft 文档
description: 使用包含多个规则和配置文件的 Resource Manager 与 VM 规模集，通过缩放操作发送电子邮件和调用 Webhook URL。
author: anirudhcavale
manager: orenr
editor: ''
services: monitoring-and-diagnostics
documentationcenter: monitoring-and-diagnostics
ms.assetid: 7e3576e2-4a2b-4736-b5ae-98c4689cdd2b
ms.service: monitoring-and-diagnostics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/22/2016
ms.author: ancav
ms.openlocfilehash: 80955535c8d863cd3d8d1b77e2ab8bc016b6d9f3
ms.sourcegitcommit: 6fcd9e220b9cd4cb2d4365de0299bf48fbb18c17
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/05/2018
---
# <a name="advanced-autoscale-configuration-using-resource-manager-templates-for-vm-scale-sets"></a>使用 VM 规模集的 Resource Manager 模板的高级自动缩放配置
可以根据性能指标阈值，按循环计划或按特定日期扩展和缩减虚拟机规模集。 还可以为缩放操作配置电子邮件和 webhook 通知。 本文演示了在 VM 规模集上使用 Resource Manager 模板配置以上所有对象的示例。

> [!NOTE]
> 尽管本文演示的是适用于 VM 规模集的步骤，但这些信息也同样适用于自动缩放[云服务](https://azure.microsoft.com/services/cloud-services/)和[应用服务 - Web 应用](https://azure.microsoft.com/services/app-service/web/)。
> 有关根据简单的性能指标（如 CPU）在 VM 规模集上进行简单的缩小/扩大设置的相关信息，请参阅 [Linux](../virtual-machine-scale-sets/virtual-machine-scale-sets-linux-autoscale.md) 和 [Windows](../virtual-machine-scale-sets/virtual-machine-scale-sets-windows-autoscale.md) 文档
>
>

## <a name="walkthrough"></a>演练
在本演练中，将使用 [Azure 资源浏览器](https://resources.azure.com/)来配置和更新规模集的自动缩放设置。 Azure 资源浏览器是通过 Resource Manager 模板轻松管理 Azure 资源的一种方式。 如果不熟悉 Azure 资源浏览器工具，请参阅[此简介](https://azure.microsoft.com/blog/azure-resource-explorer-a-new-tool-to-discover-the-azure-api/)。

1. 使用基本自动缩放设置部署新的规模集。 本文会使用 Azure 快速入门库中的规模集，该库中具有含基本自动缩放模板的 Windows 规模集。 Linux 规模集的工作方式相同。
2. 创建规模集后，从 Azure 资源管理器导航到规模集资源。 会在 Microsoft.Insights 节点下看到以下内容。

    ![Azure 资源管理器](./media/insights-advanced-autoscale-vmss/azure_explorer_navigate.png)

    模板执行已创建名为 **“autoscalewad”** 的默认自动缩放设置。 在右侧可以查看此自动缩放设置的完整定义。 在这种情况下，默认自动缩放设置带有基于 CPU% 的扩大和缩小规则。  

3. 现在，可以根据计划或特定要求添加更多配置文件和规则。 我们会创建一个具有三个配置文件的自动缩放设置。 若要了解自动缩放中的配置文件和规则，请查看[自动缩放最佳做法](insights-autoscale-best-practices.md)。  

    | 配置文件和规则 | 说明 |
    |--- | --- |
    | **配置文件** |**基于性能/指标** |
    | 规则 |服务总线队列消息计数 > x |
    | 规则 |服务总线队列消息计数 < y |
    | 规则 |CPU% > n |
    | 规则 |CPU% < p |
    | **配置文件** |**工作日上午时（无规则）** |
    | **配置文件** |**产品发布日（无规则）** |

4. 以下是用于进行此演练所假设的一个缩放方案。

    * **基于负载** - 我想要根据在规模集上托管的应用程序上的负载情况进行扩大或缩小。*
    * **消息队列大小** - 使用服务总线队列为应用程序传入消息。 使用队列的消息计数和 CPU 利用率并配置默认的配置文件，在消息计数或 CPU 利用率达到阈值时触发缩放操作。*
    * **每周和每日时间** - 每周定期按基于“每日时间”的配置文件（名为“工作日上午时间”）执行。 根据历史数据，在此期间最好有一定数量的 VM 实例来处理应用程序的负载。*
    * **特殊日期** - 添加了“产品上市日”配置文件。 提前计划具体日期，以便应用程序做好准备以处理由市场通知以及将新产品放入到应用程序中时所产生的负载。*
    * *可以在最后两个配置文件中放置基于其他性能指标的规则。在这种情况下，我决定不那样做，而是依赖于基于默认性能指标的规则。对于定期和基于日期的配置文件来说，规则是可选的。*

    自动缩放引擎的配置文件和规则的优先级顺序也在[自动缩放最佳实践](insights-autoscale-best-practices.md)文章中有所介绍。
    有关自动缩放的常用指标列表，请参阅[自动缩放的常用指标](insights-autoscale-common-metrics.md)

5. 确保在资源浏览器中处于“读/写”模式

    ![Autoscalewad，默认自动缩放设置](./media/insights-advanced-autoscale-vmss/autoscalewad.png)

6. 单击“编辑”。 将自动缩放设置中的“配置文件”元素**替换**为以下配置：

    ![配置文件](./media/insights-advanced-autoscale-vmss/profiles.png)

    ```
    {
            "name": "Perf_Based_Scale",
            "capacity": {
              "minimum": "2",
              "maximum": "12",
              "default": "2"
            },
            "rules": [
              {
                "metricTrigger": {
                  "metricName": "MessageCount",
                  "metricNamespace": "",
                  "metricResourceUri": "/subscriptions/s1/resourceGroups/rg1/providers/Microsoft.ServiceBus/namespaces/mySB/queues/myqueue",
                  "timeGrain": "PT5M",
                  "statistic": "Average",
                  "timeWindow": "PT5M",
                  "timeAggregation": "Average",
                  "operator": "GreaterThan",
                  "threshold": 10
                },
                "scaleAction": {
                  "direction": "Increase",
                  "type": "ChangeCount",
                  "value": "1",
                  "cooldown": "PT5M"
                }
              },
              {
                "metricTrigger": {
                  "metricName": "MessageCount",
                  "metricNamespace": "",
                  "metricResourceUri": "/subscriptions/s1/resourceGroups/rg1/providers/Microsoft.ServiceBus/namespaces/mySB/queues/myqueue",
                  "timeGrain": "PT5M",
                  "statistic": "Average",
                  "timeWindow": "PT5M",
                  "timeAggregation": "Average",
                  "operator": "LessThan",
                  "threshold": 3
                },
                "scaleAction": {
                  "direction": "Decrease",
                  "type": "ChangeCount",
                  "value": "1",
                  "cooldown": "PT5M"
                }
              },
              {
                "metricTrigger": {
                  "metricName": "Percentage CPU",
                  "metricNamespace": "",
                  "metricResourceUri": "/subscriptions/s1/resourceGroups/rg1/providers/Microsoft.Compute/virtualMachineScaleSets/<this_vmss_name>",
                  "timeGrain": "PT5M",
                  "statistic": "Average",
                  "timeWindow": "PT30M",
                  "timeAggregation": "Average",
                  "operator": "GreaterThan",
                  "threshold": 85
                },
                "scaleAction": {
                  "direction": "Increase",
                  "type": "ChangeCount",
                  "value": "1",
                  "cooldown": "PT5M"
                }
              },
              {
                "metricTrigger": {
                  "metricName": "Percentage CPU",
                  "metricNamespace": "",
                  "metricResourceUri": "/subscriptions/s1/resourceGroups/rg1/providers/Microsoft.Compute/virtualMachineScaleSets/<this_vmss_name>",
                  "timeGrain": "PT5M",
                  "statistic": "Average",
                  "timeWindow": "PT30M",
                  "timeAggregation": "Average",
                  "operator": "LessThan",
                  "threshold": 60
                },
                "scaleAction": {
                  "direction": "Decrease",
                  "type": "ChangeCount",
                  "value": "1",
                  "cooldown": "PT5M"
                }
              }
            ]
          },
          {
            "name": "Weekday_Morning_Hours_Scale",
            "capacity": {
              "minimum": "4",
              "maximum": "12",
              "default": "4"
            },
            "rules": [],
            "recurrence": {
              "frequency": "Week",
              "schedule": {
                "timeZone": "Pacific Standard Time",
                "days": [
                  "Monday",
                  "Tuesday",
                  "Wednesday",
                  "Thursday",
                  "Friday"
                ],
                "hours": [
                  6
                ],
                "minutes": [
                  0
                ]
              }
            }
          },
          {
            "name": "Product_Launch_Day",
            "capacity": {
              "minimum": "6",
              "maximum": "20",
              "default": "6"
            },
            "rules": [],
            "fixedDate": {
              "timeZone": "Pacific Standard Time",
              "start": "2016-06-20T00:06:00Z",
              "end": "2016-06-21T23:59:00Z"
            }
          }
    ```
    有关支持的字段及其值，请参阅[自动缩放 REST API 文档](https://msdn.microsoft.com/en-us/library/azure/dn931928.aspx)。 现在，自动缩放设置包含了之前说明的三个配置文件。

7. 最后，来看一下自动缩放“通知”部分。 自动缩放通知允许在成功触发扩大或缩小操作时执行三项操作。
   - 通知订阅的管理员和共同管理员
   - 向一组用户发送电子邮件
   - 触发 webhook 调用。 触发时，此 webhook 将发送关于自动缩放条件和规模集资源的元数据。 若要了解有关自动缩放 webhook 的有效负载的详细信息，请参阅[对自动缩放配置 Webhook 和电子邮件通知](insights-autoscale-to-webhook-email.md)。

   将以下内容添加到自动缩放设置，替换值为 null 的 **notification**  元素

   ```
   "notifications": [
      {
        "operation": "Scale",
        "email": {
          "sendToSubscriptionAdministrator": true,
          "sendToSubscriptionCoAdministrators": false,
          "customEmails": [
              "user1@mycompany.com",
              "user2@mycompany.com"
              ]
        },
        "webhooks": [
          {
            "serviceUri": "https://foo.webhook.example.com?token=abcd1234",
            "properties": {
              "optional_key1": "optional_value1",
              "optional_key2": "optional_value2"
            }
          }
        ]
      }
    ]

   ```

   在资源浏览器中单击“输入”按钮，更新自动缩放设置。

已在 VM 规模集上更新了自动缩放设置，以包括多个缩放配置文件和缩放通知。

## <a name="next-steps"></a>后续步骤
使用下列链接了解有关自动缩放的详细信息。

[排查使用虚拟机规模集进行自动缩放的问题](../virtual-machine-scale-sets/virtual-machine-scale-sets-troubleshoot.md)

[自动缩放的常用指标](insights-autoscale-common-metrics.md)

[Azure 自动缩放的最佳实践](insights-autoscale-best-practices.md)

[使用 PowerShell 管理自动缩放](insights-powershell-samples.md#create-and-manage-autoscale-settings)

[使用 CLI 管理自动缩放](insights-cli-samples.md#autoscale)

[对自动缩放配置 Webhook 和电子邮件通知](insights-autoscale-to-webhook-email.md)
