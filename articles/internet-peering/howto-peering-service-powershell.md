---
title: 使用 PowerShell 通过直接对等互连启用 Azure 对等互连服务
titleSuffix: Azure
description: 使用 PowerShell 通过直接对等互连启用 Azure 对等互连服务
services: internet-peering
author: prmitiki
ms.service: internet-peering
ms.topic: how-to
ms.date: 11/27/2019
ms.author: prmitiki
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 98341fbbbcafb6aee938870c22050c6edec352ac
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "89079041"
---
# <a name="enable-azure-peering-service-on-a-direct-peering-by-using-powershell"></a>使用 PowerShell 通过直接对等互连启用 Azure 对等互连服务

本文介绍如何使用 PowerShell cmdlet 和 Azure 资源管理器部署模型在直接对等互连上启用 Azure 对 [等服务](overview-peering-service.md) 。

如果愿意，可以使用 Azure [门户](howto-peering-service-portal.md)完成本指南。

## <a name="before-you-begin"></a>在开始之前
* 在开始配置之前，请查看 [先决条件](prerequisites.md) 。
* 在订阅中选择要为其启用对等互连服务的直接对等互连。 如果没有，请转换旧直接对等互连，或创建新的直接对等互连：
    * 若要转换旧版直接对等互连，请按照 [使用 PowerShell 将旧直接对等互连转换为 Azure 资源](howto-legacy-direct-powershell.md)中的说明进行操作。
    * 若要创建新的直接对等互连，请按照 [使用 PowerShell 创建或修改直接对等](howto-direct-powershell.md)互连中的说明进行操作。

### <a name="work-with-azure-powershell"></a>使用 Azure PowerShell
[!INCLUDE [CloudShell](./includes/cloudshell-powershell-about.md)]

## <a name="enable-peering-service-on-a-direct-peering"></a>在直接对等互连上启用对等互连服务

### <a name="view-direct-peering"></a><a name= get></a>查看直接对等
[!INCLUDE [peering-direct-get](./includes/direct-powershell-get.md)]

### <a name="enable-the-direct-peering-for-peering-service"></a><a name= get></a>为对等互连服务启用直接对等互连

在上一步中获得直接对等互连后，为对等互连服务启用该对等互连。
[!INCLUDE [peering-direct-modify](./includes/peering-service-direct-powershell.md)]

## <a name="modify-a-direct-peering-connection"></a>修改直接对等连接

如果需要修改连接设置，请参阅 [使用 PowerShell 创建或修改直接对等互连](howto-direct-powershell.md)中的 "修改直接对等互连" 一节。

## <a name="next-steps"></a>后续步骤

* [使用 PowerShell 创建或修改 Exchange 对等互连](howto-exchange-powershell.md)
* [使用 PowerShell 将旧版 Exchange 对等互连转换为 Azure 资源](howto-legacy-exchange-powershell.md)

## <a name="additional-resources"></a>其他资源
可以通过运行以下命令获取所有这些参数的详细说明：

```powershell
Get-Help Get-AzPeering -detailed
```

有关常见问题，请参阅对 [等互连服务常见问题解答](service-faqs.md)。
