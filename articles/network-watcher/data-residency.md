---
title: Azure 网络观察程序的数据驻留 |Microsoft Docs
description: 了解网络观察程序服务的数据驻留
services: network-watcher
documentationcenter: na
author: damendo
manager: ''
editor: ''
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 07/20/2020
ms.author: damendo
ms.openlocfilehash: 14b4d3568c129c77260b00d554db520809dfd670
ms.sourcegitcommit: d8b8768d62672e9c287a04f2578383d0eb857950
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/11/2020
ms.locfileid: "88068120"
---
# <a name="data-residency-for-the-azure-network-watcher"></a>Azure 网络观察程序的数据驻留
除了连接监视器 (预览) 服务之外，Azure 网络观察程序不会存储客户数据。


## <a name="connection-monitor-preview-data-residency"></a>连接监视器 (预览) 数据驻留
*连接监视器 (预览版) *服务存储客户数据。 此数据由网络观察程序自动存储在单个区域中。 因此，*连接监视器 (预览) *自动满足区域内数据派驻要求，包括在[信任中心](https://azuredatacentermap.azurewebsites.net/)中指定的要求。

## <a name="singapore-data-residency"></a>新加坡数据驻留

在 Azure 中，启用将客户数据存储在单个区域中的功能目前仅适用于亚太地域 (新加坡) 。 对于所有其他区域，客户数据存储在异地。 有关详细信息，请参阅[信任中心](https://azuredatacentermap.azurewebsites.net/)。

> [!Note]
> **先前复制**客户可以选择使用其网络观察程序实例存储最终用户 IP 地址，以便网络观察程序可以监视与最终用户 IP 地址相关的可访问性、延迟和网络拓扑更改。 这些最终用户 IP 地址可能会分类为客户数据。 从2020年7月15日起，网络观察程序将此数据存储在单个区域中。  (客户数据不再复制到 HK。 ) 此数据不再复制到 HK。 此客户数据为，并已加密为静态。
> 
> 如果此客户数据被第三方访问，这将允许第三方了解 IP 地址，但不会向第三方授予对与该 IP 地址关联的计算机或设备的访问权限。 网络观察程序认为在 HK 中没有第三方访问此客户数据。

## <a name="next-steps"></a>后续步骤

* 阅读[网络观察](https://docs.microsoft.com/azure/network-watcher/network-watcher-monitoring-overview)程序概述。
