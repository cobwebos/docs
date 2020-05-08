---
title: Microsoft Azure 中的自动缩放
description: Microsoft Azure 中的自动缩放
ms.subservice: autoscale
ms.topic: conceptual
ms.date: 09/24/2018
ms.openlocfilehash: 08b39fce046ea9dee02ddf6ffe34971b81c3b5b7
ms.sourcegitcommit: a6d477eb3cb9faebb15ed1bf7334ed0611c72053
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/08/2020
ms.locfileid: "82928697"
---
# <a name="overview-of-autoscale-in-microsoft-azure"></a>Microsoft Azure 中的自动缩放概述
本文介绍了 Microsoft Azure 自动缩放是什么、其对用户的好处，以及如何开始使用它。  

Azure Monitor 自动缩放仅适用于[虚拟机规模集](https://azure.microsoft.com/services/virtual-machine-scale-sets/)、[云服务](https://azure.microsoft.com/services/cloud-services/)、[应用服务 Web 应用](https://azure.microsoft.com/services/app-service/web/)、 [API 管理服务](https://docs.microsoft.com/azure/api-management/api-management-key-concepts)和[Azure 数据资源管理器群集](https://docs.microsoft.com/azure/data-explorer/)。

> [!NOTE]
> Azure 有两个自动缩放方法。 旧版自动缩放适用于虚拟机（可用性集）。 此功能的支持有限。若要获取更快速、更可靠的自动缩放支持，建议迁移到虚拟机规模集。 本文提供了此旧技术的使用方法链接。  
>

## <a name="what-is-autoscale"></a>自动缩放是什么？
自动缩放是指在处理应用程序负载时让适当数量的资源运行。 当负载增加时，它可以添加资源来处理增加的负载；当资源空闲时，它可以删除资源以节省资金。 可以指定需要运行的实例的最小数目和最大数目，根据规则集自动添加或删除 VM。 设置最小数目可确保应用程序在没有负载的情况下也会运行。 设置最大数目是为了限制每小时可能会引发的总成本。 可以使用创建的规则在这两种极限之间自动缩放。

 ![描述的自动缩放。 添加和删除 VM](./media/autoscale-overview/AutoscaleConcept.png)

当满足规则条件时，将触发一个或多个自动缩放操作。 可以添加和删除虚拟机，或执行其他操作。 下面的概念关系图演示了此过程。  

 ![自动缩放流关系图](./media/autoscale-overview/Autoscale_Overview_v4.png)

以下说明适用于上面的关系图的各个部分。   

## <a name="resource-metrics"></a>资源指标
资源会发出指标，这些指标随后通过规则进行处理。 指标通过不同方法发出。
虚拟机规模集使用 Azure 诊断代理提供的遥测数据，而 Web 应用和云服务的遥测则直接来自 Azure 基础结构。 一些常用的统计信息包括：CPU 使用率、内存使用情况、线程计数、队列长度和磁盘使用情况。 如需可用遥测数据的列表，请参阅[自动缩放常用指标](../../azure-monitor/platform/autoscale-common-metrics.md)。

## <a name="custom-metrics"></a>自定义指标
还可以利用应用程序可以发出的自己的自定义指标。 如果已将应用程序配置为向 Application Insights 发送指标，则可以利用这些指标来决定是否要进行缩放。

## <a name="time"></a>时间
基于计划的规则以 UTC 为基础。 设置规则时，必须正确设置时区。  

## <a name="rules"></a>规则
此图仅显示了一个自动缩放规则，但用户可以有许多个这样的规则。 可以根据自身情况所需创建复杂的、互相重叠的规则。  规则类型包括  

* **基于指标** - 例如，在 CPU 使用率超出 50% 时执行该操作。
* **基于时间** - 例如，在给定时区的每个星期六的早晨 8 点触发 webhook。

基于指标的规则会衡量应用程序负载，根据负载添加或删除 VM。 如果使用基于计划的规则，则当用户看到负载时间模式并且想要在负载可能升高或降低之前进行缩放时，完成相应的缩放。  

## <a name="actions-and-automation"></a>操作和自动化
规则可以触发一个或多个类型的操作。

* **缩放** - 扩大或缩小 VM
* **电子邮件** - 将电子邮件发送给订阅管理员、共同管理员和/或指定的其他电子邮件地址
* **通过 webhook 自动操作** - 调用 webhook，在 Azure 内外触发多个复杂操作。 在 Azure 中，可以启动 Azure 自动化 runbook、Azure 函数或 Azure 逻辑应用。 在 Azure 外的第三方 URL 示例包含 Slack 和 Twilio 之类的服务。

## <a name="autoscale-settings"></a>自动缩放设置
自动缩放使用下列术语和结构。

- **自动缩放设置**：由自动缩放引擎读取，用于确定是进行扩展还是缩减。 它包含一个或多个配置文件、目标资源的信息，以及通知设置。

  - **自动缩放配置文件**是以下项的组合：

    - **容量设置**：表示实例数的最小值、最大值和默认值。
    - **规则集**，其中的每个规则都包括一个触发器（时间或指标）和一个缩放操作（增加或缩减）。
    - **定期**：表示自动缩放应在何时使此配置文件生效。

      可以有多个配置文件，以应对不同的互相重叠的要求。 例如，可以针对一天中的不同时间或者一周中的不同日期设置不同的自动缩放配置文件。

  - **通知设置**：定义在发生自动缩放事件时应发送的通知，前提是符合某个自动缩放设置的配置文件的条件。 自动缩放可以将通知发送到一个或多个电子邮件地址，也可以对一个或多个 webhook 进行调用。


![Azure 自动缩放设置、配置文件和规则结构](./media/autoscale-overview/AzureResourceManagerRuleStructure3.png)

[自动缩放 REST API](https://msdn.microsoft.com/library/dn931928.aspx) 中提供了可配置字段和说明的完整列表。

有关代码示例，请参阅

* [使用 VM 规模集资源管理器模板的高级自动缩放配置](../../azure-monitor/platform/autoscale-virtual-machine-scale-sets.md)  
* [自动缩放 REST API](https://msdn.microsoft.com/library/dn931953.aspx)

## <a name="horizontal-vs-vertical-scaling"></a>水平缩放和垂直缩放
自动缩放仅以横向方式调整资源的规模，即只增加（“放”）或减少（“缩”）VM 实例的数目。  横向缩放在使用云服务的情况下更为灵活，因为这样可以运行数千个处理负载的 VM。

纵向缩放与此不同。 它保持 VM 数量不变，但会增强（“提高”）或削弱（“降低”）VM 的功能。 幂按内存、CPU 速度、磁盘空间等度量。 垂直缩放具有更多限制。 具体取决于更大型硬件的可用性，此可用性会快速达到上限，并因区域而异。 纵向缩放通常还需要停止和重新启动 VM。

## <a name="methods-of-access"></a>访问方法
可以通过以下方式设置自动缩放：

* [Azure 门户](../../azure-monitor/platform/autoscale-get-started.md)
* [PowerShell](../../azure-monitor/platform/powershell-quickstart-samples.md#create-and-manage-autoscale-settings)
* [跨平台的命令行接口 (CLI)](../../azure-monitor/platform/cli-samples.md#autoscale)
* [Azure 监视器 REST API](https://msdn.microsoft.com/library/azure/dn931953.aspx)

## <a name="supported-services-for-autoscale"></a>支持进行自动缩放的服务
| 服务 | 架构和文档 |
| --- | --- |
| Web 应用 |[缩放 Web 应用](../../azure-monitor/platform/autoscale-get-started.md) |
| 云服务 |[自动缩放云服务](../../cloud-services/cloud-services-how-to-scale-portal.md) |
| 虚拟机：经典 |[缩放经典虚拟机可用性集](https://blogs.msdn.microsoft.com/kaevans/2015/02/20/autoscaling-azurevirtual-machines/) |
| 虚拟机：Windows 规模集 |[缩放 Windows 中的虚拟机规模集](../../virtual-machine-scale-sets/tutorial-autoscale-powershell.md) |
| 虚拟机：Linux 规模集 |[缩放 Linux 中的虚拟机规模集](../../virtual-machine-scale-sets/tutorial-autoscale-cli.md) |
| 虚拟机：Windows 示例 |[使用 VM 规模集资源管理器模板的高级自动缩放配置](../../azure-monitor/platform/autoscale-virtual-machine-scale-sets.md) |
| API 管理服务|[自动缩放 Azure API 管理实例](https://docs.microsoft.com/azure/api-management/api-management-howto-autoscale)
| Azure 数据资源管理器群集|[管理 Azure 数据资源管理器群集缩放以适应不断变化的需求](https://docs.microsoft.com/azure/data-explorer/manage-cluster-horizontal-scaling)|
| Azure 应用服务 |[在 Azure 应用服务中纵向扩展应用](https://docs.microsoft.com/azure/app-service/manage-scale-up)|
| 逻辑应用 |[添加 integration service 环境（ISE）容量](https://docs.microsoft.com/azure/logic-apps/ise-manage-integration-service-environment#add-ise-capacity)|
## <a name="next-steps"></a>后续步骤
若要详细了解自动缩放，请使用前面列出的自动缩放演练，或参阅以下资源：

* [Azure 监视器自动缩放常用指标](../../azure-monitor/platform/autoscale-common-metrics.md)
* [Azure 监视器自动缩放的最佳做法](../../azure-monitor/platform/autoscale-best-practices.md)
* [使用自动缩放操作发送电子邮件和 webhook 警报通知](../../azure-monitor/platform/autoscale-webhook-email.md)
* [自动缩放 REST API](https://msdn.microsoft.com/library/dn931953.aspx)
* [虚拟机规模集自动缩放疑难解答](../../virtual-machine-scale-sets/virtual-machine-scale-sets-troubleshoot.md)

