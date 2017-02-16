---
title: "了解如何为 Enterprise Integration Pack 创建 AS2 协议 | Microsoft Docs"
description: "了解如何为 Enterprise Integration Pack 创建 AS2 协议| Azure 逻辑应用"
services: logic-apps
documentationcenter: .net,nodejs,java
author: msftman
manager: anneta
editor: 
ms.assetid: c9b7e1a9-4791-474c-855f-988bd7bf4b7f
ms.service: logic-apps
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/29/2016
ms.author: deonhe
translationtype: Human Translation
ms.sourcegitcommit: 47a1c5653d84a55224c18222bc547e1c863ea442
ms.openlocfilehash: ea256557a38b3ce6cb457d195fa4b48cfd8879b8


---
# <a name="enterprise-integration-with-as2"></a>与 AS2 的企业集成
## <a name="create-an-as2-agreement"></a>创建 AS2 协议
若要使用逻辑应用中的企业功能，必须首先创建协议。 

### <a name="heres-what-you-need-before-you-get-started"></a>以下是开始之前需要满足的条件
* 在 Azure 订阅中定义了[集成帐户](../logic-apps/logic-apps-enterprise-integration-accounts.md)  
* 已在集成帐户中定义了至少两个[合作伙伴](logic-apps-enterprise-integration-partners.md)  

> [!NOTE]
> 创建协议时，协议文件中的内容必须与协议类型匹配。    
> 
> 

[创建了集成帐户](../logic-apps/logic-apps-enterprise-integration-accounts.md)并[添加了合作伙伴](logic-apps-enterprise-integration-partners.md)之后，可以通过执行以下步骤来创建协议：  

