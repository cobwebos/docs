---
title: '配置 Azure Monitor agent (预览版的数据收集) '
description: ''
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 08/19/2020
ms.openlocfilehash: cd29bfafe2d37b6a34031e6962cc27bfff0006c1
ms.sourcegitcommit: ae6e7057a00d95ed7b828fc8846e3a6281859d40
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/16/2020
ms.locfileid: "92108008"
---
# <a name="configure-data-collection-for-the-azure-monitor-agent-preview"></a>配置 Azure Monitor agent (预览版的数据收集) 
数据收集规则 (DCR) 定义传入 Azure Monitor 的数据并指定应发送到的位置。 本文介绍如何创建数据收集规则，以便使用 Azure Monitor 代理从虚拟机收集数据。

有关数据收集规则的完整说明，请参阅 [Azure Monitor (preview) 中的数据收集规则 ](data-collection-rule-overview.md)。

> [!NOTE]
> 本文介绍如何为具有当前预览的 Azure Monitor 代理配置虚拟机数据。 请参阅 [Azure Monitor 代理概述](agents-overview.md) ，了解已正式发布的代理的说明以及如何使用它们来收集数据。


## <a name="dcr-associations"></a>DCR 关联
若要将 DCR 应用到虚拟机，请创建虚拟机的关联。 虚拟机可能与多个 Dcr 存在关联，而 DCR 可能有多个与其关联的虚拟机。 这允许您定义一组 Dcr，每个匹配特定要求，并只将它们应用于它们适用的虚拟机。 

例如，假设某个环境具有一组运行业务线应用程序的虚拟机，另一个虚拟机运行 SQL Server。 你可能有一个适用于所有虚拟机的默认数据收集规则，以及单独收集用于业务线应用程序和 SQL Server 的数据的单独数据收集规则。 虚拟机与数据收集规则的关联将如下图所示。

![关系图显示了托管业务线应用程序和 SQL Server 的虚拟机，这些虚拟机与名为 "central-i t-默认" 的数据收集规则和 "用于业务线的应用程序的数据收集规则"、"SQL Server 中"、"中"、"中"、"中"](media/data-collection-rule-azure-monitor-agent/associations.png)

## <a name="create-using-the-azure-portal"></a>使用 Azure 门户进行创建
你可以使用 Azure 门户来创建数据收集规则并将你的订阅中的虚拟机关联到该规则。 将自动安装 Azure Monitor 代理，并为尚未安装的任何虚拟机创建托管标识。

在 Azure 门户的 " **Azure Monitor** " 菜单中，从 "**设置**" 部分中选择 "**数据收集规则**"。 单击 " **添加** " 以添加新的数据收集规则和分配。

[![数据收集规则](media/azure-monitor-agent/data-collection-rules.png)](media/azure-monitor-agent/data-collection-rules.png#lightbox)

单击 " **添加** " 创建新规则和关联集。 提供 **规则名称** 并指定 **订阅** 和 **资源组**。 这将指定将在其中创建 DCR 的位置。 虚拟机及其关联可以位于租户中的任何订阅或资源组中。

[![数据收集规则基础知识](media/azure-monitor-agent/data-collection-rule-basics.png)](media/azure-monitor-agent/data-collection-rule-basics.png#lightbox)

在 " **虚拟机** " 选项卡中，添加应应用数据收集规则的虚拟机。 将在尚未安装的虚拟机上安装 Azure Monitor 代理。

[![数据收集规则虚拟机](media/azure-monitor-agent/data-collection-rule-virtual-machines.png)](media/azure-monitor-agent/data-collection-rule-virtual-machines.png#lightbox)

在 " **收集和传递** " 选项卡上，单击 " **添加数据源** " 以添加数据源和目标集。 选择 **数据源类型**，将显示相应的详细信息。 对于性能计数器，您可以从一组预定义的对象和采样速率中进行选择。 对于事件，您可以从一组日志或设施中进行选择，也可以从严重性级别中进行选择。 

[![数据源基本](media/azure-monitor-agent/data-collection-rule-data-source-basic.png)](media/azure-monitor-agent/data-collection-rule-data-source-basic.png#lightbox)


若要指定其他日志和性能计数器，请选择 " **自定义**"。 然后，您可以指定要收集的任何特定值的 [XPath ](https://www.w3schools.com/xml/xpath_syntax.asp) 。 有关示例，请参阅 [示例 DCR](data-collection-rule-overview.md#sample-data-collection-rule) 。

[![数据源自定义](media/azure-monitor-agent/data-collection-rule-data-source-custom.png)](media/azure-monitor-agent/data-collection-rule-data-source-custom.png#lightbox)

在 " **目标** " 选项卡上，为数据源添加一个或多个目标。 Windows 事件和 Syslog 数据源只能发送到 Azure Monitor 日志。 性能计数器可以同时发送到 Azure Monitor 度量值和 Azure Monitor 日志。

[![目标](media/azure-monitor-agent/data-collection-rule-destination.png)](media/azure-monitor-agent/data-collection-rule-destination.png#lightbox)

单击 " **添加数据源** "，然后 **查看 "+ 创建** " 查看数据收集规则的详细信息并与 vm 集关联。 单击 " **创建** " 以创建它。

> [!NOTE]
> 创建数据收集规则和关联后，最长可能需要5分钟的时间才能将数据发送到目标。

## <a name="createusingrestapi"></a>使用 REST API 创建
按照以下步骤使用 REST API 创建 DCR 和关联。 
1.使用 [示例 DCR](data-collection-rule-overview.md#sample-data-collection-rule)中所示的 JSON 格式手动创建 DCR 文件。
2.使用 [REST API](/rest/api/monitor/datacollectionrules/create#examples)创建规则。
3.使用 [REST API](/rest/api/monitor/datacollectionruleassociations/create#examples)为每个虚拟机创建与数据收集规则的关联。

## <a name="next-steps"></a>后续步骤

- 详细了解 [Azure Monitor 代理](azure-monitor-agent-overview.md)。
- 了解有关 [数据收集规则](data-collection-rule-overview.md)的详细信息。