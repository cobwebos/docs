---
title: 使用 PowerShell 创建或修改 Exchange 对等互连
titleSuffix: Azure
description: 使用 PowerShell 创建或修改 Exchange 对等互连
services: internet-peering
author: prmitiki
ms.service: internet-peering
ms.topic: article
ms.date: 11/27/2019
ms.author: prmitiki
ms.openlocfilehash: 4fd7d345e5efbe6e4e86e5bb410e2df4dd917047
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/27/2020
ms.locfileid: "75774401"
---
# <a name="create-or-modify-an-exchange-peering-using-powershell"></a>使用 PowerShell 创建或修改 Exchange 对等互连

本文介绍如何使用 PowerShell cmdlet 和资源管理器部署模型创建 Microsoft Exchange 对等互连。 本文还演示如何检查资源的状态、更新资源或删除和取消预配资源。

如果您愿意，可以使用[门户](howto-exchange-portal.md)完成本指南。

## <a name="before-you-begin"></a>开始之前
* 在开始配置之前，请查看[先决条件](prerequisites.md)和[交换对等演练](walkthrough-exchange-all.md)。
* 如果已与 Microsoft 已具有 Exchange 对等互连（未转换为 Azure 资源），请参阅[使用 PowerShell 将旧 Exchange 对等互连转换为 Azure 资源](howto-legacy-exchange-powershell.md)

### <a name="working-with-azure-powershell"></a>使用 Azure PowerShell
[!INCLUDE [CloudShell](./includes/cloudshell-powershell-about.md)]

## <a name="create-and-provision-an-exchange-peering"></a>创建和预配 Exchange 对等互连

### <a name="sign-in-to-your-azure-account-and-select-your-subscription"></a>登录到 Azure 帐户，然后选择订阅
[!INCLUDE [Account](./includes/account-powershell.md)]

### <a name="get-the-list-of-supported-peering-locations-for-exchange-peering"></a><a name=exchange-location></a>获取 Exchange 对等互连支持对等位置的列表
[!INCLUDE [exchange-location](./includes/exchange-powershell-create-location.md)]

### <a name="create-an-exchange-peering"></a><a name=create></a>创建 Exchange 对等互连
[!INCLUDE [exchange-peering](./includes/exchange-powershell-create-connection.md)]

### <a name="get-exchange-peering"></a><a name=get></a>获取交换对等互连
[!INCLUDE [peering-exchange-get](./includes/exchange-powershell-get.md)]

## <a name="modify-an-exchange-peering"></a><a name="modify"></a>修改 Exchange 对等互连
[!INCLUDE [peering-exchange-modify](./includes/exchange-powershell-modify.md)]

## <a name="deprovision-an-exchange-peering"></a><a name=delete></a>取消预配 Exchange 对等互连

[!INCLUDE [peering-exchange-delete](./includes/delete.md)]

## <a name="next-steps"></a>后续步骤

* [使用 PowerShell 创建或修改直接对等互连](howto-direct-powershell.md)
* [使用 PowerShell 将旧版直接对等互连转换为 Azure 资源](howto-legacy-direct-powershell.md)

## <a name="additional-resources"></a>其他资源
可以通过运行以下命令获取所有这些参数的详细说明：

```powershell
Get-Help Get-AzPeering -detailed
```

有关详细信息，请访问[互联网对等常见问题解答](faqs.md)
