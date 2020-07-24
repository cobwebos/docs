---
title: 常见问题-Azure Key Vault 证书导入
description: 常见问题-Azure Key Vault 证书导入
services: key-vault
author: msmbaldwin
manager: rkarlin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: certificates
ms.topic: conceptual
ms.date: 07/20/2020
ms.author: sebansal
ms.openlocfilehash: 1063f7189de4bdf1aaca4a6d72c979476433c32f
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/23/2020
ms.locfileid: "87095996"
---
# <a name="frequently-asked-questions---azure-key-vault-certificate-import"></a>常见问题-Azure Key Vault 证书导入

## <a name="frequently-asked-questions"></a>常见问题解答

### <a name="how-can-i-import-a-certificate-in-azure-key-vault"></a>如何在 Azure Key Vault 中导入证书？

导入证书–对于导入操作，Azure 密钥保管库接受两种证书格式： PEM 和 PFX。 尽管只有一个公共部分的 PEM 文件，但 Azure 密钥保管库只需在文件文件夹和私钥上接受 PEM 或 PFX。 遵循[教程导入证书](https://docs.microsoft.com/azure/key-vault/certificates/tutorial-import-certificate#import-a-certificate-to-key-vault)

### <a name="after-importing-password-protected-certificate-into-the-key-vault-and-then-downloading-it-i-am-not-able-to-see-the-password-associated-with-the-certificate"></a>将受密码保护的证书导入到密钥保管库，然后下载它后，我无法看到与证书关联的密码？
    
在存储到密钥保管库中后，已上传的受保护证书不会保存与其关联的密码。 仅需在导入操作期间使用一次密码。 尽管这是一个设计上的概念，但你始终可以获取证书作为机密，并从 Base64 转换为 PFX，通过[Azure PowerShell](https://social.technet.microsoft.com/wiki/contents/articles/37431.exporting-azure-app-service-certificates.aspx)添加以前的密码。

### <a name="how-can-i-resolve-bad-parameter-error-what-are-the-supported-certificate-formats-for-importing-in-key-vault"></a>如何解决 "错误的参数错误"？ 在 Key Vault 中，支持导入哪些格式的证书？

导入证书时，需要确保该密钥包含在文件本身中。 如果让私钥单独采用另一格式，则需将私钥与证书组合在一起。 某些证书颁发机构提供不同格式的证书，因此在导入证书之前，请确保它们采用的是 pem 或 .pfx 格式，并且所使用的密钥为 RSA 或 ECC。 请参阅，了解[证书要求](https://docs.microsoft.com/azure/key-vault/certificates/certificate-scenarios#formats-of-import-we-support)和[证书密钥要求](https://docs.microsoft.com/azure/key-vault/keys/about-keys#cryptographic-protection)。

### <a name="error-when-importing-certificate-via-portal-something-went-wrong-how-can-i-investigate-further"></a>通过门户导入证书时出现错误“出现错误”。 我如何进一步调查？
    
若要查看更多描述性错误，请通过[Azure CLI](https://docs.microsoft.com/cli/azure/keyvault/certificate?view=azure-cli-latest#az-keyvault-certificate-import)或[PowerShell](https://docs.microsoft.com/powershell/module/azurerm.keyvault/import-azurekeyvaultcertificate?view=azurermps-6.13.0)导入证书文件。

### <a name="how-can-i-resolve-error-type-access-denied-or-user-is-unauthorized-to-import-certificate"></a>如何解决 "错误类型：访问被拒绝" 或 "用户无权导入证书"？
    
此操作需要证书/导入权限。 导航到 Key Vault 所在位置，你将需要在“访问策略”下授予用户适当的权限。 导航到 Key Vault> 访问策略 > "添加访问策略" > "选择证书权限" （或所需权限） > 主体 > 搜索，然后添加用户的电子邮件。 [阅读有关证书相关访问策略的详细信息](https://docs.microsoft.com/azure/key-vault/certificates/about-certificates#certificate-access-control)


### <a name="how-can-i-resolve-error-type-conflict-when-creating-a-certificate"></a>如何解决 "错误类型：创建证书时出现冲突"？
    
证书名称应唯一。 具有相同名称的证书可能处于软删除状态，并且在 Azure 密钥保管库中使用[证书的组合](https://docs.microsoft.com/azure/key-vault/certificates/about-certificates#composition-of-a-certificate)时，如果 Key Vault 中存在与你要为证书指定的名称相同的其他密钥或机密，则该证书将会失败，并且你将需要删除该密钥或机密，或者对证书使用不同的名称。 [查看已删除的证书](https://docs.microsoft.com/rest/api/keyvault/getdeletedcertificate/getdeletedcertificate)

### <a name="why-am-i-getting-the-error-type-char-length-is-too-long"></a>为什么收到 "错误类型：字符长度太长"？
    
* 证书使用者名称长度的字符限制为200个字符
* 证书密码长度的字符限制为200个字符

### <a name="can-i-import-an-expired-certificate-in-azure-key-vault"></a>能否在 Azure 密钥保管库中导入过期的证书？
    
不会，过期的 PFX 证书不会导入到 Azure Key Vault。

### <a name="how-can-i-convert-my-certificate-to-proper-format"></a>如何将证书转换为正确的格式？

你可以要求证书颁发机构以所需的格式提供证书，此外还有第三方工具可以帮助你转换为正确的格式，不过，Microsoft 将无法进一步建议如何以所需格式获取证书。

### <a name="can-i-import-certificates-from-non-partner-cas"></a>能否从非合作伙伴 Ca 导入证书？
是的，你可以从任何 CA 导入证书，但 key vault 将不能自动续订这些证书。 你将能够设置电子邮件通知，以获得有关证书过期的通知。

### <a name="if-i-import-a-certificate-from-a-partner-ca-will-the-auto-renew-feature-still-work"></a>如果导入合作伙伴 CA 颁发的证书，自动续订功能是否仍然有效？
是的，需要确保在证书的颁发策略中指定 autorotation。 而且，所做的更改将反映在下一个周期或证书版本之前。


## <a name="next-steps"></a>后续步骤

- [Azure Key Vault 证书](/azure/key-vault/certificates/about-certificates)
