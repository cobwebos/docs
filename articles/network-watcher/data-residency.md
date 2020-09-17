---
title: Azure 网络观察程序的数据驻留 |Microsoft Docs
description: 本文将帮助你了解 Azure 网络观察程序服务的数据驻留。
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
ms.openlocfilehash: 9451b6636f2f87806e3d1e39fec4e9e4d4390485
ms.sourcegitcommit: 7374b41bb1469f2e3ef119ffaf735f03f5fad484
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/16/2020
ms.locfileid: "90706836"
---
# <a name="data-residency-for-azure-network-watcher"></a>Azure 网络观察程序的数据驻留
除了连接监视器 (预览) 服务，Azure 网络观察程序不会存储客户数据。


## <a name="connection-monitor-preview-data-residency"></a>连接监视器 (预览) 数据驻留
连接监视器 (预览版) 服务存储客户数据。 此数据由网络观察程序自动存储在单个区域中。 因此，连接监视器 (预览) 自动满足区域内数据派驻要求，包括在 [信任中心](https://azuredatacentermap.azurewebsites.net/)指定的要求。

## <a name="singapore-data-residency"></a>新加坡数据驻留

在 Azure 中，启用将客户数据存储在单个区域中的功能当前仅在亚太地理 (新加坡) 的东南部地区可用。 对于所有其他区域，客户数据存储在异地。 有关详细信息，请参阅 [信任中心](https://azuredatacentermap.azurewebsites.net/)。

## <a name="next-steps"></a>后续步骤

* 阅读 [网络观察](https://docs.microsoft.com/azure/network-watcher/network-watcher-monitoring-overview)程序概述。
