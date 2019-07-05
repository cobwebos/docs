---
title: Azure 监视的容器区域映射
description: 这篇文章介绍支持 Azure 监视的容器、 Log Analytics 工作区和自定义指标之间的区域映射。
services: azure-monitor
ms.service: azure-monitor
ms.workload: infrastructure-services
author: mgoedtel
ms.author: magoedte
ms.date: 06/26/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 481a2a400be4e983e0a2337a200324061494efa1
ms.sourcegitcommit: 6cb4dd784dd5a6c72edaff56cf6bcdcd8c579ee7
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/02/2019
ms.locfileid: "67518074"
---
# <a name="region-mappings-supported-by-azure-monitor-for-containers"></a>适用于容器的 Azure Monitor 支持的区域映射

 在 Azure Monitor 启用容器时，只有特定区域支持在链接的 Log Analytics 工作区和 AKS 群集，而收集自定义度量值提交到 Azure Monitor。

## <a name="log-analytics-workspace-supported-mappings"></a>Log Analytics 工作区支持的映射

AKS 群集资源或 Log Analytics 工作区可以驻留在其他区域，而且下表显示了我们的映射。

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
|**Canada** ||
|CanadaCentral |CanadaCentral |
|CanadaEast |CanadaCentral |
|**欧洲** | |
|FranceCentral |FranceCentral |
|FranceSouth |FranceCentral |
|NorthEurope |NorthEurope |
|UKSouth |UKSouth |
|Ukwest |UKSouth |
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

<sup>1</sup>由于容量限制范围，而该区域不可用时创建新的资源。 这包括 Log Analytics 工作区。 但是，在区域中预先存在链接的资源应继续工作。

## <a name="custom-metrics-supported-regions"></a>自定义指标支持的区域

收集度量值从 Azure Kubernetes 服务 (AKS) 群集节点和 pod 支持用于发布为仅在以下自定义指标[Azure 区域](../platform/metrics-custom-overview.md#supported-regions)。

## <a name="next-steps"></a>后续步骤

若要开始监视你的 AKS 群集，请查看[如何启用针对容器的 Azure 监视器](container-insights-onboard.md)以了解要求和可用的方法来启用监视。  