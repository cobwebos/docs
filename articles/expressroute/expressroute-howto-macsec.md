---
title: 配置 MACsec-ExpressRoute：Azure | Microsoft Docs
description: 本文介绍如何配置 MACsec 以保护边缘路由器与 Microsoft 边缘路由器之间的连接。
services: expressroute
author: cherylmc
ms.service: expressroute
ms.topic: conceptual
ms.date: 10/09/2019
ms.author: cherylmc
ms.openlocfilehash: eeaa709b88ca795d906fe3688301b4cd7d8c726e
ms.sourcegitcommit: 824e3d971490b0272e06f2b8b3fe98bbf7bfcb7f
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/10/2019
ms.locfileid: "72244131"
---
# <a name="configure-macsec-on-expressroute-direct-ports"></a>在 ExpressRoute 直接端口上配置 MACsec

本文介绍如何使用 PowerShell 配置 MACsec，以保护边缘路由器与 Microsoft 边缘路由器之间的连接。

## <a name="before-you-begin"></a>开始之前

在开始配置之前，请确认以下事项：

* 了解[ExpressRoute 直接预配工作流](expressroute-erdirect-about.md)。
* 已创建[ExpressRoute 直接端口资源](expressroute-howto-erdirect.md)。
* 如果要在本地运行 PowerShell，请验证计算机上是否安装了 Azure PowerShell 的最新版本。

### <a name="working-with-azure-powershell"></a>使用 Azure PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

[!INCLUDE [expressroute-cloudshell](../../includes/expressroute-cloudshell-powershell-about.md)]

### <a name="sign-in-and-select-the-right-subscription"></a>登录并选择正确的订阅

若要启动配置，请登录到 Azure 帐户，并选择要使用的订阅。

   [!INCLUDE [sign in](../../includes/expressroute-cloud-shell-connect.md)]

## <a name="1-create-azure-key-vault-macsec-secrets-and-user-identity"></a>1.创建 Azure Key Vault、MACsec 机密和用户标识

