---
title: 使用 PowerShell 将对等 ASN 与 Azure 订阅关联
titleSuffix: Azure
description: 使用 PowerShell 将对等 ASN 与 Azure 订阅关联
services: internet-peering
author: prmitiki
ms.service: internet-peering
ms.topic: article
ms.date: 11/27/2019
ms.author: prmitiki
ms.openlocfilehash: 77cc4732e017d95cbae19578cf26b1111b08fdde
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/27/2020
ms.locfileid: "75908989"
---
# <a name="associate-peer-asn-to-azure-subscription-using-powershell"></a>使用 PowerShell 将对等 ASN 与 Azure 订阅关联

在提交对等请求之前，应首先使用以下步骤将 ASN 与 Azure 订阅相关联。

如果您愿意，可以使用[门户](howto-subscription-association-portal.md)完成本指南。

### <a name="working-with-azure-powershell"></a>使用 Azure PowerShell
[!INCLUDE [CloudShell](./includes/cloudshell-powershell-about.md)]

## <a name="create-peerasn-to-associate-your-asn-with-azure-subscription"></a>创建对等 ASN 以将 ASN 与 Azure 订阅关联

### <a name="sign-in-to-your-azure-account-and-select-your-subscription"></a>登录到 Azure 帐户，然后选择订阅
[!INCLUDE [Account](./includes/account-powershell.md)]

### <a name="register-for-peering-resource-provider"></a>注册对等资源提供程序
使用以下命令注册订阅中的对等资源提供程序。 如果不执行此操作，则无法访问设置对等互连所需的 Azure 资源。

```powershell
Register-AzResourceProvider -ProviderNamespace Microsoft.Peering
```

您可以使用以下命令检查注册状态：
```powershell
Get-AzResourceProvider -ProviderNamespace Microsoft.Peering
```

> [!IMPORTANT]
> 等待*注册状态*转"已注册"，然后再继续。 执行命令可能需要 5 到 30 分钟。

### <a name="update-the-peer-information-associated-with-this-subscription"></a>更新与此订阅关联的对等信息

下面是更新对等信息的示例。

```powershell
New-AzPeerAsn `
    -Name "Contoso_1234" `
    -PeerName "Contoso" `
    -PeerAsn 1234 `
    -Email noc@contoso.com, support@contoso.com `
    -Phone "+1 (555) 555-5555"
```

> [!NOTE]
> -名称对应于资源名称，可以是您选择的任何名称。 但是，-对等名称对应于公司的名称，并且需要尽可能靠近您的 PeeringDB 配置文件。 请注意，对等名称的值仅支持 a-z、A-Z 和空格的字符。

订阅可以有多个 ASN。 更新每个 ASN 的对等信息。 确保每个 ASN 的"名称"是唯一的。

同行们有望在[对同侪DB](https://www.peeringdb.com)上拥有完整和最新的配置文件。 我们在注册期间使用这些信息来验证同行的详细信息，如 NOC 信息、技术联系信息及其在对等设施的存在等。

请注意，为了代替上述输出中的 **[订阅 Id]，** 将显示实际订阅 ID。

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
> 等待验证状态在提交对等请求之前将"已批准"变为"已批准"。 此批准可能需要长达 12 小时。

## <a name="modify-peerasn"></a>修改对等Asn
您可以随时修改 NOC 联系信息。

下面是一个示例：

```powershell
Set-PeerAsn -Name Contoso_1234 -Email "newemail@test.com" -Phone "1800-000-0000"
```

## <a name="delete-peerasn"></a>删除对等 Asn
当前不支持删除 PeerASN。 如果需要删除 PeerASN，请与[Microsoft 对等互连](mailto:peering@microsoft.com)联系。

## <a name="next-steps"></a>后续步骤

* [创建或修改直接对等互连](howto-direct-powershell.md)
* [将旧版直接对等互连转换为 Azure 资源](howto-legacy-direct-powershell.md)
* [创建或修改 Exchange 对等互连](howto-exchange-powershell.md)
* [将旧版 Exchange 对等互连转换为 Azure 资源](howto-legacy-exchange-powershell.md)

## <a name="additional-resources"></a>其他资源

有关详细信息，请访问[互联网对等常见问题解答](faqs.md)
