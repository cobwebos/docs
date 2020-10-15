---
title: 教程 - 更新 Key Vault 中的证书自动轮换频率 | Microsoft Docs
description: 本教程展示了如何使用 Azure 门户更新 Azure Key Vault 中证书的自动轮换频率
services: key-vault
author: msmbaldwin
manager: rkarlin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: certificates
ms.topic: tutorial
ms.custom: mvc
ms.date: 04/16/2020
ms.author: sebansal
ms.openlocfilehash: eeceb1279579055bfff33f0a4413f0798418faed
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "83201511"
---
# <a name="tutorial-configure-certificate-auto-rotation-in-key-vault"></a>教程：配置 Key Vault 中的证书自动轮换

可以使用 Azure Key Vault 轻松预配、管理和部署数字证书。 证书可以是由证书颁发机构 (CA) 签署的公共和私有安全套接字层 (SSL)/传输层安全性 (TLS) 证书，也可以是自签名证书。 Key Vault 还可以通过与 CA 的合作关系来请求和续订证书，提供用于管理证书生命周期的强大解决方案。 在本教程中，你将更新证书的有效期、自动轮换频率和 CA 属性。

本教程介绍如何：

> [!div class="checklist"]
> * 使用 Azure 门户管理证书。
> * 添加 CA 提供者帐户。
> * 更新证书的有效期。
> * 更新证书的自动轮换频率。
> * 使用 Azure PowerShell 更新证书的属性。

在开始之前，请阅读 [Key Vault 的基本概念](../general/basic-concepts.md)。

如果没有 Azure 订阅，请在开始之前创建一个[免费帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

## <a name="sign-in-to-azure"></a>登录 Azure

通过 https://portal.azure.com 登录到 Azure 门户。

## <a name="create-a-vault"></a>创建保管库

创建密钥保管库或选择现有保管库以执行操作（请参阅[创建密钥保管库的步骤](../quick-create-portal.md)）。 在以下示例中，密钥保管库名称为 Example-Vault。

![密钥保管库创建完成后的输出](../media/certificates/tutorial-import-cert/vault-properties.png)

## <a name="create-a-certificate-in-key-vault"></a>在 Key Vault 中创建证书

创建证书或将证书导入到密钥保管库中（请参阅[在 Key Vault 中创建证书的步骤](../quick-create-portal.md)）。 在本例中，你将使用名为 ExampleCertificate 的证书。

## <a name="update-certificate-lifecycle-attributes"></a>更新证书生命周期属性

在 Azure Key Vault 中，可以在创建证书之前和之后更新证书的生命周期属性。

在 Key Vault 中创建的证书可以是：

- 自签名证书。
- 通过与 Key Vault 合作的 CA 创建的证书。
- 通过未与 Key Vault 合作的 CA 创建的证书。

以下 CA 是目前与 Key Vault 合作的提供者：

- DigiCert：Key Vault 提供 OV TLS/SSL 证书。
- GlobalSign：Key Vault 提供 OV TLS/SSL 证书。

Key Vault 通过与 CA 建立的合作关系自动轮换证书。 由于 Key Vault 通过合作关系自动请求和续订证书，因此对于通过未与 Key Vault 合作的 CA 创建的证书，自动轮换功能不适用。

> [!NOTE]
> CA 提供者的帐户管理员会创建可供 Key Vault 用来创建、续订和使用 TLS/SSL 证书的凭据。
![证书颁发机构](../media/certificates/tutorial-rotate-cert/cert-authority-create.png)
>

### <a name="update-certificate-lifecycle-attributes-at-the-time-of-creation"></a>在创建时更新证书生命周期属性

1. 在密钥保管库属性页中，选择“证书”。
1. 选择“生成/导入”。
1. 在“创建证书”屏幕上，更新以下值：

   - **有效期**：输入值（以月为单位）。 建议的安全做法是创建短期证书。 默认情况下，新创建的证书的“有效期”值为 12 个月。
   - **生存期操作类型**：选择证书的自动续订和警报操作，然后更新“生存期到达的百分比”或“距离到期还剩的天数”。 默认情况下，证书的自动续订设置为其生存期的 80%。 从下拉菜单中选择以下选项之一。

        |  在给定时间自动续订| 在给定时间向所有联系人发送电子邮件 |
        |-----------|------|
        |选择此选项会启用自动轮换。 | 选择此选项不会自动轮换，而只会提醒联系人。|

1. 选择“创建”。

![证书生命周期](../media/certificates/tutorial-rotate-cert/create-cert-lifecycle.png)

### <a name="update-lifecycle-attributes-of-a-stored-certificate"></a>更新已存储证书的生命周期属性

1. 选择密钥保管库。
1. 在密钥保管库属性页中，选择“证书”。
1. 选择要更新的证书。 在本例中，你将使用名为 ExampleCertificate 的证书。
1. 从顶部菜单栏中选择“颁发策略”。

   ![证书属性](../media/certificates/tutorial-rotate-cert/cert-issuance-policy.png)

1. 在“颁发策略”屏幕上，更新以下值：

   - **有效期**：更新值（以月为单位）。
   - **生存期操作类型**：选择证书的自动续订和警报操作，然后更新“生存期到达的百分比”或“距离到期还剩的天数”。

   ![证书属性](../media/certificates/tutorial-rotate-cert/cert-policy-change.png)

1. 选择“保存”。

> [!IMPORTANT]
> 更改证书的“生存期操作类型”会立即记录对现有证书的修改。


### <a name="update-certificate-attributes-by-using-powershell"></a>使用 PowerShell 更新证书属性

```azurepowershell


Set-AzureKeyVaultCertificatePolicy -VaultName $vaultName 
                                   -Name $certificateName 
                                   -RenewAtNumberOfDaysBeforeExpiry [276 or appropriate calculated value]
```

> [!TIP]
> 若要修改一组证书的续订策略，请输入包含 `VaultName,CertName` 的 `File.csv`，如以下示例中所示：
> <br/>
 `vault1,Cert1` <br/>
>  `vault2,Cert2`
>
>  ```azurepowershell
>  $file = Import-CSV C:\Users\myfolder\ReadCSVUsingPowershell\File.csv 
> foreach($line in $file)
> {
> Set-AzureKeyVaultCertificatePolicy -VaultName $vaultName -Name $certificateName -RenewAtNumberOfDaysBeforeExpiry [276 or appropriate calculated value]
> }
>  ```
> 
若要详细了解参数，请参阅 [az keyvault certificate](https://docs.microsoft.com/cli/azure/keyvault/certificate?view=azure-cli-latest#az-keyvault-certificate-set-attributes)。

## <a name="clean-up-resources"></a>清理资源

其他 Key Vault 教程基于本教程编写。 如果你计划使用这些教程，那么你可能需要将这些现有资源保留在原处。
不再需要这些资源时，可以将资源组删除，这将删除密钥保管库和相关的资源。

要使用门户删除资源组，请执行以下操作：

1. 在门户顶部的“搜索”框中输入资源组的名称。 当本快速入门中使用的资源组显示在搜索结果中时，请将其选中。
1. 选择“删除资源组”。
1. 在“键入资源组名称:”框中，键入资源组的名称，然后选择“删除” 。


## <a name="next-steps"></a>后续步骤

在本教程中，你更新了一个证书的生命周期属性。 若要详细了解 Key Vault 以及如何将其与应用程序集成，请继续阅读以下文章：

- 详细了解如何[在 Azure Key Vault 中管理证书创建](https://docs.microsoft.com/azure/key-vault/certificates/create-certificate-scenarios)。
- 查看 [Key Vault 概述](../general/overview.md)。