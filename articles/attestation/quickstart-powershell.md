---
title: 使用 Azure PowerShell 设置 Azure 证明
description: 如何使用 Azure PowerShell 设置和配置证明提供程序。
services: attestation
author: msmbaldwin
ms.service: attestation
ms.topic: overview
ms.date: 08/31/2020
ms.author: mbaldwin
ms.openlocfilehash: 70e86e01a9d37a27620d451bcd5d035dfcb4573d
ms.sourcegitcommit: d68c72e120bdd610bb6304dad503d3ea89a1f0f7
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/01/2020
ms.locfileid: "89236840"
---
# <a name="quickstart-set-up-azure-attestation-with-azure-powershell"></a>快速入门：使用 Azure PowerShell 设置 Azure 证明

请按照以下步骤使用 Azure PowerShell 设置和配置证明提供程序。 要了解如何安装和运行 Azure PowerShell，请参阅 [Azure PowerShell 概述](/powershell/azure/?view=azps-2.8.0&viewFallbackFrom=azps-2.4.0)。

请注意，PowerShell 库已弃用传输层安全性 (TLS) 版本 1.0 和 1.1。 建议使用 TLS 1.2 或更高版本。 因此可能会收到以下错误：

- 警告：无法解析包源“https://www.powershellgallery.com/api/v2”
- PackageManagement\Install-Package：找不到与指定搜索条件和模块名称匹配的项 

若要继续与 PowerShell 库进行交互，请在 Install-Module 命令之前运行以下命令

```powershell
[Net.ServicePointManager]::SecurityProtocol = [Net.SecurityProtocolType]::Tls12 
```

## <a name="install-azattestation-powershell-module"></a>安装 Az.Attestation PowerShell 模块

在具有 Azure PowerShell 的计算机上，安装 Az.Attestation PowerShell 模块，其中包含 Azure 证明的 cmdlet。  

### <a name="initial-installation"></a>初始安装

终止所有现有 PowerShell 窗口。

若要为“当前用户”安装，请启动非提升的 PowerShell 窗口并运行：

```powershell
Install-Module -Name Az.Attestation -AllowClobber -Scope CurrentUser
```

若要为“所有用户”安装，请启动提升的 PowerShell 窗口并运行：

```powershell
Install-Module -Name Az.Attestation -AllowClobber -Scope AllUsers
```

关闭提升的 PowerShell 控制台。

### <a name="update-the-installation"></a>更新安装

终止所有现有 PowerShell 窗口。

若要为“当前用户”更新，请启动非提升的 PowerShell 窗口并运行：

```powershell
Update-Module -Name Az.Attestation
```

若要为“所有用户”更新，请启动提升的 PowerShell 窗口并运行：

```powershell
Update-Module -Name Az.Attestation
```

关闭提升的 PowerShell 控制台。

### <a name="get-installed-modules"></a>获取已安装的模块

支持证明操作所需的 Az 模块的最低版本：
- Az 4.5.0
- Az.Accounts 1.9.2
- Az.Attestation 0.1.8

运行以下命令，验证所有 Az 模块的已安装版本 

```powershell
Get-InstalledModule
```
如果版本与最低要求不匹配，请运行 Update-Module 命令。

## <a name="sign-in-to-azure"></a>登录 Azure

在 PowerShell 控制台中登录 Azure（无需提升的访问特权）。

```powershell
Connect-AzAccount
```

如果需要，请切换到要用于 Azure 证明的订阅。

```powershell
Set-AzContext -Subscription <subscription id>  
```

## <a name="register-microsoftattestation-resource-provider"></a>注册 Microsoft.Attestation 资源提供程序

在订阅中注册 Microsoft.Attestation 资源提供程序。 有关 Azure 资源提供程序以及如何配置和管理资源提供程序的详细信息，请参阅 [Azure 资源提供程序和类型](../azure-resource-manager/management/resource-providers-and-types.md)。 请注意，对于订阅，只需注册一次资源提供程序。

```powershell
Register-AzResourceProvider -ProviderNamespace Microsoft.Attestation
```
## <a name="regional-availability-of-azure-attestation"></a>Azure 证明的区域可用性

```powershell
(Get-AzResourceProvider -ProviderNamespace Microsoft.Attestation)[0].Locations
```

## <a name="create-an-azure-resource-group"></a>创建 Azure 资源组

为证明提供程序创建资源组。 请注意，其他 Azure 资源（包括具有客户端应用程序实例的虚拟机）可以放在同一资源组中。

```powershell
$location = "uksouth" 
$attestationResourceGroup = "<attestation provider resource group name>"
New-AzResourceGroup -Name $attestationResourceGroup -Location $location 
```

## <a name="create-and-manage-an-attestation-provider"></a>创建和管理证明提供程序

New-AzAttestation 创建证明提供程序。

```powershell
$attestationProvider = "<attestation provider name>" 
New-AzAttestation -Name $attestationProvider -ResourceGroupName $attestationResourceGroup -Location $location
```

PolicySignerCertificateFile 是用于指定一组受信任的签名密钥的文件。 如果为 PolicySignerCertificateFile 参数指定了文件名，则只能使用已签名 JWT 格式的策略配置证明提供程序。 否则，可以以文本或未签名 JWT 格式配置策略。

