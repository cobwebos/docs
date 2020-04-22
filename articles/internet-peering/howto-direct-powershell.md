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
ms.openlocfilehash: 7639499aaef8d479c2552849b2124e709c46fd36
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/21/2020
ms.locfileid: "81680785"
---
# <a name="create-or-modify-a-direct-peering-by-using-powershell"></a>使用 PowerShell 创建或修改直接对等互连

本文介绍如何使用 PowerShell cmdlet 和 Azure 资源管理器部署模型创建 Microsoft 直接对等互连。 本文还演示如何检查资源的状态、更新资源或删除和取消预配资源。

如果您愿意，可以使用 Azure[门户](howto-direct-portal.md)完成本指南。

## <a name="before-you-begin"></a>开始之前
* 在开始配置之前，请查看[先决条件](prerequisites.md)和[直接对等演练](walkthrough-direct-all.md)。
* 如果已与 Microsoft 具有未转换为 Azure 资源的直接对等互连连接，请参阅[使用 PowerShell 将旧版直接对等互连转换为 Azure 资源](howto-legacy-direct-powershell.md)。

### <a name="work-with-azure-powershell"></a>使用 Azure 电源外壳
[!INCLUDE [CloudShell](./includes/cloudshell-powershell-about.md)]

## <a name="create-and-provision-a-direct-peering"></a>创建和预配直接对等互连

### <a name="sign-in-to-your-azure-account-and-select-your-subscription"></a>登录到 Azure 帐户，然后选择订阅
[!INCLUDE [Account](./includes/account-powershell.md)]

### <a name="get-the-list-of-supported-peering-locations-for-direct-peering"></a><a name=direct-location></a>获取支持对等位置的列表，以便直接对等互连
[!INCLUDE [direct-location](./includes/direct-powershell-create-location.md)]

### <a name="create-a-direct-peering"></a><a name=create></a>创建直接对等互连
[!INCLUDE [direct-peering](./includes/direct-powershell-create-connection.md)]

### <a name="verify-direct-peering"></a><a name=get></a>验证直接对等互连
[!INCLUDE [peering-direct-get](./includes/direct-powershell-get.md)]

## <a name="modify-a-direct-peering"></a><a name="modify"></a>修改直接对等互连
[!INCLUDE [peering-direct-modify](./includes/direct-powershell-modify.md)]

## <a name="deprovision-a-direct-peering"></a><a name="delete"></a>取消预配直接对等互连
[!INCLUDE [peering-direct-delete](./includes/delete.md)]

## <a name="next-steps"></a>后续步骤

* [使用 PowerShell 创建或修改 Exchange 对等互连](howto-exchange-powershell.md)
* [使用 PowerShell 将旧版 Exchange 对等互连转换为 Azure 资源](howto-legacy-exchange-powershell.md)

## <a name="additional-resources"></a>其他资源
可以通过运行以下命令获取所有这些参数的详细说明：

```powershell
Get-Help Get-AzPeering -detailed
```

有关详细信息，请参阅[互联网对等常见问题解答](faqs.md)。