1. 创建 Key Vault 实例以将 MACsec 机密存储在新的资源组中。

    ```azurepowershell-interactive
    New-AzResourceGroup -Name "your_resource_group" -Location "resource_location"
    $keyVault = New-AzKeyVault -Name "your_key_vault_name" -ResourceGroupName "your_resource_group" -Location "resource_location" -EnableSoftDelete 
    ```

    如果已有密钥保管库或资源组，可以重复使用。 但是，在现有的密钥保管库上启用[**软删除**功能](https://docs.microsoft.com/en-us/azure/key-vault/key-vault-ovw-soft-delete)非常重要。 如果未启用软删除，你可以使用以下命令来启用它：

    ```azurepowershell-interactive
    ($resource = Get-AzResource -ResourceId (Get-AzKeyVault -VaultName "your_existing_keyvault").ResourceId).Properties | Add-Member -MemberType "NoteProperty" -Name "enableSoftDelete" -Value "true"
    Set-AzResource -resourceid $resource.ResourceId -Properties $resource.Properties
    ```
2. 创建用户标识。

    ```azurepowershell-interactive
    $identity = New-AzUserAssignedIdentity  -Name "identity_name" -Location "resource_location" -ResourceGroupName "your_resource_group"
    ```

    如果 AzUserAssignedIdentity 未被识别为有效的 PowerShell cmdlet，请在管理员模式下安装以下模块并重新运行上述命令。

    ```azurepowershell-interactive
    Install-Module -Name Az.ManagedServiceIdentity
    ```
3. 创建连接关联密钥（CAK）和连接关联密钥名称（CKN），并将其存储在密钥保管库中。

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

   现在，此标识可以从密钥保管库中获取机密（例如 CAK 和 CKN）。
5. 设置 ExpressRoute 使用的此用户标识。

    ```azurepowershell-interactive
    $erIdentity = New-AzExpressRoutePortIdentity -UserAssignedIdentityId $identity.Id
    ```
 
## <a name="2-configure-macsec-on-expressroute-direct-ports"></a>2.在 ExpressRoute 直接端口上配置 MACsec

### <a name="to-enable-macsec"></a>启用 MACsec

每个 ExpressRoute 直接实例都有两个物理端口。 你可以选择在两个端口上同时启用 MACsec，或一次在一个端口上启用 MACsec。 一次执行一个端口（在为其他端口提供服务时将流量切换到活动端口）有助于最大限度地减少中断（如果 ExpressRoute 直接已在服务中）。

1. 设置 MACsec 的机密和密码，并将用户标识与此端口相关联，以便 ExpressRoute 管理代码可以访问 MACsec 机密（如果需要）。

    ```azurepowershell-interactive
    $erDirect = Get-AzExpressRoutePort -ResourceGroupName "your_resource_group" -Name "your_direct_port_name"
    $erDirect.Links[0]. MacSecConfig.CknSecretIdentifier = $MacSecCKNSecret.Id
    $erDirect.Links[0]. MacSecConfig.CakSecretIdentifier = $MacSecCAKSecret.Id
    $erDirect.Links[0]. MacSecConfig.Cipher = "gcm-aes-128"
    $erDirect.Links[1]. MacSecConfig.CknSecretIdentifier = $MacSecCKNSecret.Id
    $erDirect.Links[1]. MacSecConfig.CakSecretIdentifier = $MacSecCAKSecret.Id
    $erDirect.Links[1]. MacSecConfig.Cipher = "gcm-aes-128"
    $erDirect.identity = $erIdentity
    Set-AzExpressRoutePort -ExpressRoutePort $erDirect
    ```
2. 可有可无如果端口处于管理关闭状态，你可以运行以下命令来打开端口。

    ```azurepowershell-interactive
    $erDirect = Get-AzExpressRoutePort -ResourceGroupName "your_resource_group" -Name "your_direct_port_name"
    $erDirect.Links[0].AdminState = “Enabled”
    $erDirect.Links[1].AdminState = “Enabled”
    Set-AzExpressRoutePort -ExpressRoutePort $erDirect
    ```

    此时，将在 Microsoft 端的 ExpressRoute 直接端口上启用 MACsec。 如果你尚未在边缘设备上对其进行配置，则可以继续通过相同的 MACsec 机密和密码对其进行配置。

### <a name="to-disable-macsec"></a>禁用 MACsec

如果你的 ExpressRoute 直接实例上不再需要 MACsec，可以运行以下命令以禁用它。

```azurepowershell-interactive
$erDirect = Get-AzExpressRoutePort -ResourceGroupName "your_resource_group" -Name "your_direct_port_name"
$erDirect.Links[0]. MacSecConfig.CknSecretIdentifier = $null
$erDirect.Links[0]. MacSecConfig.CakSecretIdentifier = $null
$erDirect.Links[1]. MacSecConfig.CknSecretIdentifier = $null
$erDirect.Links[1]. MacSecConfig.CakSecretIdentifier = $null
$erDirect.identity = $null
Set-AzExpressRoutePort -ExpressRoutePort $erDirect
```

此时，Microsoft 端的 ExpressRoute 直接端口上禁用了 MACsec。

### <a name="test-connectivity"></a>测试连接
在 ExpressRoute 直接端口上配置 MACsec （包括 MACsec 密钥更新）后，请[检查](expressroute-troubleshooting-expressroute-overview.md)线路的 BGP 会话是否已启动并正在运行。 如果端口上还没有线路，请先创建一个，并设置该线路的 Azure 专用对等互连或 Microsoft 对等互连。 如果 MACsec 配置不正确，包括网络设备和 Microsoft 网络设备之间的 MACsec key 不匹配，则不会在第3层中看到 ARP 解析，并在第3层建立 BGP。 如果所有内容都已正确配置，则应看到在两个方向上正确公布的 BGP 路由，以及基于 ExpressRoute 的应用程序数据流。

## <a name="next-steps"></a>后续步骤
1. [在 ExpressRoute 直接上创建 ExpressRoute 线路](expressroute-howto-erdirect.md)
2. [将 ExpressRoute 线路链接到 Azure 虚拟网络](expressroute-howto-linkvnet-arm.md)
3. [验证 ExpressRoute 连接](expressroute-troubleshooting-expressroute-overview.md)
