---
title: 证书创建方法
description: 在 Key Vault 中创建证书的方法。
services: key-vault
documentationcenter: ''
author: lleonard-msft
manager: mbaldwin
tags: azure-resource-manager
ms.assetid: e17b4c9b-4ff3-472f-8c9d-d130eb443968
ms.service: key-vault
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/09/2018
ms.author: alleonar
ms.openlocfilehash: 7b71c6a8daa97300ecf3b37ec6ab47207fece98e
ms.sourcegitcommit: d28bba5fd49049ec7492e88f2519d7f42184e3a8
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/11/2018
ms.locfileid: "34058392"
---
# <a name="certificate-creation-methods"></a>证书创建方法

 可以在密钥保管库中创建或导入 Key Vault (KV) 证书。 创建 KV 证书时，私钥是在密钥保管库中创建的，不公开给证书所有者。 下面是在 Key Vault 中创建证书的方法：  

-   **创建自签名证书：** 这样会创建公钥-私钥对并将其与证书相关联。 证书将通过其自身的密钥签名。  

-    **手动创建新证书：** 这样会创建公钥-私钥对并生成 X.509 证书签名请求。 签名请求可以由注册机构或证书颁发机构签署。 签名的 x509 证书可以与挂起的密钥对合并，以便完成 Key Vault 中的 KV 证书。 虽然此方法需要更多步骤，但其安全性更高，因为私钥是在 Key Vault 中创建的，其范围局限于 Key Vault。 下图对此进行了说明。  

![使用自己的证书颁发机构创建证书](media/certificate-authority-1.png)  

以下说明对应于上图中绿色字母代表的步骤。

1. 在上图中，应用程序在创建证书时，是在内部以在密钥保管库中创建密钥开始的。
2. Key Vault 将证书签名请求 (CSR) 返回给应用程序。
3. 应用程序将 CSR 传递给所选 CA。
4. 所选 CA 以 X509 证书进行响应。
5. 应用程序在合并 CA 提供的 X509 证书后，就完成了新证书创建过程。

-   **使用已知的颁发者提供者创建证书：** 此方法要求你执行一项一次性任务，即创建一个颁发者对象。 在密钥保管库中创建颁发者对象以后，即可在 KV 证书的策略中引用其名称。 请求创建此类 KV 证书时，会在保管库中创建一个密钥对，并使用所引用的颁发者对象中的信息与颁发者提供者服务通信，以便获取 x509 证书。 从颁发者服务中检索 x509 证书并将其与密钥对合并以后，就完成了 KV 证书创建过程。  

![使用与 Key Vault 配合使用的证书颁发机构创建证书](media/certificate-authority-2.png)  

以下说明对应于上图中绿色字母代表的步骤。

1. 在上图中，应用程序在创建证书时，是在内部以在密钥保管库中创建密钥开始的。
2. Key Vault 向 CA 发送 SSL 证书请求。
3. 应用程序会在循环和等待过程中轮询 Key Vault 至证书完成。 当 Key Vault 收到 CA 的 x509 证书响应时，证书创建完成。
4. CA 使用 X509 SSL 证书对 Key Vault 的 SSL 证书请求进行响应。
5. 与 CA 的 X509 证书合并以后，新证书的创建过程即告完成。

## <a name="asynchronous-process"></a>异步过程
KV 证书的创建是一个异步过程。 此操作会创建 KV 证书请求并返回 Http 状态代码“202 (已接受)”。 可以通过轮询此操作创建的挂起对象来跟踪请求的状态。 挂起对象的完整 URI 在 LOCATION 标头中返回。  

当创建 KV 证书的请求完成后，挂起对象的状态将从“正在进行”变为“已完成”，新版 KV 证书完成创建。 该版本将成为当前版本。  

## <a name="first-creation"></a>第一次创建
 第一次创建 KV 证书时，也会创建可寻址的密钥和机密，所用名称与证书的名称相同。 如果该名称已被使用，则操作会失败，并返回 Http 状态代码“409 (冲突)”。
可寻址密钥和机密从 KV 证书属性获取其属性。 以这种方式创建的可寻址密钥和机密将会标记为托管密钥和机密，其生存期由 Key Vault 管理。 托管密钥和机密为只读。 注意：如果 KV 证书已过期或已禁用，则相应的密钥和机密将变得不可操作。  

 如果这是创建 KV 证书所需的首次操作，则需使用策略。  也可为策略提供连续的创建操作，以便替换策略资源。 如果未提供策略，则会使用服务的策略资源来创建下一版本的 KV 证书。 请注意，当创建下一版本的请求正在处理过程中时，当前的 KV 证书以及相应的可寻址密钥和机密会保持原封不动。  

## <a name="self-issued-certificate"></a>自颁证书
 若要创建自颁证书，请在证书策略中将颁发者名称设置为“Self”，如证书策略中的以下代码片段所示。  

```  
"issuer": {  
       "name": "Self"  
    }  

```  

 如果颁发者名称未指定，系统会将颁发者名称设置为“Unknown”。 当颁发者为“Unknown”时，证书所有者必须从其选择的颁发者中手动获取 x509 证书，然后将公用 x509 证书与密钥保管库证书挂起对象合并，这样才能完成证书创建过程。

```  
"issuer": {  
       "name": "Unknown"  
    }  

```  

## <a name="partnered-ca-providers"></a>配合使用的 CA 提供程序
可以手动完成证书创建过程，也可以使用“Self”颁发者来这样做。 Key Vault 也可以与某些颁发者提供者配合使用，从而简化证书创建过程。 可以订购以下类型的证书，将密钥保管库与这些合作伙伴颁发者提供者配合使用。  

|提供程序|证书类型|  
|--------------|----------------------|  
|DigiCert|Key Vault 提供 DigiCert 的 OV 或 EV SSL 证书|
|GlobalCert|Key Vault 提供 GlobalSign 的 OV 或 EV SSL 证书|

 有关详细信息（包括这些颁发者提供者的地理可用性），请参阅[证书颁发者](/rest/api/keyvault/certificate-issuers.md)。

请注意，向颁发者提供者下单时，该提供者可能会接受 x509 证书扩展和证书有效期，也可能会将其替代，具体取决于证书类型。  

 授权：需要证书/创建权限。

 ## <a name="see-also"></a>另请参阅
 - [关于密钥、机密和证书](about-keys-secrets-and-certificates.md)
 - [监视和管理证书创建](create-certificate-scenarios.md)
