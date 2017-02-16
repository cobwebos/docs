---
title: "使用 Enterprise Integration Pack 创建 B2B 解决方案 | Microsoft Docs"
description: "了解有关使用 Enterprise Integration Pack 的 B2B 功能接收数据的信息"
services: logic-apps
documentationcenter: .net,nodejs,java
author: msftman
manager: anneta
editor: cgronlun
ms.assetid: 20fc3722-6f8b-402f-b391-b84e9df6fcff
ms.service: logic-apps
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/08/2016
ms.author: deonhe
translationtype: Human Translation
ms.sourcegitcommit: dc8c9eac941f133bcb3a9807334075bfba15de46
ms.openlocfilehash: cb8cac0c63930139760617d721faffeef70cfbec


---
# <a name="learn-about-receiving-data-using-the-b2b-features-of-the-enterprise-integration-pack"></a>了解有关使用 Enterprise Integration Pack 的 B2B 功能接收数据的信息
## <a name="overview"></a>概述
本文档是逻辑应用 Enterprise Integration Pack 的一部分。 请查看概述以了解有关 [Enterprise Integration Pack 的功能](logic-apps-enterprise-integration-overview.md)的详细信息。

## <a name="prerequisites"></a>先决条件
若要使用 AS2 和 X12，需要 Enterprise Integration 帐户

[如何创建 Enterprise Integration 帐户](../logic-apps/logic-apps-enterprise-integration-accounts.md)

## <a name="how-to-use-the-logic-apps-b2b-connectors"></a>如何使用逻辑应用 B2B 连接器
一旦创建了集成帐户并向它添加了合作伙伴和协议，你便准备好创建实现企业到企业 (B2B) 工作流的逻辑应用。

在此演练中，你将了解如何使用 AS2 和 X12 操作创建一个从贸易合作伙伴接收数据的企业到企业逻辑应用。

1. 创建一个新逻辑应用并[将它链接到集成帐户](../logic-apps/logic-apps-enterprise-integration-accounts.md)。  
2. 向逻辑应用添加“请求 - 当收到 HTTP 请求时”触发器  
   ![](./media/logic-apps-enterprise-integration-b2b/flatfile-1.png)  
3. 通过首先选择“添加操作”来添加“解码 AS2”操作  
   ![](./media/logic-apps-enterprise-integration-b2b/transform-2.png)  
4. 在搜索框中输入 **as2** 一词，以便在所有操作中筛选出要使用的操作  
   ![](./media/logic-apps-enterprise-integration-b2b/b2b-5.png)  
5. 选择“AS2 - 解码 AS2 消息”操作  
   ![](./media/logic-apps-enterprise-integration-b2b/b2b-6.png)  
6. 如图所示，添加将用作输入的“正文”。 在此示例中，选择触发逻辑应用的 HTTP 请求的正文。 或者，可以在“标头”字段中输入表达式来输入标头：
   
    @triggerOutputs()['headers']
7. 添加 AS2 所需的“标头”。 这些内容会处于 HTTP 请求标头中。 在此示例中，选择触发逻辑应用的 HTTP 请求的标头。
8. 现在，通过再次选择“添加操作”来添加“解码 X12 消息”操作  
   ![](./media/logic-apps-enterprise-integration-b2b/b2b-9.png)   
9. 在搜索框中输入 **x12** 一词，以便在所有操作中筛选出要使用的操作  
   ![](./media/logic-apps-enterprise-integration-b2b/b2b-10.png)  
10. 选择“X12 - 解码 X12 消息”操作以将它添加到逻辑应用  
    ![](./media/logic-apps-enterprise-integration-b2b/b2b-as2message.png)  
11. 现在需要指定此操作的输入，这会是上面 AS2 操作的输出。 实际消息内容处于 JSON 对象中，并且进行 base64 编码。 因此需要指定表达式作为输入，因此在“要解码的 X12 平面文件消息”输入字段中输入以下表达式  
    
    @base64ToString(body('Decode_AS2_message')?['AS2Message']?['Content'])  
12. 此步骤会解码从贸易合作伙伴收到的 X12 数据，并在 JSON 对象中输入一些项目。 若要使合作伙伴知道接收了数据，可以在 HTTP 响应操作中发送回包含 AS2 消息处置通知 (MDN) 的响应  
13. 通过选择“添加操作”来添加“响应”操作   
    ![](./media/logic-apps-enterprise-integration-b2b/b2b-14.png)  
14. 在搜索框中输入**响应**一词，以便在所有操作中筛选出要使用的操作  
    ![](./media/logic-apps-enterprise-integration-b2b/b2b-15.png)  
15. 选择“响应”操作以添加它  
    ![](./media/logic-apps-enterprise-integration-b2b/b2b-16.png)  
16. 使用以下表达式设置响应“正文”字段，以便从“解码 X12 消息”操作的输出访问 MDN  
    
    @base64ToString(body('Decode_AS2_message')?['OutgoingMdn']?['Content'])  

![](./media/logic-apps-enterprise-integration-b2b/b2b-17.png)  

1. 保存工作  
   ![](./media/logic-apps-enterprise-integration-b2b/transform-5.png)  

此时，已完成 B2B 逻辑应用设置。 在实际应用程序中，你可能要将解码的 X12 数据存储在 LOB 应用程序或数据存储中。 可以方便地添加其他操作来实现此目的，也可以编写自定义 API 以连接到自己的 LOB 应用程序并在逻辑应用中使用这些 API。

## <a name="features-and-use-cases"></a>功能和用例
* 通过 AS2 和 X12 解码和编码操作可以使用逻辑应用从贸易合作伙伴接收数据并向它们发送数据（使用行业标准协议）  
* 可以根据需要结合使用或不结合使用 AS2 和 X12 与贸易合作伙伴交换数据
* 通过 B2B 操作可以轻松地在集成帐户中创建合作伙伴和协议并在逻辑应用中使用它们  
* 通过使用其他操作扩展逻辑应用，可以与其他应用程序和服务（如 SalesForce）之间发送和接收数据  

## <a name="learn-more"></a>了解详细信息
[了解有关 Enterprise Integration Pack 的详细信息](logic-apps-enterprise-integration-overview.md)  




<!--HONumber=Jan17_HO3-->


