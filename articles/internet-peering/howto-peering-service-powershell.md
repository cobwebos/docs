---
title: 使用 PowerShell 通过直接对等互连启用对等互连服务
titleSuffix: Azure
description: 使用 PowerShell 通过直接对等互连启用对等互连服务
services: internet-peering
author: prmitiki
ms.service: internet-peering
ms.topic: article
ms.date: 11/27/2019
ms.author: prmitiki
ms.openlocfilehash: ac843fa5440f7ba085d33cd897bcd4a1722f77ac
ms.sourcegitcommit: aee08b05a4e72b192a6e62a8fb581a7b08b9c02a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/09/2020
ms.locfileid: "75774180"
---
# <a name="enable-peering-service-on-a-direct-peering-using-powershell"></a>使用 PowerShell 通过直接对等互连启用对等互连服务

本文介绍如何使用 PowerShell cmdlet 和资源管理器部署模型启用对等互连[服务](overview-peering-service.md)的直接对等互连。

如果愿意，可以使用[门户](howto-peering-service-portal.md)完成本指南。

## <a name="before-you-begin"></a>开始之前
* 在开始配置之前，请查看[先决条件](prerequisites.md)。
* 在订阅中选择要在其上启用对等互连服务的直接对等互连。 如果没有，请转换旧直接对等互连，或创建新的直接对等互连。
    * 若要转换旧版直接对等互连，请按照[使用 PowerShell 将旧直接对等互连转换为 Azure 资源](howto-legacy-direct-powershell.md)中的说明进行操作。
    * 若要创建新的直接对等互连，请按照[使用 PowerShell 创建或修改直接对](howto-direct-powershell.md)等互连中的说明进行操作。

### <a name="working-with-azure-powershell"></a>使用 Azure PowerShell
[!INCLUDE [CloudShell](./includes/cloudshell-powershell-about.md)]

## <a name="enable-peering-service-on-a-direct-peering"></a>对直接对等互连启用对等互连服务

### <a name= get></a>查看直接对等
[!INCLUDE [peering-direct-get](./includes/direct-powershell-get.md)]

### <a name= get></a>为对等互连服务启用直接对等互连

在上一步中获取直接对等互连后，为对等互连服务启用它。
[!INCLUDE [peering-direct-modify](./includes/peering-service-direct-powershell.md)]

## <a name="modify-a-direct-peering-connection"></a>修改直接对等连接

如果需要修改连接设置，请参阅[使用 PowerShell 创建或修改直接](howto-direct-powershell.md)对等互连中的**修改直接对等**互连部分

## <a name="next-steps"></a>后续步骤

* [使用 PowerShell 创建或修改 Exchange 对等互连](howto-exchange-powershell.md)
* [使用 PowerShell 将旧版 Exchange 对等互连转换为 Azure 资源](howto-legacy-exchange-powershell.md)

## <a name="additional-resources"></a>其他资源
可以通过运行以下命令获取所有这些参数的详细说明：

```powershell
Get-Help Get-AzPeering -detailed
```

有关常见问题，请参阅对[等互连服务常见问题解答](service-faqs.md)。