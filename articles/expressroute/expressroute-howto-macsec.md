---
title: Azure 快速路由：配置 MACsec
description: 本文可帮助您配置 MACsec，以确保边缘路由器和 Microsoft 边缘路由器之间的连接。
services: expressroute
author: cherylmc
ms.service: expressroute
ms.topic: conceptual
ms.date: 10/22/2019
ms.author: cherylmc
ms.openlocfilehash: 626302845dfb4b19deb921675601818b35ab8edb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/27/2020
ms.locfileid: "74083551"
---
# <a name="configure-macsec-on-expressroute-direct-ports"></a>在快速路由直接端口上配置 MACsec

本文可帮助您配置 MACsec，以使用 PowerShell 保护您的边缘路由器和 Microsoft 边缘路由器之间的连接。

## <a name="before-you-begin"></a>开始之前

在开始配置之前，请确认以下事项：

* 您了解[ExpressRoute 直接预配工作流](expressroute-erdirect-about.md)。
* 您已经创建了一个[ExpressRoute 直接端口资源](expressroute-howto-erdirect.md)。
* 如果要在本地运行 PowerShell，请验证计算机上是否安装了最新版本的 Azure PowerShell。

### <a name="working-with-azure-powershell"></a>使用 Azure PowerShell

[!INCLUDE [updated-for-az](../../includes/hybrid-az-ps.md)]

[!INCLUDE [expressroute-cloudshell](../../includes/expressroute-cloudshell-powershell-about.md)]

### <a name="sign-in-and-select-the-right-subscription"></a>登录并选择正确的订阅

要启动配置，请登录到 Azure 帐户并选择要使用的订阅。

   [!INCLUDE [sign in](../../includes/expressroute-cloud-shell-connect.md)]

## <a name="1-create-azure-key-vault-macsec-secrets-and-user-identity"></a>1. 创建 Azure 密钥保管库、MACsec 机密和用户标识

1. 创建密钥保管库实例以在新资源组中存储 MACsec 机密。

    ```azurepowershell-interactive
    New-AzResourceGroup -Name "your_resource_group" -Location "resource_location"
    $keyVault = New-AzKeyVault -Name "your_key_vault_name" -ResourceGroupName "your_resource_group" -Location "resource_location" -EnableSoftDelete 
    ```

    如果您已经拥有密钥保管库或资源组，则可以重用它们。 但是，在现有的密钥保管库上启用[**软删除**功能](../key-vault/key-vault-ovw-soft-delete.md)至关重要。 如果未启用软删除，则可以使用以下命令启用它：

    ```azurepowershell-interactive
    ($resource = Get-AzResource -ResourceId (Get-AzKeyVault -VaultName "your_existing_keyvault").ResourceId).Properties | Add-Member -MemberType "NoteProperty" -Name "enableSoftDelete" -Value "true"
    Set-AzResource -resourceid $resource.ResourceId -Properties $resource.Properties
    ```
2. 创建用户标识。

    ```azurepowershell-interactive
    $identity = New-AzUserAssignedIdentity  -Name "identity_name" -Location "resource_location" -ResourceGroupName "your_resource_group"
    ```

    如果 New-AzUser 分配标识未识别为有效的 PowerShell cmdlet，请安装以下模块（在管理员模式下），然后重新运行上述命令。

    ```azurepowershell-interactive
    Install-Module -Name Az.ManagedServiceIdentity
    ```
3. 创建连接关联密钥 （CAK） 和连接关联密钥名称 （CKN），并将它们存储在密钥保管库中。

    ```azurepowershell-interactive
    $CAK = ConvertTo-SecureString "your_key" -AsPlainText -Force
    $CKN = ConvertTo-SecureString "your_key_name" -AsPlainText -Force
    $MACsecCAKSecret = Set-AzKeyVaultSecret -VaultName "your_key_vault_name" -Name "CAK_name" -SecretValue $CAK
    $MACsecCKNSecret = Set-AzKeyVaultSecret -VaultName "your_key_vault_name" -Name "CKN_name" -SecretValue $CKN
    ```
4. 将 GET 权限分配给用户标识。

    ```azurepowershell-interactive
    Set-AzKeyVaultAccessPolicy -VaultName "your_key_vault_name" -PermissionsToSecrets get -ObjectId $identity.PrincipalId
    ```

   现在，此标识可以从密钥保管库获取机密，例如 CAK 和 CKN。
