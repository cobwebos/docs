---
title: 使用 Azure 监视器查看指标
description: 本文演示如何使用 Azure 门户图表和 Azure CLI 监视指标。
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
ms.openlocfilehash: c230e1e950bb924631032940642a6202acf4ade8
ms.sourcegitcommit: e040ab443f10e975954d41def759b1e9d96cdade
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/29/2020
ms.locfileid: "80382930"
---
# <a name="monitor-media-services-metrics"></a>监视媒体服务指标

[Azure 监视器](../../azure-monitor/overview.md)使您能够监视指标和诊断日志，以帮助您了解应用程序的性能。 有关此功能的详细说明，并了解为什么要使用 Azure 媒体服务指标和诊断日志，请参阅[监视媒体服务指标和诊断日志](media-services-metrics-diagnostic-logs.md)。

Azure 监视器提供了几种与指标进行交互的方法，包括在门户中绘制指标的图表、通过 REST API 访问指标或使用 Azure CLI 查询指标。 本文演示如何使用 Azure 门户图表和 Azure CLI 监视指标。

## <a name="prerequisites"></a>先决条件

- [创建媒体服务帐户](create-account-cli-how-to.md)
- 查看[监视器媒体服务指标和诊断日志](media-services-metrics-diagnostic-logs.md)

## <a name="view-metrics-in-azure-portal"></a>在 Azure 门户中查看指标

1. 通过 https://portal.azure.com 登录到 Azure 门户。
1. 导航到 Azure 媒体服务帐户并选择**指标**。
1. 单击 **"资源"** 框并选择要监视指标的资源。

    "**选择资源**"窗口显示在右侧，并列出可供您使用的资源。 在这种情况下，您将看到：

    * &lt;媒体服务帐户名称&gt;
    * &lt;媒体服务帐户名称&gt;/&lt;流式处理终结点名称&gt;
    * &lt;存储帐户名称&gt;

    选择资源并按 **"应用**"。 有关受支持的资源和指标的详细信息，请参阅[监视媒体服务指标](media-services-metrics-diagnostic-logs.md)。

    ![指标](media/media-services-metrics/metrics02.png)

    > [!NOTE]
    > 要在要监视指标的资源之间切换，请再次单击 **"资源"** 框并重复此步骤。
1. （可选）为您的图表指定一个名称（通过按下顶部的铅笔编辑名称）。
1. 添加要查看的指标。

    ![指标](media/media-services-metrics/metrics03.png)
1. 您可以将图表固定到仪表板。

## <a name="view-metrics-with-azure-cli"></a>使用 Azure CLI 查看指标

要使用 Azure CLI 获取"出口"指标，将运行以下`az monitor metrics`命令：

```azurecli-interactive
az monitor metrics list --resource \
   "/subscriptions/<subscription id>/resourcegroups/<resource group name>/providers/Microsoft.Media/mediaservices/<Media Services account name>/streamingendpoints/<streaming endpoint name>" \
   --metric "Egress"
```

要获取其他指标，将"出口"替换为您感兴趣的指标名称。

## <a name="see-also"></a>请参阅

* [Azure Monitor 指标](../../azure-monitor/platform/data-platform.md)
* [使用 Azure 监视器 创建、查看和管理指标警报](../../azure-monitor/platform/alerts-metric.md)。

## <a name="next-steps"></a>后续步骤

[诊断日志](media-services-diagnostic-logs-howto.md)
