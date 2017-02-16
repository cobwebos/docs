---
title: "在 Azure 逻辑应用中为 EDIFACT 消息编码 | Microsoft 文档"
description: "如何对逻辑应用使用 Enterprise Integration Pack 中的 EDIFACT 编码器"
services: logic-apps
documentationcenter: .net,nodejs,java
author: padmavc
manager: anneta
editor: 
ms.assetid: 974ac339-d97a-4715-bc92-62d02281e900
ms.service: logic-apps
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/27/2017
ms.author: padmavc
translationtype: Human Translation
ms.sourcegitcommit: 5bc7011d7b0d22a7f8c11a2fee8d002c24d3467c
ms.openlocfilehash: 94d120cd8a5e33733ecc39af96d2719ad59ab090


---
# <a name="get-started-with-encode-edifact-message"></a>编码 EDIFACT 消息入门
验证 EDI 和特定于合作伙伴的属性 

## <a name="prereqs"></a>先决条件
* Azure 帐户；可以创建[免费帐户](https://azure.microsoft.com/free)
* 使用编码 EDIFACT 消息连接器需要集成帐户。 请参阅有关如何创建[集成帐户](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md)、[合作伙伴](logic-apps-enterprise-integration-partners.md)和 [EDIFACT 协议](../logic-apps/logic-apps-enterprise-integration-edifact.md)的详细信息

## <a name="encode-edifact-messages"></a>为 EDIFACT 消息编码
1. [创建逻辑应用](../logic-apps/logic-apps-create-a-logic-app.md)。
2. 此连接器没有任何触发器。 使用其他触发器启动逻辑应用（如请求触发器）。  在逻辑应用设计器中，添加触发器和操作。  在下拉列表中选择“显示 Microsoft 托管的 API”，然后在搜索框中输入“EDIFACT”。  选择“编码 EDIFACT 消息(按协议名称)”或“编码为 EDIFACT 消息(按标识)”：
   
    ![搜索 EDIFACT](media/logic-apps-enterprise-integration-edifact-encode/edifactdecodeimage1.png)  
3. 如果事先未与集成帐户建立任何连接，系统会提示输入连接详细信息：
   
    ![创建集成帐户连接](media/logic-apps-enterprise-integration-edifact-encode/edifactencodeimage1.png)  
4. 输入集成帐户详细信息。  带星号的属性是必填的：
   
   | 属性 | 详细信息 |
   | --- | --- |
   | 连接名称 * |为连接输入任何名称 |
   | 集成帐户 * |输入集成帐户名称。 确保集成帐户和逻辑应用处于相同 Azure 位置 |
   
    完成之后，连接详细信息会类似于下面这样：
   
    ![集成帐户连接](media/logic-apps-enterprise-integration-edifact-encode/edifactencodeimage2.png)
5. 选择“创建” 。
6. 可以看到，已创建连接：
   
    ![集成帐户连接详细信息](media/logic-apps-enterprise-integration-edifact-encode/edifactencodeimage4.png)

#### <a name="encode-edifact-message-by-agreement-name"></a>编码 EDIFACT 消息(按协议名称)
输入 EDIFACT 协议名称以及要编码的 XML 消息进行编码：
   
   ![提供必填字段](media/logic-apps-enterprise-integration-edifact-encode/edifactencodeimage6.png)

#### <a name="encode-edifact-message-by-identities"></a>编码 EDIFACT 消息(按标识)
输入 EDIFACT 协议中配置的发送方标识符、发送方限定符、接收方标识符和接收方限定符。 选择要编码的 XML 消息：  
    ![提供必填字段](media/logic-apps-enterprise-integration-edifact-encode/edifactencodeimage7.png)

## <a name="edifact-encode-details"></a>EDIFACT 编码详细信息
编码 EDIFACT 连接器执行以下操作： 

* 通过将发送方限定符和标识符与接收方限定符和标识符进行匹配来解析协议
* 序列化 EDI 交换，将 XML 编码的消息转换成交换中的 EDI 事务集。
* 应用事务集标头和尾部段
* 为每个传出交换生成交换控制编号、组控制编号和事务集控制编号
* 替换有效负载数据中的分隔符
* 验证 EDI 和特定于合作伙伴的属性
  * 针对消息架构进行的事务集数据元素架构验证。
  * 对事务集数据元素执行的 EDI 验证。
  * 对事务集数据元素执行的扩展验证
* 为每个事务集生成 XML 文档。
* 请求技术和/或功能确认（如果已配置）。
  * 作为技术确认，CONTRL 消息指示交换的接收。
  * 作为功能确认，CONTRL 消息指示收到的交换、组或消息的接受或拒绝（具有错误或不支持的功能的列表）

## <a name="next-steps"></a>后续步骤
[了解有关 Enterprise Integration Pack 的详细信息](logic-apps-enterprise-integration-overview.md "了解 Enterprise Integration Pack") 




<!--HONumber=Jan17_HO5-->


