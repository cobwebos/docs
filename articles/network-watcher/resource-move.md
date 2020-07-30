---
title: 移动 Azure 网络观察程序资源 |Microsoft Docs
description: 跨区域移动 Azure 网络观察程序资源
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
ms.date: 07/27/2020
ms.author: damendo
ms.openlocfilehash: 97349071fee6a95623e5b5efdc0c9818cfe7b811
ms.sourcegitcommit: 5b8fb60a5ded05c5b7281094d18cf8ae15cb1d55
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/29/2020
ms.locfileid: "87388370"
---
# <a name="moving-azure-network-watcher-resources-across-regions"></a>跨区域移动 Azure 网络观察程序资源

## <a name="moving-the-network-watcher-resource"></a>移动网络观察程序资源
网络观察程序资源代表网络观察程序的后端服务，由 Azure 完全托管。 客户无需管理它。 此资源不支持移动操作。

## <a name="moving-child-resources-of-network-watcher"></a>移动网络观察程序的子资源
对于资源类型的任何子资源，当前不支持在区域之间移动资源 `*networkWatcher*` 。

## <a name="next-steps"></a>后续步骤
* 阅读[网络观察程序概述](https://docs.microsoft.com/azure/network-watcher/network-watcher-monitoring-overview)
* 请参阅[网络观察程序常见问题](https://docs.microsoft.com/azure/network-watcher/frequently-asked-questions)
