---
title: "了解如何为 Enterprise Integration Pack 创建 AS2 协议"
description: "了解如何为 Enterprise Integration Pack 创建 AS2 协议 | Microsoft Azure App Service"
services: logic-apps
documentationcenter: .net,nodejs,java
author: msftman
manager: erikre
editor: cgronlun
ms.assetid: c9b7e1a9-4791-474c-855f-988bd7bf4b7f
ms.service: logic-apps
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/29/2016
ms.author: deonhe
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 13b2757a10c6da7ca3dd14c3db9620fab0035561


---
# <a name="enterprise-integration-with-as2"></a>与 AS2 的企业集成
## <a name="create-an-as2-agreement"></a>创建 AS2 协议
若要使用逻辑应用中的企业功能，必须首先创建协议。 

### <a name="heres-what-you-need-before-you-get-started"></a>以下是开始之前需要满足的条件
* 在 Azure 订阅中定义了[集成帐户](app-service-logic-enterprise-integration-accounts.md)  
* 已在集成帐户中定义了至少两个[合作伙伴](app-service-logic-enterprise-integration-partners.md)  

> [!NOTE]
> 创建协议时，协议文件中的内容必须与协议类型匹配。    
> 
> 

[创建了集成帐户](app-service-logic-enterprise-integration-accounts.md)并[添加了合作伙伴](app-service-logic-enterprise-integration-partners.md)之后，可以通过执行以下步骤来创建协议：  

### <a name="from-the-azure-portal-home-page"></a>在 Azure 门户主页中
登录到 [Azure 门户](http://portal.azure.com "Azure 门户")之后：  

1. 从左侧菜单中选择“浏览”。  

> [!TIP]
> 如果看不到“浏览”链接，则可能需要先展开菜单。 可通过选择位于折叠菜单左上角的“显示菜单”链接来执行此操作。  
> 
> 

![](./media/app-service-logic-enterprise-integration-overview/overview-1.png)    

1. 在筛选器搜索框中输入“集成”，然后从结果列表中选择“集成帐户”。       
   ![](./media/app-service-logic-enterprise-integration-overview/overview-2.png)  
2. 在打开的“集成帐户”边栏选项卡中，选择将在其中创建协议的集成帐户。 如果看不到任何集成帐户列表，请[首先创建一个](app-service-logic-enterprise-integration-accounts.md "All about integration accounts")。  
   ![](./media/app-service-logic-enterprise-integration-overview/overview-3.png)  
3. 选择“协议”磁贴。 如果看不到磁贴协议，请首先添加它。   
   ![](./media/app-service-logic-enterprise-integration-agreements/agreement-1.png)   
4. 在打开的“协议”边栏选项卡中，选择“添加”按钮。  
   ![](./media/app-service-logic-enterprise-integration-agreements/agreement-2.png)  
5. 在打开的“协议”边栏选项卡中，输入协议的“名称”，然后选择“管理方”、“管理方标识”、“托管方”、“托管方标识”。  
   ![](./media/app-service-logic-enterprise-integration-agreements/agreement-3.png)  

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
   
   * 你也可以覆盖传入消息中的属性。 若要执行此操作，请选择“替代消息属性”复选框。
   * 如果想要对所有传入消息进行签名，请选择“对消息进行签名”复选框。 如果选择此选项，那么也需要选择用于验证消息的签名的“证书”。
   * 你还可以要求加密消息。 若要执行此操作，请选择“对消息进行加密”复选框。 然后需要选择用于对传入消息进行解码“证书”选项。
   * 你可能还需要压缩消息。 若要执行此操作，请选择“对消息进行压缩”复选框。  
     ![](./media/app-service-logic-enterprise-integration-agreements/agreement-4.png)  

如果想要了解有关接收设置的作用的详细信息，请参阅下表。  

| 属性 | 说明 |
| --- | --- |
| 替代消息属性 |选择此选项表示可以替代接收消息中的属性 |
| 对消息进行签名 |启用此选项以要求对消息进行数字签名 |
| 对消息进行加密 |启用此选项以要求对消息进行加密 非加密的消息将被拒绝。 |
| 对消息进行压缩 |启用此选项以要求对消息进行压缩 非压缩的消息将被拒绝。 |
| MDN 文本 |这是要发送到消息发送方的默认 MDN |
| 发送 MDN |启用此选项以允许发送 MDN。 |
| 发送已签名的 MDN |启用此选项以要求对 MDN 进行签名。 |
| MIC 算法 | |
| 发送异步 MDN |启用此选项以要求异步发送消息 |
| 代码 |这是将消息发送到的 URL。 |

现在，让我们继续：  

1. 选择“发送设置”以配置通过此协议发送的消息的处理方式。  
   ![](./media/app-service-logic-enterprise-integration-agreements/agreement-5.png)  

如果想要了解有关发送设置的作用的详细信息，请参阅下表。  

| 属性 | 说明 |
| --- | --- |
| 启用消息签名 |选择此复选框可对协议发送的所有消息进行签名。 |
| MIC 算法 |选择要在消息签名中使用的算法 |
| 证书 |选择要在消息签名中使用的证书 |
| 启用消息加密 |选中此复选框可对协议发送的所有消息进行加密。 |
| 加密算法 |选择要在消息加密中使用的加密算法 |
| 展开 HTTP 标头 |选择此复选框可将 HTTP content-type 标头展开为单个行。 |
| 请求 MDN |选中此复选框可对协议发送的所有消息请求 MDN |
| 要求对 MDN 进行签名 |启用此选项以要求对发送到协议的所有 MDN 进行签名 |
| 要求发送异步 MDN |启用此选项可将异步 MDN 发送到协议 |
| 代码 |要将 MDN 发送到的 URL |
| 启用 NRR |选择此复选框可启用回执的不可否认性 |

即将完成！  

1. 在“集成帐户”边栏选项卡上选择“协议”磁贴，会看到列出新添加的协议。  
   ![](./media/app-service-logic-enterprise-integration-agreements/agreement-6.png)




<!--HONumber=Nov16_HO3-->


