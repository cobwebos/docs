---
title: 常见问题-Azure Key Vault 证书导入
description: 获取有关导入 Azure Key Vault 证书的常见问题的解答。
services: key-vault
author: msmbaldwin
manager: rkarlin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: certificates
ms.topic: how-to
ms.date: 07/20/2020
ms.author: sebansal
ms.openlocfilehash: b7a2c78238de58ee8851462aa7193121b35f72a9
ms.sourcegitcommit: 02ca0f340a44b7e18acca1351c8e81f3cca4a370
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/19/2020
ms.locfileid: "88588815"
---
# <a name="importing-azure-key-vault-certificates-faq"></a>导入 Azure Key Vault 证书常见问题

本文解答了有关导入 Azure Key Vault 证书的常见问题。

## <a name="frequently-asked-questions"></a>常见问题

### <a name="how-can-i-import-a-certificate-in-azure-key-vault"></a>如何在 Azure Key Vault 中导入证书？

对于证书导入操作，Azure Key Vault 接受两种证书文件格式： PEM 和 PFX。 尽管只有公共部分的 PEM 文件，但 Key Vault 只需要和仅接受带有私钥的 PEM 或 PFX 文件。 有关详细信息，请参阅 [将证书导入 Key Vault](https://docs.microsoft.com/azure/key-vault/certificates/tutorial-import-certificate#import-a-certificate-to-key-vault)。

### <a name="after-i-import-a-password-protected-certificate-to-key-vault-and-then-download-it-why-cant-i-see-the-password-thats-associated-with-it"></a>在将受密码保护的证书导入 Key Vault 然后下载它后，为什么无法看到与其关联的密码？
    
在 Key Vault 中导入并保护证书后，不会保存其关联的密码。 在导入操作期间，只需输入密码一次。 这是设计使然，但你始终可以将证书作为机密，并通过 [Azure PowerShell](https://social.technet.microsoft.com/wiki/contents/articles/37431.exporting-azure-app-service-certificates.aspx)添加密码将其从 Base64 转换为 PFX。

### <a name="how-can-i-resolve-a-bad-parameter-error-what-are-the-supported-certificate-formats-for-importing-to-key-vault"></a>如何解决 "错误的参数" 错误？ Key Vault 导入的支持的证书格式是什么？

导入证书时，需要确保文件中包含该密钥。 如果你有以不同格式单独存储的私钥，则需要将密钥与证书组合在一起。 某些证书颁发机构 (Ca) 提供其他格式的证书。 因此，在导入证书之前，请确保该证书为 PEM 或 PFX 文件格式，并且该密钥使用 Rivest – Rivest-shamir-adleman – Rivest-shamir-adleman (RSA) 或椭圆曲线加密 (ECC) 加密。 

有关详细信息，请参阅 [证书要求](https://docs.microsoft.com/azure/key-vault/certificates/certificate-scenarios#formats-of-import-we-support) 和 [证书密钥要求](https://docs.microsoft.com/azure/key-vault/keys/about-keys#cryptographic-protection)。

###  <a name="can-i-import-a-certificate-by-using-an-arm-template"></a>能否使用 ARM 模板导入证书？

不可以，不能使用 Azure 资源管理器 (ARM) 模板来执行证书操作。 建议的解决方法是使用 Azure API、Azure CLI 或 PowerShell 中的证书导入方法。 如果有现成的证书，可以将其导入为机密。

### <a name="when-i-import-a-certificate-via-the-azure-portal-i-get-a-something-went-wrong-error-how-can-i-investigate-further"></a>通过 Azure 门户导入证书时，出现 "出现错误" 错误。 我如何进一步调查？
    
若要查看更具描述性的错误，请使用 [Azure CLI](https://docs.microsoft.com/cli/azure/keyvault/certificate?view=azure-cli-latest#az-keyvault-certificate-import) 或 [PowerShell](https://docs.microsoft.com/powershell/module/azurerm.keyvault/import-azurekeyvaultcertificate?view=azurermps-6.13.0)导入证书文件。

### <a name="how-can-i-resolve-error-type-access-denied-or-user-is-unauthorized-to-import-certificate"></a>如何解决 "错误类型：访问被拒绝或用户未经授权导入证书"？
    
导入操作要求您向用户授予在访问策略下导入证书的权限。 为此，请前往密钥保管库，选择 "**访问策略**" "  >  **添加访问策略**  >  " "**选择证书权限**  >  **主体**"，搜索用户，然后添加用户的电子邮件地址。 

有关证书相关访问策略的详细信息，请参阅 [关于 Azure Key Vault 证书](https://docs.microsoft.com/azure/key-vault/certificates/about-certificates#certificate-access-control)。


### <a name="how-can-i-resolve-error-type-conflict-when-creating-a-certificate"></a>如何解决 "错误类型：创建证书时的冲突"？
    
每个证书名称必须是唯一的。 与另一个证书同名的证书可能处于软删除状态。 此外，根据 Azure Key Vault 中 [证书的撰写](https://docs.microsoft.com/azure/key-vault/certificates/about-certificates#composition-of-a-certificate) ，如果密钥保管库中有另一个密钥或机密与你要为证书指定的名称相同，则证书创建将会失败，并且你需要删除此密钥或机密，或者对证书使用不同的名称。 

有关详细信息，请参阅 [获取删除的证书操作](https://docs.microsoft.com/rest/api/keyvault/getdeletedcertificate/getdeletedcertificate)。

### <a name="why-am-i-getting-error-type-char-length-is-too-long"></a>为什么收到 "错误类型：字符长度太长"？
此错误可能是由以下两个原因之一引起的：    
* 证书使用者名称限制为200个字符。
* 证书密码限制为200个字符。

### <a name="can-i-import-an-expired-certificate-to-azure-key-vault"></a>是否可以将过期的证书导入 Azure Key Vault？
    
不能，过期的 PFX 证书无法导入到 Key Vault。

### <a name="how-can-i-convert-my-certificate-to-the-proper-format"></a>如何将证书转换为正确的格式？

可以要求 CA 提供所需格式的证书。 还有第三方工具可以帮助你将证书转换为正确的格式。

### <a name="can-i-import-certificates-from-non-partner-cas"></a>能否从非合作伙伴 Ca 导入证书？
是的，你可以从任何 CA 导入证书，但你的密钥保管库将无法自动续订这些证书。 你可以设置提醒以获得有关证书过期的通知。

### <a name="if-i-import-a-certificate-from-a-partner-ca-will-the-autorenewal-feature-still-work"></a>如果导入合作伙伴 CA 颁发的证书，自动续订功能是否仍然有效？
是的。 上传证书后，请确保在证书的颁发策略中指定 autorotation。 设置将一直有效，直到下一个周期或证书版本发布。

### <a name="why-cant-i-see-the-app-service-certificate-that-i-imported-to-key-vault"></a>为什么看不到要 Key Vault 导入的应用服务证书？ 
如果已成功导入证书，你应该可以通过转到 " **机密** " 窗格来确认证书。


## <a name="next-steps"></a>后续步骤

- [Azure Key Vault 证书](/azure/key-vault/certificates/about-certificates)
