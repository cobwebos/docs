---
title: 使用 PowerShell 将对等 ASN 关联到 Azure 订阅
titleSuffix: Azure
description: 使用 PowerShell 将对等 ASN 关联到 Azure 订阅
services: internet-peering
author: prmitiki
ms.service: internet-peering
ms.topic: article
ms.date: 11/27/2019
ms.author: prmitiki
ms.openlocfilehash: 77cc4732e017d95cbae19578cf26b1111b08fdde
ms.sourcegitcommit: f9601bbccddfccddb6f577d6febf7b2b12988911
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/12/2020
ms.locfileid: "75908989"
---
# <a name="associate-peer-asn-to-azure-subscription-using-powershell"></a>使用 PowerShell 将对等 ASN 关联到 Azure 订阅

提交对等互连请求之前，首先应使用以下步骤将 ASN 与 Azure 订阅相关联。

如果愿意，可以使用[门户](howto-subscription-association-portal.md)完成本指南。

### <a name="working-with-azure-powershell"></a>使用 Azure PowerShell
[!INCLUDE [CloudShell](./includes/cloudshell-powershell-about.md)]

## <a name="create-peerasn-to-associate-your-asn-with-azure-subscription"></a>创建 PeerASN，将 ASN 与 Azure 订阅关联

### <a name="sign-in-to-your-azure-account-and-select-your-subscription"></a>登录到 Azure 帐户，然后选择订阅
[!INCLUDE [Account](./includes/account-powershell.md)]

### <a name="register-for-peering-resource-provider"></a>注册对等互连资源提供程序
使用下面的命令，在订阅中注册对等互连资源提供程序。 如果未执行此操作，则设置对等互连所需的 Azure 资源将无法访问。

```powershell
Register-AzResourceProvider -ProviderNamespace Microsoft.Peering
```

可以使用以下命令检查注册状态：
```powershell
Get-AzResourceProvider -ProviderNamespace Microsoft.Peering
```

> [!IMPORTANT]
> 等待*RegistrationState*在继续之前 "已注册"。 执行命令可能需要5到30分钟。

### <a name="update-the-peer-information-associated-with-this-subscription"></a>更新与此订阅关联的对等信息

下面是一个更新对等信息的示例。

```powershell
New-AzPeerAsn `
    -Name "Contoso_1234" `
    -PeerName "Contoso" `
    -PeerAsn 1234 `
    -Email noc@contoso.com, support@contoso.com `
    -Phone "+1 (555) 555-5555"
```

> [!NOTE]
> -"名称" 对应于 "资源名称"，可以是你选择的任何内容。 但是，-对等名对应于公司的名称，需要尽可能接近你的 PeeringDB 配置文件。 请注意，-对等名的值仅支持字符 a-z、a-z 和空格。

一个订阅可以有多个 Asn。 更新每个 ASN 的对等互连信息。 请确保每个 ASN 的 "名称" 是唯一的。

对等端应在[PeeringDB](https://www.peeringdb.com)上具有完整的和最新的配置文件。 在注册过程中，我们将使用此信息来验证对等方的详细信息，例如 NOC 信息、技术联系人信息，以及它们在对等互连设施上的存在性等。

请注意，在上面的**输出中，** 将显示实际订阅 ID。

## <a name="view-status-of-a-peerasn"></a>查看 PeerASN 的状态

使用以下命令检查 ASN 验证状态：

```powershell
Get-AzPeerAsn
```

下面是一个示例响应：
```powershell
PeerContactInfo : Microsoft.Azure.PowerShell.Cmdlets.Peering.Models.PSContactInfo
PeerName        : Contoso
ValidationState : Approved
PeerAsnProperty : 1234
Name            : Contoso_1234
Id              : /subscriptions/{subscriptionId}/providers/Microsoft.Peering/peerAsns/Contoso_1234
Type            : Microsoft.Peering/peerAsns
```

> [!IMPORTANT]
> 等待 ValidationState 在提交对等互连请求之前变成 "已批准"。 此批准最多可能需要12小时。

## <a name="modify-peerasn"></a>修改 PeerAsn
你可以随时修改 NOC 联系信息。

下面是一个示例：

```powershell
Set-PeerAsn -Name Contoso_1234 -Email "newemail@test.com" -Phone "1800-000-0000"
```

## <a name="delete-peerasn"></a>删除 PeerAsn
当前不支持删除 PeerASN。 如果需要删除 PeerASN，请联系[Microsoft 对等互连](mailto:peering@microsoft.com)。

## <a name="next-steps"></a>后续步骤

* [创建或修改直接对等互连](howto-direct-powershell.md)
* [将旧直接对等互连转换为 Azure 资源](howto-legacy-direct-powershell.md)
* [创建或修改 Exchange 对等互连](howto-exchange-powershell.md)
* [将旧式 Exchange 对等互连转换为 Azure 资源](howto-legacy-exchange-powershell.md)

## <a name="additional-resources"></a>其他资源

有关详细信息，请访问[Internet 对等互连常见问题解答](faqs.md)
