---
title: 在 Azure 逻辑应用中使用证书保护 B2B 消息 | Microsoft Docs
description: 使用 Enterprise Integration Pack 添加证书来保护 B2B 消息
services: logic-apps
documentationcenter: .net,nodejs,java
author: padmavc
manager: jeconnoc
editor: ''
ms.assetid: 4cbffd85-fe8d-4dde-aa5b-24108a7caa7d
ms.service: logic-apps
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/03/2016
ms.author: LADocs; padmavc
ms.openlocfilehash: 7ba76a15792fe40b2a628b030f06930641d197a3
ms.sourcegitcommit: 6f6d073930203ec977f5c283358a19a2f39872af
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/11/2018
ms.locfileid: "35299328"
---
# <a name="secure-b2b-messages-with-certificates"></a>使用证书保护 B2B 消息

有时候，你需要保留 B2B 通信凭据。 为帮助你保护企业集成应用（具体而言是逻辑应用）的 B2B 通信，可以向集成帐户添加证书。 证书是用于在电子通信中验证参与者身份的数字文档。
证书可以通过以下方式来帮助你保护通信：

* 加密消息内容
* 对消息进行数字签名  

在企业集成应用中，可以使用以下证书：

* 公用证书，必须从证书颁发机构 (CA) 购买。
* 私有证书，可以自己颁发。 这些证书有时称为自签名证书。

## <a name="upload-a-public-certificate"></a>上传公用证书

要在具有 B2B 功能的逻辑应用中使用“公用证书”，必须首先将证书上传到集成帐户中。 在你创建的[协议](logic-apps-enterprise-integration-agreements.md)中定义属性后，可以使用证书来帮助你保护 B2B 消息。

1. 登录到 [Azure 门户](https://portal.azure.com)。

2. 在 Azure 主菜单中，选择“所有服务”。 在搜索框中输入“集成”，然后选择“集成帐户”。

   ![查找集成帐户](media/logic-apps-enterprise-integration-certificates/overview-1.png)  

3. 在“集成帐户”下，选择要添加证书的集成帐户。

   ![选择要将证书添加到其中的集成帐户](media/logic-apps-enterprise-integration-certificates/overview-3.png)  

4. 选择“证书”磁贴。  

   ![选择“证书”](media/logic-apps-enterprise-integration-certificates/certificate-1.png)

5. 在“证书”下，选择“添加”。

   ![选择“添加”](media/logic-apps-enterprise-integration-certificates/certificate-2.png)

6. 在“添加证书”下，提供证书的详细信息。
   
   1. 输入证书**名称**。 对于证书类型，选择“公用”。

   2. 在“证书”框的右侧，选择文件夹图标。 
   查找并选择要上传的证书文件。 
   完成后，选择“确定”。

      ![上传公用证书](media/logic-apps-enterprise-integration-certificates/certificate-3.png)

   在验证你的选择后，Azure 会上传你的证书。

   ![查看新证书](media/logic-apps-enterprise-integration-certificates/certificate-4.png) 

## <a name="upload-a-private-certificate"></a>上传私有证书

要在具有 B2B 功能的逻辑应用中使用“私有证书”，必须首先将证书上传到集成帐户中。 还需要有一个私钥，需要首先将其添加到 [Azure Key Vault](../key-vault/key-vault-get-started.md)。 

在你创建的[协议](logic-apps-enterprise-integration-agreements.md)中定义属性后，可以使用证书来帮助你保护 B2B 消息。

> [!NOTE]
> 对于私有证书，请确保添加要显示在用于对消息进行签名和加密的 [AS2 协议](logic-apps-enterprise-integration-as2.md)发送和接收设置中的对应公用证书。

1. [将私钥添加到 Azure Key Vault](../key-vault/key-vault-get-started.md#add) 并提供**密钥名称**。
   
2. 授权 Azure 逻辑应用对 Azure Key Vault 执行操作。 若要向逻辑应用服务主体授予访问权限，请使用 PowerShell 命令 [Set-AzureRmKeyVaultAccessPolicy](https://docs.microsoft.com/powershell/module/azurerm.keyvault/set-azurermkeyvaultaccesspolicy)，例如：

   `Set-AzureRmKeyVaultAccessPolicy -VaultName 'TestcertKeyVault' -ServicePrincipalName 
   '7cd684f4-8a78-49b0-91ec-6a35d38739ba' -PermissionsToKeys decrypt, sign, get, list`
 
3. 登录到 [Azure 门户](https://portal.azure.com)。

4. 在 Azure 主菜单中，选择“所有服务”。 在搜索框中输入“集成”，然后选择“集成帐户”。

   ![查找集成帐户](media/logic-apps-enterprise-integration-certificates/overview-1.png) 

5. 在“集成帐户”下，选择要添加证书的集成帐户。

6. 选择“证书”磁贴。  

   ![选择“证书”磁贴](media/logic-apps-enterprise-integration-certificates/certificate-1.png)

7. 在“证书”下，选择“添加”。   

   ![选择“添加”按钮](media/logic-apps-enterprise-integration-certificates/certificate-2.png)

8. 在“添加证书”下，提供证书的详细信息。
   
   1. 输入证书**名称**。 对于证书类型，选择“私有”。

   2. 在“证书”框的右侧，选择文件夹图标。 
   查找并选择要上传的证书文件。 
   此外，请选择**资源组**、**Key Vault** 和**密钥名称**。 
   完成后，选择“确定”。

      ![添加证书](media/logic-apps-enterprise-integration-certificates/privatecertificate-1.png)

   在验证你的选择后，Azure 会上传你的证书。

   ![查看新证书](media/logic-apps-enterprise-integration-certificates/privatecertificate-2.png)

## <a name="next-steps"></a>后续步骤

* [创建 B2B 协议](logic-apps-enterprise-integration-agreements.md)
