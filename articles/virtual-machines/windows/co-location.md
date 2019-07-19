---
title: 归置 Windows Azure Vm |Microsoft Docs
description: 了解如何归置 Azure VM 资源来提高延迟。
services: virtual-machines-windows
documentationcenter: ''
author: cynthn
manager: jeconnoc
editor: ''
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 06/28/2019
ms.author: cynthn
ms.openlocfilehash: a6360e1ee8469f8674685f5975ec09db3e87a4ea
ms.sourcegitcommit: fa45c2bcd1b32bc8dd54a5dc8bc206d2fe23d5fb
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/12/2019
ms.locfileid: "67850439"
---
# <a name="co-locate-resource-for-improved-latency"></a>归置资源以提高延迟

在 Azure 中部署应用程序时, 跨区域或可用性区域分配实例会产生网络延迟, 这可能会影响应用程序的总体性能。 


## <a name="preview-proximity-placement-groups"></a>预览版：邻近位置组 

[!INCLUDE [virtual-machines-common-ppg-overview](../../../includes/virtual-machines-common-ppg-overview.md)]

## <a name="next-steps"></a>后续步骤

使用 Azure PowerShell 将 VM 部署到[邻近位置组](proximity-placement-groups.md)。

