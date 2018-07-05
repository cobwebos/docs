---
title: Key Vault 证书入门
description: 以下方案概述了 Key Vault 的证书管理服务的多种主要使用方式，包括在密钥保管库中创建第一个证书所需的其他步骤。
services: key-vault
documentationcenter: ''
author: lleonard-msft
manager: mbaldwin
tags: azure-resource-manager
ms.assetid: a788b958-3acb-4bb6-9c94-4776852aeea1
ms.service: key-vault
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/09/2018
ms.author: alleonar
ms.openlocfilehash: f1a1a2fa083dd1bf02132e08981d736a17a2c58f
ms.sourcegitcommit: 5a7f13ac706264a45538f6baeb8cf8f30c662f8f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/29/2018
ms.locfileid: "37109477"
---
# <a name="get-started-with-key-vault-certificates"></a>Key Vault 证书入门
以下方案概述了 Key Vault 的证书管理服务的多种主要使用方式，包括在密钥保管库中创建第一个证书所需的其他步骤。

下面是概述的内容：
- 创建第一个 Key Vault 证书
- 使用与 Key Vault 配合使用的证书颁发机构创建证书
- 使用不与 Key Vault 配合使用的证书颁发机构创建证书
- 导入证书

## <a name="certificates-are-complex-objects"></a>证书是复杂的对象
证书由三个相互关联的资源组成，以 Key Vault 证书、证书元数据、密钥和机密的形式链接到一起。


![证书是复杂的](media/azure-key-vault.png)


## <a name="creating-your-first-key-vault-certificate"></a>创建第一个 Key Vault 证书  
 在 Key Vault (KV) 中创建证书之前，必须成功完成先决条件步骤 1 和步骤 2，并且必须存在适用于该用户/组织的密钥保管库。  

**步骤 1** - 证书颁发机构 (CA) 提供者  
-   对于任何给定公司（例如 Contoso）来说，以 IT 管理员、PKI 管理员或任何可以使用 CA 来管理帐户的人员的身份加入 是使用 Key Vault 证书的先决条件。  
    以下 CA 是目前可以与 Key Vault 配合使用的提供者：  
    -   DigiCert - Key Vault 提供 DigiCert 的 OV SSL 证书。  
    -   GlobalSign - Key Vault 提供 GlobalSign 的 OV SSL 证书  
    -   WoSign - Key Vault 根据客户在 WoSign 门户的 WoSign 帐户中配置的设置提供 WoSign 的 OV SSL 或 EV SSL 证书。  

**步骤 2** - CA 提供者的帐户管理员创建可供 Key Vault 使用的凭据，以便通过 Key Vault 注册、续订和使用 SSL 证书。

**步骤 3** - Contoso 管理员以及拥有证书（取决于 CA）的 Contoso 员工（Key Vault 用户）可以从管理员处获取证书，也可以直接从 CA 的帐户获取。  

-   开始通过创建[证书颁发者](https://docs.microsoft.com/rest/api/keyvault/certificate-issuers)资源，对密钥保管库执行添加凭据操作。 
    -   例如： MyDigiCertIssuer  
        -   提供程序  
        -   凭据 - CA 帐户凭据。 每个 CA 都有其自身的特定数据。  

     若要详细了解如何通过 CA 提供者来创建帐户，请参阅 [Key Vault 博客](http://aka.ms/kvcertsblog)上的相关文章。  

**步骤 3.1** - 设置用于接收通知的[证书联系人](https://docs.microsoft.com/rest/api/keyvault/certificate-contacts)。 这是 Key Vault 用户的联系人。 Key Vault 不强制执行此步骤。  

注意 - 上述过程（一直到步骤 3.1）是一次性操作。  

## <a name="creating-a-certificate-with-a-ca-partnered-with-key-vault"></a>使用与 Key Vault 配合使用的 CA 创建证书

![使用与 Key Vault 配合使用的证书颁发机构创建证书](media/certificate-authority-2.png)

**步骤 4** - 以下说明对应于上图中绿色数字代表的步骤。  
  (1) - 在上图中，应用程序在创建证书时，是在内部以在密钥保管库中创建密钥开始的。  
  (2) - Key Vault 向 CA 发送 SSL 证书请求。  
  (3) - 应用程序会在循环和等待过程中轮询 Key Vault 至证书完成。 当 Key Vault 收到 CA 的 x509 证书响应时，证书创建完成。  
  (4) - CA 使用 X509 SSL 证书对 Key Vault 的 SSL 证书请求进行响应。  
  (5) - 与 CA 的 X509 证书合并以后，新证书的创建过程即告完成。  

  Key Vault 用户 - 通过指定策略来创建证书

  -   根据需要进行重复  
  -   策略约束  
      -   X509 属性  
      -   密钥属性  
      -   提供者引用 - > 例如 MyDigiCertIssure  
      -   续订信息 - > 例如 在到期之前 90 天  

  - 证书创建过程通常为异步过程，涉及轮询密钥保管库中“创建证书”操作的状态。  
[获取证书操作](https://docs.microsoft.com/en-us/rest/api/keyvault/getcertificateoperation)  
      -   状态：“已完成”、“失败并显示错误消息”或“已取消”  
      -   由于创建操作延迟，因此可能会启动取消操作。 取消操作可能生效，也可能不生效。  

## <a name="import-a-certificate"></a>导入证书  
 也可将证书导入 Key Vault - PFX 或 PEM。  

 有关 PEM 格式的详细信息，请参阅[关于密钥、机密和证书](about-keys-secrets-and-certificates.md)的证书部分。  

 导入证书 - 需要 PEM 或 PFX 存在于磁盘上，并且要有私钥。 
-   必须指定：保管库名称和证书名称（策略为可选）

-   PEM/PFX 文件包含的属性可供 KV 分析和用来填充证书策略。 如果已指定证书策略，KV 会尝试匹配 PFX/PEM 文件中的数据。  

-   导入确定以后，后续操作会使用新策略（新版本）。  

-   如果没有进一步的操作，Key Vault 首先要做的是发送过期通知。 

-   另外，用户可以编辑策略。策略在导入时生效，但其包含的默认设置在导入时并未指定任何信息。 例如： 无颁发者信息  

## <a name="creating-a-certificate-with-a-ca-not-partnered-with-key-vault"></a>使用不与 Key Vault 配合使用的 CA 创建证书  
 此方法允许使用除 Key Vault 的合作提供者之外的其他 CA，也就是说，组织可以使用自选的 CA。  

![使用自己的证书颁发机构创建证书](media/certificate-authority-1.png)  

 以下步骤说明对应于上图中绿色字母代表的步骤。  

  (1) - 在上图中，应用程序在创建证书时，是在内部以在密钥保管库中创建密钥开始的。  

  (2) - Key Vault 将证书签名请求 (CSR) 返回给应用程序。  

  (3) - 应用程序将 CSR 传递给所选 CA。  

  (4) - 所选 CA 以 X509 证书进行响应。  

  (5) - 应用程序在合并 CA 提供的 X509 证书后，就完成了新证书创建过程。

## <a name="see-also"></a>另请参阅
- [证书操作](/rest/api/keyvault/certificate-operations)
- [关于键、密钥和证书](about-keys-secrets-and-certificates.md)
