---
title: "将证书用于 Enterprise Integration Pack | Microsoft Docs"
description: "了解如何将证书用于 Enterprise Integration Pack | Azure 逻辑应用"
services: logic-apps
documentationcenter: .net,nodejs,java
author: padmavc
manager: anneta
editor: cgronlun
ms.assetid: 4cbffd85-fe8d-4dde-aa5b-24108a7caa7d
ms.service: logic-apps
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/03/2016
ms.author: LADocs; padmavc
ms.openlocfilehash: 0570aab14283b38f9efcc50636f0c0c1c8e3ed13
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/11/2017
---
# <a name="learn-about-certificates-and-enterprise-integration-pack"></a>了解证书和 Enterprise Integration Pack
## <a name="overview"></a>概述
企业集成使用证书保护 B2B 通信。 在企业集成应用中，可以使用两种类型的证书：

* 公用证书，必须从证书颁发机构 (CA) 购买。
* 私有证书，可以自己颁发。 这些证书有时称为自签名证书。

## <a name="what-are-certificates"></a>什么是证书？
证书是用于在电子通信中验证参与者身份以及保护电子通信的数字文档。

## <a name="why-use-certificates"></a>为什么使用证书？
B2B 通信有时必须保持机密。 企业集成通过两种方式使用证书保护这些通信：

* 加密消息的内容
* 对消息进行数字签名  

## <a name="upload-a-public-certificate"></a>上传公用证书

要在具有 B2B 功能的逻辑应用中使用公用证书，需要首先将证书上传到集成帐户中。  

上传证书之后，在创建的[协议](logic-apps-enterprise-integration-agreements.md)中定义 B2B 消息属性时，证书可以用于帮助保护消息。  

以下是用于在登录到 Azure 门户之后将公用证书上传到集成帐户中的详细步骤：

1. 选择“更多服务”，并在筛选器搜索框中输入“集成”。 在结果列表中选择“集成帐户”     
![选择“浏览”](media/logic-apps-enterprise-integration-certificates/overview-1.png)  
2. 选择要将证书添加到其中的集成帐户。  
![选择要将证书添加到其中的集成帐户](media/logic-apps-enterprise-integration-certificates/overview-3.png)  
3. 选择“证书”磁贴。  
![选择“证书”磁贴](media/logic-apps-enterprise-integration-certificates/certificate-1.png)
4. 在打开的“证书”边栏选项卡中，选择“添加”按钮。   
![选择“添加”按钮](media/logic-apps-enterprise-integration-certificates/certificate-2.png)
5. 为证书输入**名称**，然后在下拉列表中将证书类型选择为“公共”。  
6. 选择“证书”文本框右侧的文件夹图标。 文件选取器打开时，找到并选择要上传到集成帐户的证书文件。
7. 选择该证书，并在文件选取器中选择“确定”。 这会验证证书并将它上传到集成帐户。
8. 最后，返回“添加证书”边栏选项卡，选择“确定”按钮。  
![选择“确定”按钮](media/logic-apps-enterprise-integration-certificates/certificate-3.png)  
9. 选择“证书”磁贴。 应看到新添加的证书。  
![查看新证书](media/logic-apps-enterprise-integration-certificates/certificate-4.png)  

## <a name="upload-a-private-certificate"></a>上传私有证书

要在具有 B2B 功能的逻辑应用中使用私有证书，可通过执行以下步骤将私有证书上传到集成帐户

1. [将私钥上传到 Key Vault](../key-vault/key-vault-get-started.md "了解 Key Vault") 并提供**密钥名称** 
   
   > [!TIP]
   > 必须授权逻辑应用在 Key Vault 上执行操作。 可以使用以下 PowerShell 命令向逻辑应用服务主体授予访问权限：`Set-AzureRmKeyVaultAccessPolicy -VaultName 'TestcertKeyVault' -ServicePrincipalName '7cd684f4-8a78-49b0-91ec-6a35d38739ba' -PermissionsToKeys decrypt, sign, get, list`  
   > 
   > 

完成上一步后，请向集成帐户添加私有证书。

以下是用于在登录到 Azure 门户之后将私有证书上传到集成帐户中的详细步骤：  
 
1. 选择要将证书添加到其中的集成帐户，然后选择“证书”磁贴。  
![选择“证书”磁贴](media/logic-apps-enterprise-integration-certificates/certificate-1.png)  
2. 在打开的“证书”边栏选项卡中，选择“添加”按钮。   
![选择“添加”按钮](media/logic-apps-enterprise-integration-certificates/certificate-2.png)
3. 为证书输入**名称**，然后在下拉列表中将证书类型选择为“私有”。   
4. 选择“证书”文本框右侧的文件夹图标。 文件选取器打开时，找到要上传到集成帐户的相应公共证书。   
   
   > [!Note]
   > 添加私有证书时，请务必添加相应的公共证书，以便在 [AS2 协议](logic-apps-enterprise-integration-as2.md)中显示用于对消息进行签名和加密的接收和发送设置。
   > 
   >   

5. 选择“资源组”、“Key Vault”、“密钥名称”，并选择“确定”按钮。  
![添加证书](media/logic-apps-enterprise-integration-certificates/privatecertificate-1.png)  
6. 选择“证书”磁贴。 应看到新添加的证书。
![查看新证书](media/logic-apps-enterprise-integration-certificates/privatecertificate-2.png)  



* [创建 B2B 协议](logic-apps-enterprise-integration-agreements.md)  
* [了解有关密钥保管库的详细信息](../key-vault/key-vault-get-started.md "了解密钥保管库")  