5. 将此用户标识设置为 ExpressRoute 使用。

    ```azurepowershell-interactive
    $erIdentity = New-AzExpressRoutePortIdentity -UserAssignedIdentityId $identity.Id
    ```
 
## <a name="2-configure-macsec-on-expressroute-direct-ports"></a>2. 在快速路由直接端口上配置 MACsec

### <a name="to-enable-macsec"></a>启用 MACsec

每个 ExpressRoute Direct 实例都有两个物理端口。 您可以选择同时在两个端口上启用 MACsec，也可以选择一次在一个端口上启用 MACsec。 一次执行一个端口（在维修另一个端口时将流量切换到活动端口）有助于将中断降至最低，如果您的 ExpressRoute Direct 已在服务中。

1. 设置 MACsec 机密和密码，并将用户标识与端口相关联，以便 ExpressRoute 管理代码在需要时可以访问 MACsec 机密。

    ```azurepowershell-interactive
    $erDirect = Get-AzExpressRoutePort -ResourceGroupName "your_resource_group" -Name "your_direct_port_name"
    $erDirect.Links[0]. MacSecConfig.CknSecretIdentifier = $MacSecCKNSecret.Id
    $erDirect.Links[0]. MacSecConfig.CakSecretIdentifier = $MacSecCAKSecret.Id
    $erDirect.Links[0]. MacSecConfig.Cipher = "GcmAes256"
    $erDirect.Links[1]. MacSecConfig.CknSecretIdentifier = $MacSecCKNSecret.Id
    $erDirect.Links[1]. MacSecConfig.CakSecretIdentifier = $MacSecCAKSecret.Id
    $erDirect.Links[1]. MacSecConfig.Cipher = "GcmAes256"
    $erDirect.identity = $erIdentity
    Set-AzExpressRoutePort -ExpressRoutePort $erDirect
    ```
2. （可选）如果端口处于"管理关闭"状态，则可以运行以下命令来启动端口。

    ```azurepowershell-interactive
    $erDirect = Get-AzExpressRoutePort -ResourceGroupName "your_resource_group" -Name "your_direct_port_name"
    $erDirect.Links[0].AdminState = "Enabled"
    $erDirect.Links[1].AdminState = "Enabled"
    Set-AzExpressRoutePort -ExpressRoutePort $erDirect
    ```

    此时，MACsec 在 Microsoft 端的 ExpressRoute 直接端口上启用。 如果尚未在边缘设备上配置它，则可以继续使用相同的 MACsec 机密和密码来配置它们。

### <a name="to-disable-macsec"></a>禁用 MACsec

如果您的 ExpressRoute Direct 实例不再需要 MACsec，则可以运行以下命令来禁用它。

```azurepowershell-interactive
$erDirect = Get-AzExpressRoutePort -ResourceGroupName "your_resource_group" -Name "your_direct_port_name"
$erDirect.Links[0]. MacSecConfig.CknSecretIdentifier = $null
$erDirect.Links[0]. MacSecConfig.CakSecretIdentifier = $null
$erDirect.Links[1]. MacSecConfig.CknSecretIdentifier = $null
$erDirect.Links[1]. MacSecConfig.CakSecretIdentifier = $null
$erDirect.identity = $null
Set-AzExpressRoutePort -ExpressRoutePort $erDirect
```

此时，MACsec 在 Microsoft 端的 ExpressRoute 直接端口上被禁用。

### <a name="test-connectivity"></a>测试连接
在 ExpressRoute Direct 端口上配置 MACsec（包括 MACsec 密钥更新）后，[请检查](expressroute-troubleshooting-expressroute-overview.md)电路的 BGP 会话是否启动并运行。 如果端口上没有任何电路，请先创建一个电路，并设置电路的 Azure 专用对等互连或 Microsoft 对等互连。 如果 MACsec 配置错误（包括 MACsec 密钥不匹配）网络设备和 Microsoft 的网络设备之间，您将不会在第 2 层和第 3 层的 BGP 建立中看到 ARP 分辨率。 如果所有内容都配置正确，您应该会看到双向正确通告的 BGP 路由，以及应用程序数据流通过 ExpressRoute 相应地进行。

## <a name="next-steps"></a>后续步骤
1. [在快速路由直接上创建快速路由电路](expressroute-howto-erdirect.md)
2. [将 ExpressRoute 线路链接到 Azure 虚拟网络](expressroute-howto-linkvnet-arm.md)
3. [验证 ExpressRoute 连接](expressroute-troubleshooting-expressroute-overview.md)
