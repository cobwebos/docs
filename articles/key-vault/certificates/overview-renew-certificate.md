---
title: 关于 Azure Key Vault 证书续订
description: 关于 Azure Key Vault 证书续订
services: key-vault
author: msmbaldwin
manager: rkarlin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: certificates
ms.topic: conceptual
ms.date: 07/20/2020
ms.author: sebansal
ms.openlocfilehash: c6999b67a5c0a0f4ca7cb943ae8de3afd8b6a11e
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/23/2020
ms.locfileid: "87095997"
---
# <a name="about-azure-key-vault-certificate-renewal"></a>关于 Azure Key Vault 证书续订

Azure Key Vault 使你能轻松地为网络预配、管理和部署数字证书，并支持应用程序的安全通信。 有关证书的更多常规信息，请参阅[Azure Key Vault 证书](https://docs.microsoft.com/azure/key-vault/certificates/about-certificates)

具有短暂的生存期证书或提高证书轮换的频率会限制攻击者的范围。

Key vault 中创建了三类证书。 本指南将帮助你了解如何才能实现证书续订。
-   通过集成 CA （DigiCert 或 GlobalSign）创建的证书
-   用非集成 CA 创建的证书
-   自签名证书

## <a name="renewal-of-integrated-ca-certificate"></a>续订集成 CA 证书 
好消息！ Azure 密钥保管库负责对由 Microsoft 信任的 Ca （即 DigiCert 和 GlobalSign）颁发的证书进行端到端维护。 了解如何将[受信任的 CA 与密钥保管库集成](https://docs.microsoft.com/azure/key-vault/certificates/how-to-integrate-certificate-authority)。

## <a name="renewal-of-non-integrated-ca-certificate"></a>续订非集成 CA 证书 
Azure 密钥保管库为其用户提供从任何 CA 导入证书的好处，使其用户能够与多个 Azure 资源集成并简化部署。 如果您担心您的证书已过期，或者发现您的证书已过期，然后 Key Vault 可以帮助你保持最新状态。 对于非集成 CA 证书，密钥保管库允许其用户设置近过期电子邮件通知。 还可以为多个用户设置这些通知。

现在，若要续订证书，请务必了解 Azure Key Vault 证书是版本控制的对象。 如果当前版本即将过期，则需要创建新版本。 从概念上讲，每个新版本都是由密钥和 blob 组成的新证书，该证书将该密钥与标识进行联系。 使用非合作 CA 时，密钥保管库将生成键值对并返回 CSR。

**在 Azure 门户中要遵循的步骤：-**
1.  打开要续订的证书。
2.  选择 "证书" 屏幕上的 " **+ 新建版本**" 按钮。
3.  选择**证书操作**
4.  选择 "**下载 CSR**"。 这会在本地驱动器上下载一个 csr 文件。
5.  将 CSR 引入你选择的 CA 来签署请求
6.  返回签名的请求，并在相同证书操作屏幕上选择**合并 CSR** 。

> [!NOTE]
> 必须将签名的 CSR 与创建的同一 CSR 请求合并，否则该键将不匹配。

步骤类似于创建新证书，详细信息请参阅[此]( https://docs.microsoft.com/azure/key-vault/certificates/create-certificate-signing-request#azure-portal)文。

## <a name="renewal-of-self-signed-certificate"></a>续订自签名证书

好消息！ Azure 密钥保管库还将负责自动续订其用户的自签名证书。 若要详细了解如何更改颁发策略和更新证书的生存期操作属性，请[在此处](https://docs.microsoft.com/azure/key-vault/certificates/tutorial-rotate-certificates#update-lifecycle-attributes-of-a-stored-certificate)阅读详细信息。

### <a name="troubleshoot"></a>疑难解答
如果颁发的证书处于 Azure 门户中的 "已禁用" 状态，请继续查看证书操作，以查看该证书的错误消息。

### <a name="see-also"></a>另请参阅
*   [将 Key Vault 与 DigiCert 证书颁发机构集成](how-to-integrate-certificate-authority.md)
*   [教程：配置 Key Vault 中的证书自动轮换](tutorial-rotate-certificates.md)