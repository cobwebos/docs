---
title: 使用证书保护 B2B 消息 - Azure 逻辑应用 | Microsoft Docs
description: 使用 Enterprise Integration Pack 在 Azure 逻辑应用中添加证书来保护 B2B 消息
services: logic-apps
ms.service: logic-apps
author: divyaswarnkar
ms.author: divswa
ms.reviewer: estfan, LADocs
manager: jeconnoc
ms.assetid: 4cbffd85-fe8d-4dde-aa5b-24108a7caa7d
ms.suite: integration
ms.topic: article
ms.date: 08/17/2018
ms.openlocfilehash: 5ae69d365a183f7d2a219d853241e73c1e27212b
ms.sourcegitcommit: f057c10ae4f26a768e97f2cb3f3faca9ed23ff1b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/17/2018
ms.locfileid: "42144178"
---
# <a name="secure-b2b-messages-with-certificates"></a>使用证书保护 B2B 消息

当需要使 B2B 通信保持机密时，可以通过向集成帐户添加证书来保护企业集成应用（具体而言是逻辑应用）的 B2B 通信。 证书是数字文档，用于在电子通信中验证参与者身份并通过以下方式保护通信安全：

* 对消息内容进行加密。
* 对消息进行数字签名。 

在企业集成应用中，可以使用以下证书：

* [公用证书](https://en.wikipedia.org/wiki/Public_key_certificate)，必须从公用 Internet [证书颁发机构 (CA)](https://en.wikipedia.org/wiki/Certificate_authority) 购买，不需要任何密钥。 

* 私有证书或[*自签名证书*](https://en.wikipedia.org/wiki/Self-signed_certificate)，由你自己创建并颁发，但还需要私钥。 

## <a name="upload-a-public-certificate"></a>上传公用证书

要在具有 B2B 功能的逻辑应用中使用“公用证书”，必须首先将证书上传到集成帐户中。 在你创建的[协议](logic-apps-enterprise-integration-agreements.md)中定义属性后，可以使用证书来帮助你保护 B2B 消息。

1. 登录到 [Azure 门户](https://portal.azure.com)。 在 Azure 主菜单中，选择“所有资源”。 在搜索框中，输入你的集成帐户名称，然后选择所需的集成帐户。

   ![查找并选择你的集成帐户](media/logic-apps-enterprise-integration-certificates/select-integration-account.png)  

2. 在“组件”下，选择“证书”磁贴。

   ![选择“证书”](media/logic-apps-enterprise-integration-certificates/add-certificates.png)

3. 在“证书”下，选择“添加”。 在“添加证书”下，提供证书的这些详细信息。 完成后，选择“确定”。

   | 属性 | 值 | 说明 | 
   |----------|-------|-------------|
   | **名称** | <*证书名称*> | 你的证书的名称，在本例中为“publicCert” | 
   | **证书类型** | 公用 | 你的证书的类型 |
   | **证书** | <*证书文件名*> | 若要查找并选择要上传的证书文件，请选择“证书”框旁边的文件夹图标。 |
   ||||

   ![选择“添加”，提供证书详细信息](media/logic-apps-enterprise-integration-certificates/public-certificate-details.png)

   在 Azure 验证你的选择后，Azure 会上传你的证书。

   ![Azure 显示新证书](media/logic-apps-enterprise-integration-certificates/new-public-certificate.png) 

## <a name="upload-a-private-certificate"></a>上传私有证书

要在具有 B2B 功能的逻辑应用中使用“私有证书”，必须首先将证书上传到集成帐户中。 还需要有一个私钥，需要首先将其添加到 [Azure Key Vault](../key-vault/key-vault-get-started.md)。 

在你创建的[协议](logic-apps-enterprise-integration-agreements.md)中定义属性后，可以使用证书来帮助你保护 B2B 消息。

> [!NOTE]
> 对于私有证书，请确保添加显示在 [AS2 协议的](logic-apps-enterprise-integration-as2.md)**发送和接收**设置中用于对消息进行签名和加密的对应公用证书。

1. [将私钥添加到 Azure Key Vault](../key-vault/key-vault-get-started.md#add) 并提供**密钥名称**。
   
2. 授权 Azure 逻辑应用对 Azure Key Vault 执行操作。 若要向逻辑应用服务主体授予访问权限，请使用 PowerShell 命令 [Set-AzureRmKeyVaultAccessPolicy](https://docs.microsoft.com/powershell/module/azurerm.keyvault/set-azurermkeyvaultaccesspolicy)，例如：

   `Set-AzureRmKeyVaultAccessPolicy -VaultName 'TestcertKeyVault' -ServicePrincipalName 
   '7cd684f4-8a78-49b0-91ec-6a35d38739ba' -PermissionsToKeys decrypt, sign, get, list`
 
3. 登录到 [Azure 门户](https://portal.azure.com)。 在 Azure 主菜单中，选择“所有资源”。 在搜索框中，输入你的集成帐户名称，然后选择所需的集成帐户。

   ![查找集成帐户](media/logic-apps-enterprise-integration-certificates/select-integration-account.png) 

4. 在“组件”下，选择“证书”磁贴。  

   ![选择“证书”磁贴](media/logic-apps-enterprise-integration-certificates/add-certificates.png)

5. 在“证书”下，选择“添加”。 在“添加证书”下，提供证书的这些详细信息。 完成后，选择“确定”。

   | 属性 | 值 | 说明 | 
   |----------|-------|-------------|
   | **Name** | <*证书名称*> | 你的证书的名称，在本例中为“privateCert” | 
   | **证书类型** | 专用 | 你的证书的类型 |
   | **证书** | <*证书文件名*> | 若要查找并选择要上传的证书文件，请选择“证书”框旁边的文件夹图标。 | 
   | **资源组** | <*集成帐户资源组*> | 你的集成帐户的资源组，在本例中为“MyResourceGroup” | 
   | **Key Vault** | <*密钥保管库名称*> | 你的 Azure 密钥保管库的名称 |
   | **密钥名称** | <*密钥名称*> | 你的密钥的名称 |
   ||||

   ![选择“添加”，提供证书详细信息](media/logic-apps-enterprise-integration-certificates/private-certificate-details.png)

   在 Azure 验证你的选择后，Azure 会上传你的证书。

   ![Azure 显示新证书](media/logic-apps-enterprise-integration-certificates/new-private-certificate.png) 

## <a name="next-steps"></a>后续步骤

* [创建 B2B 协议](logic-apps-enterprise-integration-agreements.md)
