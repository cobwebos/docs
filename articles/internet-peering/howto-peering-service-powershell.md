---
title: 使用 PowerShell 在直接对等互连上启用对等互连服务
titleSuffix: Azure
description: 使用 PowerShell 在直接对等互连上启用对等互连服务
services: internet-peering
author: prmitiki
ms.service: internet-peering
ms.topic: article
ms.date: 11/27/2019
ms.author: prmitiki
ms.openlocfilehash: ac843fa5440f7ba085d33cd897bcd4a1722f77ac
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/27/2020
ms.locfileid: "75774180"
---
# <a name="enable-peering-service-on-a-direct-peering-using-powershell"></a>使用 PowerShell 在直接对等互连上启用对等互连服务

本文介绍如何使用 PowerShell cmdlet 和资源管理器部署模型在直接对等互连上启用[对等互连服务](overview-peering-service.md)。

如果您愿意，可以使用[门户](howto-peering-service-portal.md)完成本指南。

## <a name="before-you-begin"></a>开始之前
* 在开始配置之前，请查看[先决条件](prerequisites.md)。
* 选择要启用对等服务的订阅中的直接对等互连。 如果没有，请转换旧版直接对等互连或创建新的直接对等互连。
    * 要转换旧版直接对等互连，请按照[PowerShell 将旧版直接对等互连](howto-legacy-direct-powershell.md)转换为 Azure 资源的说明进行操作。
    * 要创建新的直接对等互连，请按照"创建"中的说明进行操作[，或使用 PowerShell 修改直接对等互连](howto-direct-powershell.md)。

### <a name="working-with-azure-powershell"></a>使用 Azure PowerShell
[!INCLUDE [CloudShell](./includes/cloudshell-powershell-about.md)]

## <a name="enable-peering-service-on-a-direct-peering"></a>在直接对等互连上启用对等互连服务

### <a name="view-direct-peering"></a><a name= get></a>查看直接对等互连
[!INCLUDE [peering-direct-get](./includes/direct-powershell-get.md)]

### <a name="enable-the-direct-peering-for-peering-service"></a><a name= get></a>为对等互连服务启用直接对等互连

在上一步骤中获取直接对等互连后，将其启用到对等服务。
[!INCLUDE [peering-direct-modify](./includes/peering-service-direct-powershell.md)]

## <a name="modify-a-direct-peering-connection"></a>修改直接对等互连连接

如果需要修改连接设置，请参阅在"创建 **"中修改直接对等部分**[，或使用 PowerShell 修改直接对等互连](howto-direct-powershell.md)

## <a name="next-steps"></a>后续步骤

* [使用 PowerShell 创建或修改 Exchange 对等互连](howto-exchange-powershell.md)
* [使用 PowerShell 将旧版 Exchange 对等互连转换为 Azure 资源](howto-legacy-exchange-powershell.md)

## <a name="additional-resources"></a>其他资源
可以通过运行以下命令获取所有这些参数的详细说明：

```powershell
Get-Help Get-AzPeering -detailed
```

有关常见问题，请参阅[对等服务常见问题解答](service-faqs.md)。