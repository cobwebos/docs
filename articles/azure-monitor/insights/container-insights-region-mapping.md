---
title: 容器区域映射的 Azure 监视器
description: 本文介绍了容器 Azure 监视器、日志分析工作区和自定义指标之间支持的区域映射。
ms.topic: conceptual
ms.date: 06/26/2019
ms.openlocfilehash: a058f9cac987bb5c7130019f50370c6a176b09ac
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/27/2020
ms.locfileid: "75403427"
---
# <a name="region-mappings-supported-by-azure-monitor-for-containers"></a>Azure 监视器支持的容器区域映射

 为容器启用 Azure 监视器时，仅支持某些区域链接日志分析工作区和 AKS 群集，以及收集提交到 Azure 监视器的自定义指标。

## <a name="log-analytics-workspace-supported-mappings"></a>日志分析工作区支持的映射

AKS 群集资源或日志分析工作区可以驻留在其他区域，下表显示了我们的映射。

|**AKS 群集区域** | **日志分析工作区区域** |
|-----------------------|------------------------------------|
|**非洲** | |
|南非北部 |西欧 |
|南非西部 |西欧 |
|**澳大利亚** | |
|澳大利亚东部 |澳大利亚东部 |
|澳大利亚中部 |澳大利亚中部 |
|澳大利亚中心2 |澳大利亚中部 |
|澳大利亚东部 |澳大利亚东部 |
|**亚太** | |
|EastAsia |EastAsia |
|SoutheastAsia |SoutheastAsia |
|**巴西** | |
|巴西南部 | 中南部 |
|**Canada** ||
|CanadaCentral |CanadaCentral |
|CanadaEast |CanadaCentral |
|**欧洲** | |
|法国中部 |法国中部 |
|法国南部 |法国中部 |
|北欧 |北欧 |
|UKSouth |UKSouth |
|英国西部 |UKSouth |
|西欧 |西欧 |
|**印度** | |
|CentralIndia |CentralIndia |
|SouthIndia |CentralIndia |
|WestIndia |CentralIndia |
|**日本** | |
|JapanEast |JapanEast |
|日本西部 |JapanEast |
|**韩国** | |
|KoreaCentral |KoreaCentral |
|KoreaSouth |KoreaCentral |
|**我们** | |
|中央 |中央|
|EastUS |EastUS |
|EastUS2 |EastUS2 |
|韦斯特乌斯 |韦斯特乌斯 |
|美国西部 2 |美国西部 2 |
|西中乌斯<sup>1</sup>|东US<sup>1</sup>|
|US Gov 弗吉尼亚州 |US Gov 弗吉尼亚州 |

<sup>1</sup>由于容量限制，在创建新资源时该区域不可用。 这包括日志分析工作区。 然而，该区域已有的相互联系资源应继续发挥作用。

## <a name="custom-metrics-supported-regions"></a>受支持的自定义指标区域

支持从 Azure Kubernetes 服务 （AKS） 群集节点和 pod 收集指标，仅支持在以下[Azure 区域](../platform/metrics-custom-overview.md#supported-regions)中作为自定义指标发布。

## <a name="next-steps"></a>后续步骤

若要开始监视 AKS 群集，请查看[如何为容器启用 Azure Monitor](container-insights-onboard.md) 以了解启用监视的要求和可用方法。  
