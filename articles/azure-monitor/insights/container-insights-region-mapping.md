---
title: 容器区域映射的 Azure Monitor
description: 介绍容器 Azure Monitor 之间支持的区域映射，Log Analytics 工作区和自定义度量值。
ms.topic: conceptual
ms.date: 09/22/2020
ms.custom: references_regions
ms.openlocfilehash: 9e3b8635c70dfdf33b0a062be80c948cd77923cc
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "91272899"
---
# <a name="region-mappings-supported-by-azure-monitor-for-containers"></a>容器 Azure Monitor 支持区域映射

 为容器启用 Azure Monitor 时，仅支持某些区域以链接 Log Analytics 工作区和 AKS 群集，以及收集提交到 Azure Monitor 的自定义指标。

## <a name="log-analytics-workspace-supported-mappings"></a>Log Analytics 支持工作区的映射

受支持的 AKS 区域在 [按区域提供的产品](https://azure.microsoft.com/global-infrastructure/services/?products=kubernetes-service)中列出。 Log Analytics 工作区必须位于同一区域，但下表中列出的区域除外。 观看 [AKS 发行说明](https://github.com/Azure/AKS/releases) 以获取更新。


|**AKS 群集区域** | **Log Analytics 工作区区域** |
|-----------------------|------------------------------------|
|**非洲** | |
|SouthAfricaNorth |西欧 |
|SouthAfricaWest |西欧 |
|**澳大利亚** | |
|AustraliaCentral2 |AustraliaCentral |
|**巴西** | |
|BrazilSouth | Default-machinelearning-southcentralus |
|**加拿大** ||
|CanadaEast |CanadaCentral |
|**欧洲** | |
|FranceSouth |FranceCentral |
|UKWest |UKSouth |
|**印度** | |
|SouthIndia |CentralIndia |
|WestIndia |CentralIndia |
|**日本** | |
|JapanWest |JapanEast |
|**韩国** | |
|KoreaSouth |KoreaCentral |
|**美国** | |
|WestCentralUS<sup>1</sup>|EastUS<sup>1</sup>|


<sup>1</sup> 由于容量限制，区域在创建新资源时不可用。 这包括 Log Analytics 工作区。 但是，区域中预先存在的链接资源应该继续工作。

## <a name="custom-metrics-supported-regions"></a>自定义指标支持区域

仅支持在以下 [azure 区域](../platform/metrics-custom-overview.md#supported-regions)中以自定义指标的形式从 Azure Kubernetes SERVICES (AKS) 群集节点和 pod 收集指标。

## <a name="next-steps"></a>后续步骤

若要开始监视 AKS 群集，请查看[如何为容器启用 Azure Monitor](container-insights-onboard.md) 以了解启用监视的要求和可用方法。  
