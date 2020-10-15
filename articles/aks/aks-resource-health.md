---
title: '检查影响 AKS 群集 (预览版资源运行状况事件) '
description: 使用 Azure 资源运行状况检查 AKS 群集的运行状况状态。
services: container-service
author: yunjchoi
ms.topic: troubleshooting
ms.date: 08/18/2020
ms.author: yunjchoi
ms.openlocfilehash: a409ac944c51d893fc344f82ae83d97559e055ef
ms.sourcegitcommit: a92fbc09b859941ed64128db6ff72b7a7bcec6ab
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/15/2020
ms.locfileid: "92070650"
---
# <a name="check-for-resource-health-events-impacting-your-aks-cluster-preview"></a>检查影响 AKS 群集 (预览版资源运行状况事件) 


在 AKS 上运行容器工作负荷时，需要确保在出现问题时尽快进行故障排除并解决问题，以最大程度地降低对工作负荷的可用性的影响。 [Azure 资源运行状况](../service-health/resource-health-overview.md) 使你可以查看各种运行状况事件，这些事件可能会导致 AKS 群集不可用。

[!INCLUDE [preview features callout](./includes/preview/preview-callout.md)]

## <a name="open-resource-health"></a>打开资源运行状况

### <a name="to-access-resource-health-for-your-aks-cluster"></a>若要访问 AKS 群集的资源运行状况：

- 在 [Azure 门户](https://portal.azure.com)中导航到 AKS 群集。
- 在左侧导航栏中选择 " **资源运行状况** "。

### <a name="to-access-resource-health-for-all-clusters-on-your-subscription"></a>要访问订阅中所有群集的资源运行状况：

- 在[Azure 门户](https://portal.azure.com)中搜索 "服务运行状况"，并导航到该**服务运行状况**。
- 选择左侧导航栏中的 " **资源运行状况** "。
- 选择订阅，并将资源类型设置为 Azure Kubernetes Service (AKS) 。

![屏幕截图显示 A K S 群集的资源运行状况。](./media/aks-resource-health/resource-health-check.png)

## <a name="check-the-health-status"></a>检查运行状况状态

Azure 资源运行状况有助于在服务问题影响到 Azure 资源时进行诊断和获取支持。 资源运行状况报告资源当前和过去的运行状况，并帮助你确定问题是否是由用户启动的操作或平台事件引起的。

资源运行状况接收托管群集的信号，以确定群集的运行状况。 它会检查 AKS 群集的运行状况状态，并报告每个运行状况信号所需的操作。 这些信号包括自动解决问题、计划的更新、计划外的运行状况事件以及由用户启动的操作导致的不可用。 这些信号使用 Azure 资源运行状况的运行状况状态进行分类： *可用*、 *不可用*、 *未知*和 *降级*。

- **可用**：当没有任何已知问题影响群集的运行状况时，资源运行状况会将群集报告为 *可用*。

- **不可用**：当存在影响群集运行状况的平台或非平台事件时，资源运行状况会将群集报告为 *不可用*。

- **未知**：群集的运行状况指标发生暂时的连接丢失时，资源运行状况将群集报告为 " *未知*"。

- **降级**：当存在要求你的操作的运行状况问题时，资源运行状况会将你的群集报告为 *降级*。

有关每个运行状况状态指示的详细信息，请访问 [资源运行状况概述](../service-health/resource-health-overview.md#health-status)。

### <a name="view-historical-data"></a>查看历史数据

你还可以在 " **运行状况历史记录** " 部分中查看过去30天的历史资源运行状况信息。

## <a name="next-steps"></a>后续步骤

在群集上运行检查，通过使用 [AKS 诊断](./concepts-diagnostics.md)进一步排查群集问题。