### <a name="from-the-azure-portal-home-page"></a>在 Azure 门户主页中
登录到 [Azure 门户](http://portal.azure.com "Azure 门户")之后：  

1. 选择“更多服务”，然后在筛选器搜索框中输入“集成”。 在结果列表中选择“集成帐户”    
![](./media/logic-apps-enterprise-integration-agreements/overview-1.png)    
2. 选择要将证书添加到其中的集成帐户。 
![](./media/logic-apps-enterprise-integration-overview/overview-3.png)  
3. 选择“协议”磁贴。 如果看不到磁贴协议，请首先添加它。   
![](./media/logic-apps-enterprise-integration-agreements/agreement-1.png)   
4. 在打开的“协议”边栏选项卡中，选择“添加”按钮。  
![](./media/logic-apps-enterprise-integration-agreements/agreement-2.png)  
5. 为协议输入“名称”，在“协议”边栏选项卡中，从“协议类型”中选择“AS2”、“主机合作伙伴”、“主机标识”、“来宾合作伙伴”、“来宾标识”。  
![](./media/logic-apps-enterprise-integration-agreements/agreement-3.png)  

下面是一些有助于配置协议的设置的详细信息： 

| 属性 | 说明 |
| --- | --- |
| 管理方 |协议需要有管理方和托管方。 管理方代表配置协议的组织。 |
| 管理方标识 |管理方的标识符。 |
| 托管方 |协议需要有管理方和托管方。 托管方代表与管理方进行交易的组织。 |
| 托管方标识 |托管方的标识符。 |
| 接收设置 |这些属性适用于协议接收到的所有消息 |
| 发送设置 |这些属性适用于协议发送的所有消息 |

让我们继续：  

1. 选择“接收设置”以配置通过此协议收到的消息的处理方式。  
   
   * 你也可以覆盖传入消息中的属性。 若要执行此操作，请选择“替代消息属性”。
   * 如果要对所有传入消息进行签名，请选择“应对消息进行签名”。 如果选择此选项，则需选择“来宾合作伙伴公共证书”，以验证消息的签名。
   * 如果要对所有传入消息进行加密，请选择“应对消息进行加密”。  如果选择此选项，则需选择“主机合作伙伴私有证书”，以便对传入消息进行解密。
   * 你可能还需要压缩消息。 若要执行此操作，请选择“应对消息进行压缩”。    
   * 选择“发送 MDN”，以便发送所接收消息的同步 MDN
   * 选择“发送已签名的 MDN”，以便发送所接收消息的已签名 MDN
   * 选择“发送异步 MDN”，以便发送所接收消息的异步 MDN     
    ![](./media/logic-apps-enterprise-integration-agreements/agreement-4.png)  

如果想要了解有关接收设置的作用的详细信息，请参阅下表。  

| 属性 | 说明 |
| --- | --- |
| 替代消息属性 |选择此选项表示可以替代接收消息中的属性 |
| 对消息进行签名 |启用此选项以要求对消息进行数字签名。  配置来宾合作伙伴公共证书以进行签名验证  |
| 对消息进行加密 |启用此选项以要求对消息进行加密 非加密的消息将被拒绝。 配置主机合作伙伴私有证书以对消息进行解密  |
| 对消息进行压缩 |启用此选项以要求对消息进行压缩 非压缩的消息将被拒绝。 |
| MDN 文本 |这是要发送到消息发送方的默认 MDN |
| 发送 MDN |启用此选项以允许发送 MDN。 |
| 发送已签名的 MDN |启用此选项以要求对 MDN 进行签名。 |
| MIC 算法 | |
| 发送异步 MDN |启用此选项以要求异步发送消息 |
| 代码 |这是要将 MDN 发送到的 URL。 |

现在，让我们继续：  

1. 选择“发送设置”以配置通过此协议发送的消息的处理方式。  

   * 选择“启用消息签名”，将已签名消息发送到合作伙伴。 如果选择此选项，则需选择“主机合作伙伴私有证书 MIC 算法”和“主机合作伙伴私有证书”，以对消息进行签名。
   * 选择“启用消息加密”，将已加密消息发送到合作伙伴。 如果选择此选项，则需选择“来宾合作伙伴公共证书算法”和“来宾合作伙伴公共证书”，对消息进行加密。
   * 选择“应对消息进行压缩”，以便压缩消息。 
   * 选择“展开 HTTP 标头”，将 HTTP 内容类型标头展开为单个行 
   * 选择“请求 MDN”，以接收所发送消息的同步 MDN
   * 选择“请求已签名的 MDN”，以接收所发送消息的已签名 MDN
   * 选择“请求异步 MDN”，以接收所发送消息的异步 MDN。 如果选择此选项，则需提供一个向其发送 MDN 的 URL  
   * 选择“启用 NRR”，以启用回执的不可否认性    
   ![](./media/logic-apps-enterprise-integration-agreements/agreement-5.png)  

如果想要了解有关发送设置的作用的详细信息，请参阅下表。  

| 属性 | 说明 |
| --- | --- |
| 启用消息签名 |选择此选项可对协议发送的所有消息进行签名。 |
| MIC 算法 |选择要用于消息签名的算法。 配置主机合作伙伴私有证书 MIC 算法以对消息进行签名 |
| 证书 |选择要用于消息签名的证书。 配置主机合作伙伴私有证书以对消息进行签名 |
| 启用消息加密 |选中此选项可对协议发送的所有消息进行加密。 配置来宾合作伙伴公共证书算法以对消息进行加密 |
| 加密算法 |选择要用于消息加密的加密算法。 配置来宾合作伙伴公共证书以对消息进行加密 |
| 展开 HTTP 标头 |选择此选项可将 HTTP 内容类型标头展开为单个行 |
| 请求 MDN |选中此选项可对协议发送的所有消息请求 MDN |
| 要求对 MDN 进行签名 |启用此选项以要求对发送到协议的所有 MDN 进行签名 |
| 要求发送异步 MDN |启用此选项可将异步 MDN 发送到协议 |
| 代码 |要将 MDN 发送到的 URL |
| 启用 NRR |选择此选项可启用回执的不可否认性 |

在“集成帐户”边栏选项卡上选择“协议”磁贴，会看到列出新添加的协议。  
![](./media/logic-apps-enterprise-integration-agreements/agreement-6.png)

## <a name="next-steps"></a>后续步骤
* [了解有关 Enterprise Integration Pack 的详细信息](logic-apps-enterprise-integration-overview.md "了解 Enterprise Integration Pack")  


<!--HONumber=Jan17_HO4-->


