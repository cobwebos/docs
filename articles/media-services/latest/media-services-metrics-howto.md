---
title: 使用 Azure Monitor 查看指标
description: 本文介绍如何使用 Azure 门户的图表和 Azure CLI 监视指标。
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/08/2019
ms.author: juliako
ms.openlocfilehash: d331dc4eb0c6668426e1ab1a01a1dd1dcebe0c85
ms.sourcegitcommit: 66237bcd9b08359a6cce8d671f846b0c93ee6a82
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/11/2019
ms.locfileid: "67795830"
---
# <a name="monitor-media-services-metrics"></a>监视媒体服务指标 

[Azure 监视器](../../azure-monitor/overview.md)允许你监视指标和诊断日志，帮助您理解如何执行你的应用程序。 有关详细说明此功能和要了解为什么你想要使用 Azure 媒体服务指标和诊断日志，请参阅[监视媒体服务指标和诊断日志](media-services-metrics-diagnostic-logs.md)。

Azure Monitor 提供多种方式来与指标，包括在门户中制作指标图表、 通过 REST API 访问这些或查询它们交互使用 CLI。 本文介绍如何使用 Azure 门户的图表和 Azure CLI 监视指标。

## <a name="prerequisites"></a>先决条件

- [创建媒体服务帐户](create-account-cli-how-to.md)
- 查看[监视媒体服务指标和诊断日志](media-services-metrics-diagnostic-logs.md)

## <a name="view-metrics-in-azure-portal"></a>在 Azure 门户中查看指标

1. 通过 https://portal.azure.com 登录到 Azure 门户。
1. 导航到你的 Azure 媒体服务帐户并选择**指标**。
1. 单击**资源**框并选择你想要监视指标的资源。 

    **选择一个资源**到你的资源可用列表右侧显示的窗口。 在这种情况下，请参阅 

    * &lt;媒体服务帐户名称&gt;
    * &lt;媒体服务帐户名&gt;/&lt;流式处理终结点名称&gt;
    * &lt;存储帐户名称&gt;

    选择资源，然后按**应用**。 有关受支持的资源和指标的详细信息，请参阅[监视媒体服务指标](media-services-metrics-diagnostic-logs.md)。
 
    ![度量值](media/media-services-metrics/metrics02.png)
    
    > [!NOTE]
    > 若要切换想要监视指标的资源，请单击**资源**再次并重复此步骤。
1. （可选） 为您的图表提供一个名称 （通过按顶部的铅笔名称编辑）。
1. 添加你想要查看的度量值。

    ![度量值](media/media-services-metrics/metrics03.png)
1. 可以将图表固定到仪表板。

## <a name="view-metrics-with-azure-cli"></a>使用 Azure CLI 查看指标

若要使用 CLI"出口"度量值，将运行以下`az monitor metrics`CLI 命令：

```cli
az monitor metrics list --resource \
   "/subscriptions/<subscription id>/resourcegroups/<resource group name>/providers/Microsoft.Media/mediaservices/<Media Services account name>/streamingendpoints/<streaming endpoint name>" \
   --metric "Egress"
```

若要获取其他度量值，替换你感兴趣的指标名称为"出口"。

## <a name="see-also"></a>请参阅

* [Azure Monitor 指标](../../azure-monitor/platform/data-platform.md)
* [创建、 查看和管理使用 Azure Monitor 的指标警报](../../azure-monitor/platform/alerts-metric.md)。

## <a name="next-steps"></a>后续步骤

[诊断日志](media-services-diagnostic-logs-howto.md)
