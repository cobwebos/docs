---
title: 容器区域映射的 Azure Monitor
description: 本文介绍容器 Azure Monitor、Log Analytics 工作区和自定义指标之间支持的区域映射。
ms.topic: conceptual
ms.date: 06/26/2019
ms.openlocfilehash: a058f9cac987bb5c7130019f50370c6a176b09ac
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/25/2019
ms.locfileid: "75403427"
---
# <a name="region-mappings-supported-by-azure-monitor-for-containers"></a>容器 Azure Monitor 支持区域映射

 为容器启用 Azure Monitor 时，仅支持某些区域以链接 Log Analytics 工作区和 AKS 群集，以及收集提交到 Azure Monitor 的自定义指标。

## <a name="log-analytics-workspace-supported-mappings"></a>Log Analytics 支持工作区的映射

AKS 群集资源或 Log Analytics 工作区可以位于其他区域，下表显示了映射。

|**AKS 群集区域** | **Log Analytics 工作区区域** |
|-----------------------|------------------------------------|
|**非洲** | |
|SouthAfricaNorth |西欧 |
|SouthAfricaWest |西欧 |
|**澳大利亚** | |
|AustraliaEast |AustraliaEast |
|AustraliaCentral |AustraliaCentral |
|AustraliaCentral2 |AustraliaCentral |
|AustraliaEast |AustraliaEast |
|**亚太区** | |
|EastAsia |EastAsia |
|SoutheastAsia |SoutheastAsia |
|**巴西** | |
|BrazilSouth | SouthCentralUS |
|**加拿大** ||
|CanadaCentral |CanadaCentral |
|CanadaEast |CanadaCentral |
|**欧洲** | |
|FranceCentral |FranceCentral |
|FranceSouth |FranceCentral |
|NorthEurope |NorthEurope |
|UKSouth |UKSouth |
|UKWest |UKSouth |
|西欧 |西欧 |
|**印度** | |
|CentralIndia |CentralIndia |
|SouthIndia |CentralIndia |
|WestIndia |CentralIndia |
|**日本** | |
|JapanEast |JapanEast |
|JapanWest |JapanEast |
|**韩国** | |
|KoreaCentral |KoreaCentral |
|KoreaSouth |KoreaCentral |
|**美国** | |
|CentralUS |CentralUS|
|EastUS |EastUS |
|EastUS2 |EastUS2 |
|WestUS |WestUS |
|WestUS2 |WestUS2 |
|WestCentralUS<sup>1</sup>|EastUS<sup>1</sup>|
|美国弗吉尼亚州政府 |美国弗吉尼亚州政府 |

<sup>1</sup>由于容量限制，区域在创建新资源时不可用。 这包括 Log Analytics 工作区。 但是，区域中预先存在的链接资源应该继续工作。

## <a name="custom-metrics-supported-regions"></a>自定义指标支持区域

仅支持在以下[azure 区域](../platform/metrics-custom-overview.md#supported-regions)中收集 Azure Kubernetes SERVICES （AKS）群集节点和 pod 中的指标以作为自定义指标发布。

## <a name="next-steps"></a>后续步骤

若要开始监视 AKS 群集，请查看[如何启用容器 Azure Monitor](container-insights-onboard.md)以了解启用监视的要求和可用方法。  