```powershell
New-AzAttestation -Name $attestationProvider -ResourceGroupName $attestationResourceGroup -Location $location -PolicySignersCertificateFile "C:\test\policySignersCertificates.pem"
```

有关 PolicySignersCertificateFile 示例，请参阅[策略签名者证书的示例](policy-signer-examples.md)。

Get-AzAttestation 检索证明提供程序属性（如 status 和 AttestURI）。 记下 AttestURI，因为稍后需要用到它。

```azurepowershell
Get-AzAttestation -Name $attestationProvider -ResourceGroupName $attestationResourceGroup  
```

上述命令应生成如下所示的输出： 

```
Id:/subscriptions/MySubscriptionID/resourceGroups/MyResourceGroup/providers/Microsoft.Attestation/attestationProviders/MyAttestationProvider
Location: MyLocation
ResourceGroupName: MyResourceGroup
Name: MyAttestationProvider
Status: Ready
TrustModel: AAD
AttestUri: https://MyAttestationProvider.us.attest.azure.net 
Tags: 
TagsTable: 
```

可以使用 Remove-AzAttestation cmdlet 删除证明提供程序。  

``azurepowershell Remove-AzAttestation -Name $attestationProvider -ResourceGroupName $attestationResourceGroup
```

## Policy management

In order to manage policies, an Azure AD user requires the following permissions for "Actions":
- Microsoft.Attestation/attestationProviders/attestation/read
- Microsoft.Attestation/attestationProviders/attestation/write
- Microsoft.Attestation/attestationProviders/attestation/delete

These permissions can be assigned to an AD user through a role such as "Owner" (wildcard permissions), "Contributor" (wildcard permissions) or "Attestation Contributor" (specific permissions for Azure Attestation only).  

In order to read policies, an Azure AD user requires the following permission for "Actions":
- Microsoft.Attestation/attestationProviders/attestation/read

This permission can be assigned to an AD user through a role such as "Reader" (wildcard permissions) or "Attestation Reader" (specific permissions for Azure Attestation only).

Below PowerShell cmdlets provide policy management for an attestation provider (one TEE at a time).

Get-AzAttestationPolicy returns the current policy for the specified TEE. The cmdlet displays policy in both text and JWT format of the policy.

```powershell
$teeType = "<tee Type>"
Get-AzAttestationPolicy   -Name $attestationProvider -ResourceGroupName $attestationResourceGroup -Tee $teeType 
```

支持的 TEE 类型为“sgxenclave”和“vbsenclave”。

Set-AttestationPolicy 为指定的 TEE 设置新策略。 cmdlet 接受文本或 JWT 格式的策略，并由 PolicyFormat 参数控制。 “Text”是 PolicyFormat 的默认值。 

```powershell
$policyFormat = "<policy format>"
$policy=Get-Content -path "C:\test\policy.txt" -Raw
Set-AzAttestationPolicy   -Name $attestationProvider -ResourceGroupName $attestationResourceGroup -Tee $teeType -Policy $policy -PolicyFormat $policyFormat 
```

如果在创建证明提供程序的过程中提供 PolicySignerCertificateFile，则只能以已签名的 JWT 格式配置策略。 否则，可以以文本或未签名 JWT 格式配置策略。

JWT 格式的证明策略必须包含名为“AttestationPolicy”的声明。 对于已签名的策略，必须用与任何现有策略签名者证书相对应的私钥对 JWT 进行签名。

有关策略示例，请参阅[证明策略的示例](policy-examples.md)。

Reset-AzAttestationPolicy 将指定 TEE 的策略重置为默认值。

```powershell
Reset-AzAttestationPolicy -Name $attestationProvider -ResourceGroupName $attestationResourceGroup -Tee $teeType 
```

## <a name="policy-signer-certificates-management"></a>策略签名者证书管理

下面的 PowerShell cmdlet 为证明提供程序提供策略签名者证书管理：

```powershell
Get-AzAttestationPolicySigners -Name $attestationProvider -ResourceGroupName $attestationResourceGroup

Add-AzAttestationPolicySigner -Name $attestationProvider -ResourceGroupName $attestationResourceGroup -Signer <signer>

Remove-AzAttestationPolicySigner -Name $attestationProvider -ResourceGroupName $attestationResourceGroup -Signer <signer>
```

策略签名者证书是带有名为“maa-policyCertificate”的声明的签名 JWT。 声明的值为 JWK，其中包含要添加的受信任签名密钥。 必须用与任何现有策略签名者证书相对应的私钥对 JWT 进行签名。

请注意，策略签名者证书的所有语义操作都必须在 PowerShell 之外完成。 就 PowerShell 而言，它是一个简单的字符串。

有关策略签名者证书示例，请参阅[策略签名者证书的示例](policy-signer-examples.md)。

有关 cmdlet 及其参数的详细信息，请参阅 [Azure 证明 PowerShell cmdlet](/powershell/module/az.attestation/?view=azps-4.3.0#attestation) 

## <a name="next-steps"></a>后续步骤

- [如何创作证明策略并对其签名](author-sign-policy.md)
- [使用代码示例证明 SGX enclave](https://docs.microsoft.com/samples/browse/?expanded=azure&terms=attestation)
