---
title: 使用 PowerShell 创建或修改直接对等互连
titleSuffix: Azure
description: 使用 PowerShell 创建或修改直接对等互连
services: internet-peering
author: prmitiki
ms.service: internet-peering
ms.topic: article
ms.date: 11/27/2019
ms.author: prmitiki
ms.openlocfilehash: 69031550bdab1535213c78f81426fa76e8ea62ad
ms.sourcegitcommit: aee08b05a4e72b192a6e62a8fb581a7b08b9c02a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/09/2020
ms.locfileid: "75774232"
---
# <a name="create-or-modify-a-direct-peering-using-powershell"></a>使用 PowerShell 创建或修改直接对等互连

本文介绍如何使用 PowerShell cmdlet 和资源管理器部署模型创建 Microsoft 直接对等互连。 本文还介绍了如何检查资源的状态、对其进行更新、删除和取消预配。

如果愿意，可以使用[门户](howto-direct-portal.md)完成本指南。

## <a name="before-you-begin"></a>开始之前
* 开始配置之前，请查看[先决条件](prerequisites.md)和[直接对等互连演练](walkthrough-direct-all.md)。
* 如果你已将与 Microsoft 的直接对等互连，但未将其转换为 Azure 资源，请参阅[使用 PowerShell 将旧直接对等互连转换为 azure 资源](howto-legacy-direct-powershell.md)

### <a name="working-with-azure-powershell"></a>使用 Azure PowerShell
[!INCLUDE [CloudShell](./includes/cloudshell-powershell-about.md)]

## <a name="create-and-provision-a-direct-peering"></a>创建和预配直接对等互连

### <a name="sign-in-to-your-azure-account-and-select-your-subscription"></a>登录到 Azure 帐户，然后选择订阅
[!INCLUDE [Account](./includes/account-powershell.md)]

### <a name=direct-location></a>获取支持的对等互连位置的列表以实现直接对等互连
[!INCLUDE [direct-location](./includes/direct-powershell-create-location.md)]

### <a name=create></a>创建直接对等互连
[!INCLUDE [direct-peering](./includes/direct-powershell-create-connection.md)]

### <a name=get></a>验证直接对等互连
[!INCLUDE [peering-direct-get](./includes/direct-powershell-get.md)]

## <a name="modify"></a>修改直接对等互连
[!INCLUDE [peering-direct-modify](./includes/direct-powershell-modify.md)]

## <a name="delete"></a>取消预配对等互连
[!INCLUDE [peering-direct-delete](./includes/delete.md)]

## <a name="next-steps"></a>后续步骤

* [使用 PowerShell 创建或修改 Exchange 对等互连](howto-exchange-powershell.md)。
* [使用 PowerShell 将旧版 Exchange 对等互连转换为 Azure 资源](howto-legacy-exchange-powershell.md)。

## <a name="additional-resources"></a>其他资源
可以通过运行以下命令获取所有这些参数的详细说明：

```powershell
Get-Help Get-AzPeering -detailed
```

有关详细信息，请访问[Internet 对等互连常见问题解答](faqs.md)
