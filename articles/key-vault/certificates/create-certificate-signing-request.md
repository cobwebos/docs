---
title: 在 Azure Key Vault 中创建和合并 CSR
description: 在 Azure Key Vault 中创建和合并 CSR
services: key-vault
author: msmbaldwin
manager: rkarlin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: certificates
ms.topic: tutorial
ms.date: 06/17/2020
ms.author: sebansal
ms.openlocfilehash: 44d77c36b9aacb8a2f06fd7a0f167cffa06ae4eb
ms.sourcegitcommit: 6fc156ceedd0fbbb2eec1e9f5e3c6d0915f65b8e
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/21/2020
ms.locfileid: "88716106"
---
# <a name="creating-and-merging-csr-in-key-vault"></a>在 Key Vault 中创建和合并 CSR

Azure Key Vault 支持将你选择的任何证书颁发机构颁发的数字证书存储在密钥保管库中。 它支持使用私钥/公钥对创建证书签名请求，可由所选的任何证书颁发机构签名。 选择的证书颁发机构可以是内部企业 CA，也可以是外部公共 CA。 证书签名请求（也称为 CSR 或证书请求）是用户向证书颁发机构 (CA) 发送的一条消息，用于请求颁发数字证书。

若要详细了解证书的常规信息，请参阅 [Azure Key Vault 证书](/azure/key-vault/certificates/about-certificates)。

如果没有 Azure 订阅，请在开始之前创建一个[免费帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

## <a name="adding-certificate-in-key-vault-issued-by-a-non-trusted-ca"></a>在 Key Vault 中添加不受信任的 CA 颁发的证书

以下步骤将帮助你从没有与 Key Vault 合作的证书颁发机构（例如，GoDaddy 不是受信任的密钥保管库 CA）创建证书 


### <a name="azure-powershell"></a>Azure PowerShell



1.  首先，创建证书策略。 由于此方案中所选的 CA 不受支持，Key Vault 不会代表用户注册或续订证书颁发者颁发的证书，因此 IssuerName 设置为“未知”。

    ```azurepowershell
    $policy = New-AzKeyVaultCertificatePolicy -SubjectName "CN=www.contosoHRApp.com" -ValidityInMonths 1  -IssuerName Unknown
    ```


2. 创建证书签名请求

   ```azurepowershell
   $csr = Add-AzKeyVaultCertificate -VaultName ContosoKV -Name ContosoManualCSRCertificate -CertificatePolicy $policy
   $csr.CertificateSigningRequest
   ```

3. 获取由 CA 签名的 CSR 请求。`$certificateOperation.CertificateSigningRequest` 是针对该证书的 base4 编码的证书签名请求。 可以获取此 blob，并将其转储到证书颁发者的证书请求网站中。 此步骤因 CA 而异，最好的方法是查看 CA 提供的关于如何执行此步骤的指南。 此外，还可以使用 certreq 或 openssl 之类的工具来对证书请求进行签名，并完成证书生成过程。


4. 在 Key Vault 中合并已签名的请求。证书颁发者对证书请求进行签名后，可以带回已签名的证书，并将其与在 Azure Key Vault 中创建的初始私钥/公钥对合并

    ```azurepowershell-interactive
    Import-AzKeyVaultCertificate -VaultName ContosoKV -Name ContosoManualCSRCertificate -FilePath C:\test\OutputCertificateFile.cer
    ```

    现已成功合并证书请求。

### <a name="azure-portal"></a>Azure 门户

1.  若要为所选的 CA 生成 CSR，请导航到要添加证书的密钥保管库。
2.  在密钥保管库属性页中，选择“证书”。
3.  选择“生成/导入”选项卡。
4.  在“创建证书”屏幕上，选择以下值：
    - 证书创建方法：生成。
    - 证书名称：ContosoManualCSRCertificate。
    - 证书颁发机构 (CA) 类型：非集成 CA 颁发的证书
    - 主题：`"CN=www.contosoHRApp.com"`
    - 根据需要选择其他值。 单击“创建”。

    ![证书属性](../media/certificates/create-csr-merge-csr/create-certificate.png)
6.  此时，将看到证书已添加到“证书”列表中。 选择刚创建的新证书。 证书的当前状态为“已禁用”，因为它尚未由 CA 颁发。
7. 单击“证书操作”选项卡，然后选择“下载 CSR” 。
 ![证书属性](../media/certificates/create-csr-merge-csr/download-csr.png)

8.  将 .csr 文件带到 CA，以便对请求进行签名。
9.  CA 对请求进行签名后，请带回证书文件以在同一“证书操作”屏幕中合并已签名的请求。

现已成功合并证书请求。

## <a name="adding-more-information-to-csr"></a>向 CSR 添加更多信息

如果想要在创建 CSR 时添加更多信息，例如 - 
    - 国家/地区：
    - 城市/区域：
    - 省/自治区/直辖市：
    - 组织：
    - 组织单位：可以在创建 CSR 时通过在 subjectName 中定义这些信息来添加所有这些信息。

示例
    ```SubjectName="CN = docs.microsoft.com, OU = Microsoft Corporation, O = Microsoft Corporation, L = Redmond, S = WA, C = US"
    ```

>[!Note]
>如果你正在 CSR 中请求具有所有这些详细信息的 DV 证书，则 CA 可能会拒绝该请求，因为 CA 可能无法验证请求中的所有信息。 如果你正在请求 OV 证书，那么在 CSR 中添加所有这些信息更合适。


## <a name="troubleshoot"></a>疑难解答

- 错误类型“指定的 X.509 证书内容中终端实体证书的公钥与指定私钥的公共部分不一致。请检查证书是否有效”。如果你不将 CSR 与启动的同一 CSR 请求合并，则会发生此错误。 每次创建 CSR 时，它都会创建一个在合并签名请求时必须匹配的私钥。
    
- 如果颁发的证书在 Azure 门户中处于“已禁用”状态，请继续查看“证书操作”以查看该证书的错误消息。

有关详细信息，请参阅 [Key Vault REST API 中的证书操作参考](/rest/api/keyvault)。 有关建立权限的信息，请参阅[保管库 - 创建或更新](/rest/api/keyvault/vaults/createorupdate)和[保管库 - 更新访问策略](/rest/api/keyvault/vaults/updateaccesspolicy)。

## <a name="next-steps"></a>后续步骤

- [身份验证、请求和响应](../general/authentication-requests-and-responses.md)
- [Key Vault 开发人员指南](../general/developers-guide.md)
