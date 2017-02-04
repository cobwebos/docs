---
title: "了解 Enterprise Integration Pack 编码 AS2 消息连接器 | Microsoft Docs"
description: "了解如何将合作伙伴与 Enterprise Integration Pack 和逻辑应用一起使用"
services: logic-apps
documentationcenter: .net,nodejs,java
author: padmavc
manager: anneta
editor: 
ms.assetid: 332fb9e3-576c-4683-bd10-d177a0ebe9a3
ms.service: logic-apps
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/15/2016
ms.author: padmavc
translationtype: Human Translation
ms.sourcegitcommit: dc8c9eac941f133bcb3a9807334075bfba15de46
ms.openlocfilehash: b32713cfd80faac403d44085dbffa8d1f4a91fd9


---
# <a name="get-started-with-encode-as2-message"></a>编码 AS2 消息入门
传输消息时连接到编码 AS2 消息以建立安全性和可靠性。 编码 AS2 消息通过邮件处置通知 (MDN) 提供数字签名、解密和确认等功能，它还潜在支持不可否认性。

## <a name="create-the-connection"></a>创建连接
### <a name="prerequisites"></a>先决条件
* Azure 帐户；可以创建[免费帐户](https://azure.microsoft.com/free)
* 使用编码 AS2 消息连接器需要集成帐户。 请参阅有关如何创建[集成帐户](logic-apps-enterprise-integration-create-integration-account.md)、[合作伙伴](logic-apps-enterprise-integration-partners.md)和 [AS2 协议](logic-apps-enterprise-integration-as2.md)的详细信息

### <a name="connect-to-encode-as2-message-using-the-following-steps"></a>使用以下步骤连接到编码 AS2 消息：
1. [创建逻辑应用](logic-apps-create-a-logic-app.md)提供了一个示例
2. 此连接器没有任何触发器。 使用其他触发器启动逻辑应用（如请求触发器）。  在逻辑应用设计器中，添加触发器和操作。  在下拉列表中选择“显示 Microsoft 托管的 API”，然后在搜索框中输入“AS2”。  选择“AS2 - 编码 AS2 消息”
   
    ![搜索 AS2](./media/logic-apps-enterprise-integration-as2-encode/as2decodeimage1.png)
3. 如果以前尚未创建任何与集成帐户的连接，则系统提示你输入连接详细信息
   
    ![创建到集成帐户的连接](./media/logic-apps-enterprise-integration-as2-encode/as2encodeimage1.png)  
4. 输入集成帐户详细信息。  带星号的属性是必填的
   
   | 属性 | 详细信息 |
   | --- | --- |
   | 连接名称 * |为连接输入任何名称 |
   | 集成帐户 * |输入集成帐户名称。 确保集成帐户和逻辑应用处于相同 Azure 位置 |
   
      完成之后，连接详细信息与下面的内容类似
   
      ![已建立集成连接](./media/logic-apps-enterprise-integration-as2-encode/as2encodeimage2.png)  
5. 选择“创建”
6. 请注意，连接已创建。  提供 AS2-From、AS2-To 标识符（根据协议中的配置）和正文（消息有效负载）详细信息。 
   
    ![提供必填字段](./media/logic-apps-enterprise-integration-as2-encode/as2encodeimage3.png)

## <a name="the-as2-encode-does-the-following"></a>AS2 编码执行以下操作
* 应用 AS2/HTTP 标头
* 对传出消息进行签名（如果已配置）
* 对传出消息进行加密（如果已配置）
* 压缩消息（如果已配置）

## <a name="try-it-for-yourself"></a>亲自试用
何不尝试一下。 单击[此处](https://azure.microsoft.com/documentation/templates/201-logic-app-as2-send-receive/)部署自己的使用逻辑应用 AS2 功能的完全可操作逻辑应用

## <a name="next-steps"></a>后续步骤
[了解有关 Enterprise Integration Pack 的详细信息](logic-apps-enterprise-integration-overview.md "了解 Enterprise Integration Pack") 




<!--HONumber=Jan17_HO3-->


