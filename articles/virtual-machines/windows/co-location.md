---
title: 并置 VM 以缩短延迟
description: 了解并置 Azure VM 资源如何改善延迟。
author: cynthn
ms.service: virtual-machines
ms.topic: conceptual
ms.workload: infrastructure-services
ms.date: 10/30/2019
ms.author: zivr
ms.openlocfilehash: b5a3c0a582b1e9dfbcf81968ebc9d0c7a0a4f75e
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "87288316"
---
# <a name="co-locate-resource-for-improved-latency"></a>并置资源以改善延迟

在 Azure 中部署应用程序时，跨区域或可用性区域分布实例会造成网络延迟，这可能会影响应用程序的总体性能。 


## <a name="proximity-placement-groups"></a>邻近放置组 

[!INCLUDE [virtual-machines-common-ppg-overview](../../../includes/virtual-machines-common-ppg-overview.md)]

## <a name="next-steps"></a>后续步骤

使用 Azure PowerShell 将 VM 部署到[邻近放置组](proximity-placement-groups.md)。

了解如何[测试网络延迟](https://aka.ms/TestNetworkLatency?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)。

了解如何[优化网络吞吐量](../../virtual-network/virtual-network-optimize-network-bandwidth.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)。  

了解如何 [结合使用邻近组和 SAP 应用程序](../workloads/sap/sap-proximity-placement-scenarios.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)。
