---
title: 使用 Azure 防火墙工作簿监视日志
description: Azure 防火墙工作簿为 Azure 防火墙数据分析提供了一个灵活的画布，并在 Azure 门户中创建了丰富的视觉对象报表。
services: firewall
author: gopimsft
ms.service: firewall
ms.topic: how-to
ms.date: 09/22/2020
ms.author: victorh
ms.openlocfilehash: dabe154535d2bd3ae5550d4bbbafa75adc5ae2c0
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "91341717"
---
# <a name="monitor-logs-using-azure-firewall-workbook"></a>使用 Azure 防火墙工作簿监视日志

Azure 防火墙工作簿为 Azure 防火墙数据分析提供了一个灵活的画布。 可以使用它在 Azure 门户中创建丰富的视觉对象报表。 你可以在 Azure 中点击多个部署的防火墙，并将其合并为统一的交互式体验。

你可以深入了解 Azure 防火墙事件，了解你的应用程序和网络规则，并查看跨 Url、端口和地址的防火墙活动的统计信息。 使用 Azure 防火墙工作簿可以筛选防火墙和资源组，并在调查日志中的问题时，使用易于阅读的数据集对每个类别进行动态筛选。 

## <a name="prerequisites"></a>必备条件

在开始之前，应通过 Azure 门户 [启用诊断日志记录](firewall-diagnostics.md#enable-diagnostic-logging-through-the-azure-portal) 。 此外，请阅读 [Azure 防火墙日志和指标](logs-and-metrics.md) ，了解 azure 防火墙的诊断日志和指标的概述。

## <a name="get-started"></a>入门

若要部署工作簿，请参阅 [Azure 防火墙 Azure Monitor 工作簿](https://github.com/Azure/Azure-Network-Security/tree/master/Azure%20Firewall/Azure%20Monitor%20Workbook) ，并按照页面上的说明进行操作。 Azure 防火墙工作簿旨在跨多个租户和多个订阅工作，并且可筛选为多个防火墙。

## <a name="overview-page"></a>概述页

"概述" 页提供了跨工作区、时间和防火墙进行筛选的方法。 它按时间跨防火墙和日志类型显示事件 (应用程序、网络、威胁 intel、DNS proxy) 。

:::image type="content" source="./media/firewall-workbook/firewall-workbook-overview.png" alt-text="Azure 防火墙工作簿概述":::

## <a name="application-rule-log-statistics"></a>应用程序规则日志统计信息

此页显示了一段时间内 IP 地址的唯一源、应用程序规则计数使用情况、已拒绝/允许的 FQDN （一段时间）以及筛选后的数据。 可以基于 IP 地址筛选数据。

:::image type="content" source="./media/firewall-workbook/firewall-workbook-application-rule.png" alt-text="Azure 防火墙工作簿概述":::

## <a name="network-rule-log-statistics"></a>网络规则日志统计信息

此页提供按规则操作-允许/拒绝、目标端口按 IP 和 DNAT 进行查看的视图。 还可以按操作、端口和目标类型进行筛选。

:::image type="content" source="./media/firewall-workbook/firewall-workbook-network-rule.png" alt-text="Azure 防火墙工作簿概述":::

你还可以基于时间范围筛选日志：

:::image type="content" source="./media/firewall-workbook/firewall-workbook-network-rule-time.png" alt-text="Azure 防火墙工作簿概述":::

## <a name="investigations"></a>调查

可以查看日志，并根据源 IP 地址了解资源的详细信息。 可以获取虚拟机名称和网络接口名称等信息。 从日志筛选到资源很简单。

:::image type="content" source="./media/firewall-workbook/firewall-workbook-investigation.png" alt-text="Azure 防火墙工作簿概述":::

## <a name="next-steps"></a>后续步骤

- 了解有关[Azure 防火墙诊断](firewall-diagnostics.md)的详细信息