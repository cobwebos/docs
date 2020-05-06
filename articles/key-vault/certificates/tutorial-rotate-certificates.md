---
title: 教程 - 更新 Key Vault 中证书的自动轮换频率 | Microsoft Docs
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
ms.openlocfilehash: 2e6c250a0bcb9d73e7c572dfe8138c31269993e8
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/29/2020
ms.locfileid: "82107554"
---
# <a name="tutorial-configuring-certificates-auto-rotation-in-key-vault"></a>教程：配置 Key Vault 中证书的自动轮换

使用 Azure Key Vault，你可以轻松预配、管理和部署数字证书。 它们可以是由证书颁发机构签名的公共和专用 SSL/TLS 证书，也可以是自签名证书。 Key Vault 还可以通过与证书颁发机构的合作关系来请求和续订证书，提供用于管理证书生命周期的强大解决方案。 在本教程中，你将更新证书的属性 - 有效期、自动轮换频率、CA。 有关 Key Vault 的详细信息，请参阅[概述](../general/overview.md)。

本教程介绍如何：

> [!div class="checklist"]
> * 使用 Azure 门户管理证书
> * 添加证书颁发机构提供商帐户
> * 更新证书的有效期
> * 更新证书的自动轮换频率
> * 使用 Azure Powershell 更新证书的属性


在开始之前，请阅读 [Key Vault 的基本概念](../general/basic-concepts.md)。 

如果没有 Azure 订阅，请在开始之前创建一个[免费帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

## <a name="sign-in-to-azure"></a>登录 Azure

通过 https://portal.azure.com 登录到 Azure 门户。

## <a name="create-a-vault"></a>创建保管库

创建或选择现有的 Key Vault 来执行操作。 [（创建密钥保管库的步骤）。](../quick-create-portal.md) 在示例中，保管库名称为 Example-Vault  。 

![Key Vault 创建完成后的输出](../media/certificates/tutorial-import-cert/vault-properties.png)

## <a name="create-a-certificate-in-key-vault"></a>在 Key Vault 中创建证书

在保管库中创建或导入证书。 [（在密钥保管库中创建证书的步骤）。](../quick-create-portal.md) 在本例中，我们使用名为 ExampleCertificate  的证书。

> [!NOTE]
> 在 Azure Key Vault 中，可以在创建证书时更新证书的生命周期属性，也可以在创建证书后更新。 
## <a name="updating-certificates-life-cycle-attributes"></a>更新证书的生命周期属性

在 Key Vault 中创建的证书可以是 
- 自签名证书
- 与 Key Vault 合作的证书颁发机构 (CA) 创建的证书
- 不与 Key Vault 合作的证书颁发机构创建的证书

以下证书颁发机构是当前与 Key Vault 合作的提供商：
- DigiCert - Key Vault 提供 DigiCert 的 OV TLS/SSL 证书。
- GlobalSign - Key Vault 提供 GlobalSign 的 OV TLS/SSL 证书。

Azure Key Vault 通过与证书颁发机构合作来自动轮换证书。 通过建立该合作关系，Key Vault 自动请求并续订证书。 因此，自动轮换功能不适用于未与 Key Vault 合作的 CA 创建的证书。  

> [!NOTE]
> CA 提供商的帐户管理员创建可供 Key Vault 使用的凭据，以便通过 Key Vault 创建、续订和使用 TLS/SSL 证书。
![证书颁发机构](../media/certificates/tutorial-rotate-cert/cert-authority-create.png)
> 


### <a name="updating-certificates-life-cycle-attributes-at-the-time-of-certificate-creation"></a>在创建证书时更新证书的生命周期属性

1. 在密钥保管库属性页中，选择“证书”  。
2. 单击“生成/导入”  。
3. 在“创建证书”  屏幕上，更新以下值：
    

    - **有效期**：输入值（以月为单位）。 建议的安全做法是创建短期证书。 默认情况下，新创建的证书的有效期为 12 个月。
    - **生存期操作类型**：选择证书的自动续订和警报操作。 根据选择，更新“生存期到达的百分比”或“距离到期还剩的天数”。 默认情况下，证书的自动续订设置为其生存期的 80%。<br> 从下拉菜单中，选择以下选项：

    |  在给定时间自动续订| 在给定时间向所有联系人发送电子邮件 |
    |-----------|------|
    |选择此选项会启用自动轮换 | 选择此选项不会自动轮换，只会提醒联系人|
        


4. 单击“创建”。 

![证书生命周期](../media/certificates/tutorial-rotate-cert/create-cert-lifecycle.png)

### <a name="updating-life-cycle-attributes-of-stored-certificate"></a>更新已存储证书的生命周期属性

1. 选择 Key Vault。
2. 在密钥保管库属性页中，选择“证书”  。
3. 选择要更新的证书。 在本例中，我们将使用名为 ExampleCertificate  的证书。
4. 从顶部菜单栏中选择“颁发策略”  。

![证书属性](../media/certificates/tutorial-rotate-cert/cert-issuance-policy.png)
5. 在“颁发策略”  屏幕上，更新以下值：
- **有效期**：更新值（以月为单位）。
- **生存期操作类型**：选择证书的自动续订和警报操作。 根据选择，更新“生存期到达的百分比”或“距离到期还剩的天数”。 

![证书属性](../media/certificates/tutorial-rotate-cert/cert-policy-change.png)
6. 单击“保存”  。

> [!IMPORTANT]
> 更改证书的“生存期操作类型”会立即记录对现有证书的修改。


### <a name="updating-certificates-attributes-using-powershell"></a>使用 PowerShell 更新证书的属性

```azurepowershell


Set-AzureKeyVaultCertificatePolicy -VaultName $vaultName 
                                   -Name $certificateName 
                                   -RenewAtNumberOfDaysBeforeExpiry [276 or appropriate calculated value]
```

> [!TIP]
> 若要修改一系列证书的续订策略，请输入包含 VaultName,CertName 的 File.csv <br/>
>  vault1,Cert1 <br/>
>  vault2,Cert2
>
>  ```azurepowershell
>  $file = Import-CSV C:\Users\myfolder\ReadCSVUsingPowershell\File.csv 
> foreach($line in $file)
> {
> Set-AzureKeyVaultCertificatePolicy -VaultName $vaultName -Name $certificateName -RenewAtNumberOfDaysBeforeExpiry [276 or appropriate calculated value]
> }
>  ```
> 
请在[此处](https://docs.microsoft.com/cli/azure/keyvault/certificate?view=azure-cli-latest#az-keyvault-certificate-set-attributes)详细了解这些参数

## <a name="clean-up-resources"></a>清理资源

其他 Key Vault 快速入门和教程是在本快速入门的基础上制作的。 如果打算继续使用后续的快速入门和教程，则可能需要保留这些资源。
如果不再需要资源组，可以将其删除，这将删除 Key Vault 和相关的资源。 要通过门户删除资源组，请执行以下操作：

1. 在门户顶部的“搜索”框中输入资源组的名称。 在搜索结果中看到在本快速入门中使用的资源组后，将其选中。
2. 选择“删除资源组”  。
3. 在“键入资源组名称:”框中，键入资源组的名称，然后选择“删除”   。


## <a name="next-steps"></a>后续步骤

在本教程中，你更新了一个证书的生命周期。 若要详细了解 Key Vault 以及如何将其与应用程序集成，请继续阅读以下文章。

详细了解如何[在 Azure Key Vault 中管理证书创建](https://docs.microsoft.com/azure/key-vault/certificates/create-certificate-scenarios)
- 查看 [Key Vault 概述](../general/overview.md)