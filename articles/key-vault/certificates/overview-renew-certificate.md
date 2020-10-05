---
title: 关于 Azure Key Vault 证书续订
description: 本文介绍如何续订 Azure Key Vault 证书。
services: key-vault
author: msmbaldwin
manager: rkarlin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: certificates
ms.topic: overview
ms.date: 07/20/2020
ms.author: sebansal
ms.openlocfilehash: 3809fa9e1ce17a5a0c3cf333ac20ef543db4b5a7
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/05/2020
ms.locfileid: "88588797"
---
# <a name="renew-your-azure-key-vault-certificates"></a>续订 Azure Key Vault 证书

通过 Azure Key Vault，可轻松地为网络预配、管理和部署数字证书，并支持应用程序的安全通信。 若要了解证书的详细信息，请参阅[关于 Azure Key Vault 证书](https://docs.microsoft.com/azure/key-vault/certificates/about-certificates)。

通过使用生存期较短的证书或增加证书轮换的频率，可以帮助防止未经授权的用户访问应用程序。

本文介绍如何续订 Azure Key Vault 证书。

## <a name="get-notified-about-certificate-expirations"></a>获取有关证书过期的通知
若要在证书即将过期时收到通知，请执行以下操作：

首先，使用 PowerShell cmdlet [Add-AzureKeyVaultCertificateContact](https://docs.microsoft.com/powershell/module/azurerm.keyvault/add-azurekeyvaultcertificatecontact?view=azurermps-6.13.0) 将证书联系人添加到密钥保管库。

其次，配置希望收到证书过期通知的时间。 若要配置证书的生命周期属性，请参阅[在 Key Vault 中配置证书自动轮换](https://docs.microsoft.com/azure/key-vault/certificates/tutorial-rotate-certificates#update-lifecycle-attributes-of-a-stored-certificate)。

在 Key Vault 中，有三种类别的证书：
-   通过集成证书颁发机构 (CA)（如 DigiCert 或 GlobalSign）创建的证书
-   通过非集成 CA 创建的证书
-   自签名证书

## <a name="renew-an-integrated-ca-certificate"></a>续订集成 CA 证书 
Azure Key Vault 处理由受信任的 Microsoft 证书颁发机构 DigiCert 和 GlobalSign 颁发的证书的端到端维护。 了解如何[将受信任的 CA 与 Key Vault 集成](https://docs.microsoft.com/azure/key-vault/certificates/how-to-integrate-certificate-authority)。

## <a name="renew-a-nonintegrated-ca-certificate"></a>续订非集成 CA 证书 
通过使用 Azure Key Vault，可导入来自任何 CA 的证书，这一优点使你能够与多个 Azure 资源集成并简化部署。 如果你担心无法跟踪证书的到期日期，或者更糟的是，你发现某个证书已过期，那么你的密钥保管库可帮助你保持最新状态。 对于非集成 CA 证书，密钥保管库允许你设置即将过期的电子邮件通知。 此类通知也可为多个用户设置。

> [!IMPORTANT]
> 证书是受版本控制的对象。 如果当前版本即将过期，则需要创建新版本。 从概念上讲，每个新版本都是一个新证书，它由一个密钥和将该密钥与标识联系起来的 Blob 组成。 使用非合作伙伴 CA 时，密钥保管库将生成一个键/值对，并返回证书签名请求 (CSR)。

若要续订非集成 CA 证书，请执行以下操作：

1. 登录到 Azure 门户，然后打开要续订的证书。
1. 在证书窗格中，选择“新版本”。
1. 选择“证书操作”。
1. 选择“下载 CSR”，将 CSR 文件下载到本地驱动器。
1. 将 CSR 发送到所选的 CA 以对请求进行签名。
1. 返回已签名的请求，并在相同证书操作窗格中选择“合并 CSR”。

> [!NOTE]
> 将已签名的 CSR 与你创建的相同 CSR 请求合并，这点很重要。 否则，密钥将不匹配。

有关创建新 CSR 的详细信息，请参阅[在 Key Vault 中创建和合并 CSR]( https://docs.microsoft.com/azure/key-vault/certificates/create-certificate-signing-request#azure-portal)。

## <a name="renew-a-self-signed-certificate"></a>续订自签名证书

Azure Key Vault 还处理自签名证书的自动续订。 若要详细了解如何更改颁发策略和更新证书的生命周期属性，请参阅[在 Key Vault 中配置证书自动轮换](https://docs.microsoft.com/azure/key-vault/certificates/tutorial-rotate-certificates#update-lifecycle-attributes-of-a-stored-certificate)。

## <a name="troubleshoot"></a>疑难解答
如果颁发的证书在 Azure 门户中处于“已禁用”状态，转到“证书操作”查看该证书的错误消息。

## <a name="frequently-asked-questions"></a>常见问题

如何测试证书的自动轮换功能？

创建一个有效期为 1 个月的证书，然后将其轮换的生存期操作设置为 1% 。 此设置将每 7.2 小时轮换一次证书。
  
在自动续订证书后是否复制标记？

是的，在自动续订后复制标记。

## <a name="next-steps"></a>后续步骤
*   [将 Key Vault 与 DigiCert 证书颁发机构集成](how-to-integrate-certificate-authority.md)
*   [教程：配置 Key Vault 中的证书自动轮换](tutorial-rotate-certificates.md)
