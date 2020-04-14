---
title: 使用资源运行状况监视 Azure 数据资源管理器
description: 使用 Azure 资源运行状况监视 Azure 数据资源管理器资源。
author: orspod
ms.author: orspodek
ms.reviewer: prvavill
ms.service: data-explorer
ms.topic: conceptual
ms.date: 03/31/2020
ms.openlocfilehash: ee42f45d8c96e908061142b5a8e6b2e5cee21993
ms.sourcegitcommit: 8dc84e8b04390f39a3c11e9b0eaf3264861fcafc
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/13/2020
ms.locfileid: "81262054"
---
# <a name="monitor-azure-data-explorer-using-resource-health-preview"></a>使用资源运行状况监视 Azure 数据资源管理器（预览）

Azure 数据资源管理器[的资源运行状况](/azure/service-health/resource-health-overview)会通知您 Azure 数据资源管理器资源的运行状况，并提供可操作的建议来解决问题。 资源运行状况每 1-2 分钟更新一次，并报告资源的当前运行状况和过去运行状况。 

资源运行状况通过检查各种运行状况检查（如：
* 资源可用性
* 查询失败

## <a name="access-resource-health-reporting"></a>访问资源运行状况报告

1. 在[Azure 门户](https://portal.azure.com/)中，从服务列表中选择 **"监视器**"。
1. 选择**服务运行状况** > **资源运行状况**。
1. 在 **"订阅"** 下拉列表中，选择您的订阅。 在 **"资源类型**"下拉下拉下，选择**Azure 数据资源管理器**。
1. 生成的表列出所选订阅中的所有资源。 每个资源都将有一个运行状况状态图标，指示资源运行状况。
1. 选择您的资源以查看其[资源运行状况和建议](#resource-health-status)。

    ![概述](media/monitor-with-resource-health/resource-health-overview.png)

## <a name="resource-health-status"></a>资源运行状况

将显示资源的运行状况，并显示以下状态之一，可用、不可用和未知。

### <a name="available"></a>可用

"**可用"** 的运行状况表示 Azure 数据资源管理器资源状况良好且没有任何问题。

![可用](media/monitor-with-resource-health/available.png)

### <a name="unavailable"></a>不可用

运行状况为 **"不可用**"表示 Azure 数据资源管理器资源存在持续问题，导致它无法用于查询和引入。 例如，Azure 数据资源管理器资源中的节点可能意外重新启动。 如果 Azure 数据资源管理器资源长时间处于此状态，请与支持人员联系。

![不可用](media/monitor-with-resource-health/unavailable.png)

### <a name="unknown"></a>未知

运行状况为 **"未知**"表示**资源运行状况**已超过 10 分钟未收到有关此 Azure 数据资源管理器资源的信息。 此状态不是 Azure 数据资源管理器资源运行状况的明确指示，而是故障排除过程中的重要数据点。 如果 Azure 数据资源管理器群集按预期运行，则状态将在几分钟内更改为 **"可用**"。 **未知**运行状况可能表示平台中的事件正在影响资源。 

> [!TIP]
> Azure 数据资源管理器群集资源运行状况如果处于已停止状态，则为 **"未知**"。

![未知](media/monitor-with-resource-health/unknown.png)

## <a name="historical-information"></a>历史信息

在 **"资源运行状况**>**运行状况历史记录**"中，访问长达四周的资源运行状况状态信息。 选择箭头，获取有关此窗格中报告的运行状况事件问题的其他信息。 

![历史记录](media/monitor-with-resource-health/healthhistory.png)

## <a name="next-steps"></a>后续步骤

* [配置资源运行状况警报](https://docs.microsoft.com/azure/service-health/resource-health-alert-arm-template-guide)
* [教程：在 Azure 数据资源管理器中引入和查询监视数据](ingest-data-no-code.md)
* [通过指标监视 Azure 数据资源管理器的性能、运行状况和使用情况](using-metrics.md)