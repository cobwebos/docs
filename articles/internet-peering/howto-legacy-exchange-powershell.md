---
title: 使用 PowerShell 将旧版 Exchange 对等互连转换为 Azure 资源
titleSuffix: Azure
description: 使用 PowerShell 将旧版 Exchange 对等互连转换为 Azure 资源
services: internet-peering
author: prmitiki
ms.service: internet-peering
ms.topic: how-to
ms.date: 11/27/2019
ms.author: prmitiki
ms.openlocfilehash: 2abd05243f30a32d8fe49f12bf4ee2ad09855002
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/02/2020
ms.locfileid: "84699985"
---
# <a name="convert-a-legacy-exchange-peering-to-an-azure-resource-by-using-powershell"></a>使用 PowerShell 将旧版 Exchange 对等互连转换为 Azure 资源

本文介绍如何使用 PowerShell cmdlet 将现有的旧式 Exchange 对等互连转换为 Azure 资源。

如果愿意，可以使用 Azure[门户](howto-legacy-exchange-portal.md)完成本指南。

## <a name="before-you-begin"></a>开始之前
* 在开始配置之前，请查看[先决条件](prerequisites.md)和 [Exchange 对等互连演练](walkthrough-exchange-all.md)。

### <a name="work-with-azure-powershell"></a>使用 Azure PowerShell
[!INCLUDE [CloudShell](./includes/cloudshell-powershell-about.md)]

## <a name="convert-a-legacy-exchange-peering-to-an-azure-resource"></a>将旧版 Exchange 对等互连转换为 Azure 资源

### <a name="sign-in-to-your-azure-account-and-select-your-subscription"></a>登录到 Azure 帐户，然后选择订阅
[!INCLUDE [Account](./includes/account-powershell.md)]

### <a name="get-legacy-exchange-peering-for-conversion"></a><a name= get></a>获取要转换的旧 Exchange 对等互连
此示例演示如何在西雅图对等位置获取旧的 Exchange 对等互连：

```powershell
$legacyPeering = Get-AzLegacyPeering -Kind Exchange -PeeringLocation "Seattle"
$legacyPeering
```

响应类似于以下示例：
```powershell
    Kind                     : Exchange
    PeeringLocation          : Seattle
    PeerAsn                  : 65000
    Connection               : ------------------------
    PeerSessionIPv4Address   : 10.21.31.100
    MicrosoftIPv4Address     : 10.21.31.50
    SessionStateV4           : Established
    MaxPrefixesAdvertisedV4  : 20000
    PeerSessionIPv6Address   : fe01::3e:100
    MicrosoftIPv6Address     : fe01::3e:50
    SessionStateV6           : Established
    MaxPrefixesAdvertisedV6  : 2000
    ConnectionState          : Active
```

### <a name="convert-legacy-peering"></a>转换旧对等互连
此命令可用于将旧的 Exchange 对等互连转换为 Azure 资源：

```powershell
$legacyPeering[0] | New-AzPeering `
    -Name "SeattleExchangePeering" `
    -ResourceGroupName "PeeringResourceGroup"

```

&nbsp;
> [!IMPORTANT] 
> 将旧对等互连转换为 Azure 资源时，不支持修改。
&nbsp;

此示例响应显示已成功完成端到端预配：

```powershell
    Name                     : SeattleExchangePeering
    Kind                     : Exchange
    Sku                      : Basic_Exchange_Free
    PeeringLocation          : Seattle
    PeerAsn                  : 65000
    Connection               : ------------------------
    PeerSessionIPv4Address   : 10.21.31.100
    MicrosoftIPv4Address     : 10.21.31.50
    SessionStateV4           : Established
    MaxPrefixesAdvertisedV4  : 20000
    PeerSessionIPv6Address   : fe01::3e:100
    MicrosoftIPv6Address     : fe01::3e:50
    SessionStateV6           : Established
    MaxPrefixesAdvertisedV6  : 2000
    ConnectionState          : Active
```
## <a name="additional-resources"></a>其他资源
可以通过运行以下命令获取所有这些参数的详细说明：

```powershell
Get-Help Get-AzPeering -detailed
```
有关详细信息，请参阅 [Internet 对等互连常见问题解答](faqs.md)。

## <a name="next-steps"></a>后续步骤

* [使用 PowerShell 创建或修改 Exchange 对等互连](howto-exchange-powershell.md)
