---
title: 将 Key Vault 与 DigiCert 证书颁发机构集成
description: 如何将 Key Vault 与 DigiCert 证书颁发机构集成
services: key-vault
author: msmbaldwin
manager: rkarlin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: certificates
ms.topic: tutorial
ms.date: 06/02/2020
ms.author: sebansal
ms.openlocfilehash: d6cb8d386efbcdaf053f0740d955b249c0e636b4
ms.sourcegitcommit: 34eb5e4d303800d3b31b00b361523ccd9eeff0ab
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/17/2020
ms.locfileid: "84907800"
---
# <a name="integrating-key-vault-with-digicert-certificate-authority"></a>将 Key Vault 与 DigiCert 证书颁发机构集成

Azure Key Vault 使你能轻松地为网络预配、管理和部署数字证书，并支持应用程序的安全通信。 数字证书是一种电子凭据，用于在电子交易中建立身份证明。 

Azure 密钥保管库用户可以直接从其 Key Vault 生成 DigiCert 证书。 Key Vault 可以通过 Key Vault 与 DigiCert 证书颁发机构的受信任的合作关系，确保 DigiCert 颁发的证书的端到端证书生命周期管理。

有关证书的常规详细信息，请参阅 [Azure Key Vault 证书](/azure/key-vault/certificates/about-certificates)。

如果没有 Azure 订阅，请在开始之前创建一个[免费帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

## <a name="prerequisites"></a>先决条件

要完成本指南，必须备好以下资源。
* 一个 Key Vault。 可以使用现有的 Key Vault，或者遵循以下快速入门之一中的步骤创建一个新的 Key Vault：
   - [使用 Azure CLI 创建 Key Vault](../secrets/quick-create-cli.md)
   - [使用 Azure PowerShell 创建 Key Vault](../secrets/quick-create-powershell.md)
   - [使用 Azure 门户创建 Key Vault](../secrets/quick-create-portal.md)
*   需要激活 DigiCert CertCentral 帐户。 [注册](https://www.digicert.com/account/signup/) CertCentral 帐户。
*   帐户中的管理员级别权限。


### <a name="before-you-begin"></a>开始之前

确保你的 DigiCert CertCentral 帐户提供以下信息：
-   CertCentral 帐户 ID
-   组织 ID
-   API 密钥

## <a name="adding-certificate-authority-in-key-vault"></a>在 Key Vault 中添加证书颁发机构 
从 DigiCert CertCentral 帐户收集上述信息后，现在可以在密钥保管库中将 DigiCert 添加到证书颁发机构列表中。

### <a name="azure-portal"></a>Azure 门户

1.  要添加 DigiCert 证书颁发机构，请导航到要添加 DigiCert 的密钥保管库。 
2.  在密钥保管库属性页中，选择“证书”。
3.  选择“证书颁发机构”选项卡。![证书属性](../media/certificates/how-to-integrate-certificate-authority/select-certificate-authorities.png)
4.  选择“添加”选项。
 ![证书属性](../media/certificates/how-to-integrate-certificate-authority/add-certificate-authority.png)
5.  在“创建证书颁发机构”屏幕上，选择以下值：
    -   名称：添加可识别的颁发者名称。 示例 DigicertCA
    -   **提供**：从菜单中选择“DigiCert”。
    -   **帐户 ID**：输入你的 DigiCert CertCentral 帐户 ID
    -   **帐户密码**：输入在 DigiCert CertCentral 帐户中生成的 API 密钥
    -   **组织 ID**：输入从 DigiCert CertCentral 帐户收集的 OrgID 
    -   单击“创建”。
   
6.  你会看到 DigicertCA 现已添加到证书颁发机构列表中。


### <a name="azure-powershell"></a>Azure PowerShell

Azure PowerShell 用于通过命令或脚本创建和管理 Azure 资源。 Azure 托管 Azure Cloud Shell（一个可通过浏览器中的 Azure 门户使用的交互式 shell 环境）。

如果选择在本地安装并使用 PowerShell，则本教程需要 Azure PowerShell 模块 1.0.0 或更高版本。 键入 `$PSVersionTable.PSVersion` 即可查找版本。 如果需要升级，请参阅[安装 Azure PowerShell 模块](/powershell/azure/install-az-ps)。 如果在本地运行 PowerShell，则还需运行 `Login-AzAccount` 来创建与 Azure 的连接。

```azurepowershell-interactive
Login-AzAccount
```

1.  创建“资源组”

使用 [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) 创建 Azure 资源组。 资源组是在其中部署和管理 Azure 资源的逻辑容器。 

```azurepowershell-interactive
New-AzResourceGroup -Name ContosoResourceGroup -Location EastUS
```

2. 创建 Key Vault

必须对密钥保管库使用唯一的名称。 在本指南中，“Contoso-Vaultname”是 Key Vault 的名称。

- “保管库名称”Contoso-Vaultname。
- 资源组名称 **ContosoResourceGroup**。
- “位置”EastUS。

```azurepowershell-interactive
New-AzKeyVault -Name 'Contoso-Vaultname' -ResourceGroupName 'ContosoResourceGroup' -Location 'EastUS'
```

3. 为从 DigiCert CertCentral 帐户收集的信息定义变量。

- 定义“帐户 ID”变量
- 定义“组织 ID”变量
- 定义“API 密钥”变量
- 定义“颁发者名称”变量

```azurepowershell-interactive
$accountId = "myDigiCertCertCentralAccountID"
$org = New-AzureKeyVaultCertificateOrganizationDetails -Id OrganizationIDfromDigiCertAccount
$secureApiKey = ConvertTo-SecureString DigiCertCertCentralAPIKey -AsPlainText –Force
$issuerName = "DigiCertCA"
```

4. 设置“颁发者”。 这将在密钥保管库中添加 Digicert 作为证书颁发机构。
```azurepowershell-interactive
Set-AzureKeyVaultCertificateIssuer -VaultName $vaultName -IssuerName $issuerName -IssuerProvider DigiCert -AccountId $accountId -ApiKey $secureApiKey -OrganizationDetails $org
```

5. 直接在 Key Vault 的 DigiCert 中设置证书的策略并颁发证书。

```azurepowershell-interactive
$Policy = New-AzKeyVaultCertificatePolicy -SecretContentType "application/x-pkcs12" -SubjectName "CN=contoso.com" -IssuerName DigiCertCA -ValidityInMonths 12 -RenewAtNumberOfDaysBeforeExpiry 60
Add-AzKeyVaultCertificate -VaultName "Contoso-Vaultname" -Name "ExampleCertificate" -CertificatePolicy $Policy
```

通过此集成，Digicert CA 现已在指定的 Key Vault 内成功颁发证书。

## <a name="troubleshoot"></a>疑难解答

如果颁发的证书在 Azure 门户中处于“禁用”状态，请继续查看“证书操作”以查看该证书的 DigiCert 错误消息。

 ![证书属性](../media/certificates/how-to-integrate-certificate-authority/certificate-operation-select.png)

有关详细信息，请参阅 [Key Vault REST API 中的证书操作参考](/rest/api/keyvault)。 有关建立权限的信息，请参阅[保管库 - 创建或更新](/rest/api/keyvault/vaults/createorupdate)和[保管库 - 更新访问策略](/rest/api/keyvault/vaults/updateaccesspolicy)。

## <a name="next-steps"></a>后续步骤

- [身份验证、请求和响应](../general/authentication-requests-and-responses.md)
- [Key Vault 开发人员指南](../general/developers-guide.md)
