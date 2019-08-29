---
title: 归置 Linux Azure Vm |Microsoft Docs
description: 了解如何归置 Azure VM 资源来提高延迟。
services: virtual-machines-linux
documentationcenter: ''
author: cynthn
manager: jeconnoc
editor: ''
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 06/28/2019
ms.author: cynthn
ms.openlocfilehash: 81b9dc5059a6ab3e8245acd9c7e7ef8be5abdafd
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/28/2019
ms.locfileid: "70083872"
---
# <a name="co-locate-resources-for-improved-latency"></a>归置资源以提高延迟

在 Azure 中部署应用程序时, 跨区域或可用性区域分配实例会产生网络延迟, 这可能会影响应用程序的总体性能。 

## <a name="preview-proximity-placement-groups"></a>预览版：邻近放置组

[!INCLUDE [virtual-machines-common-ppg-overview](../../../includes/virtual-machines-common-ppg-overview.md)]

## <a name="next-steps"></a>后续步骤

使用 Azure CLI 将 VM 部署到[邻近位置组](proximity-placement-groups.md)。

