---
title: "了解 Enterprise Integration Pack 编码 X12 消息连接器 | Microsoft Docs"
description: "了解如何将合作伙伴与 Enterprise Integration Pack 和逻辑应用一起使用"
services: logic-apps
documentationcenter: .net,nodejs,java
author: padmavc
manager: erikre
editor: 
ms.assetid: a01e9ca9-816b-479e-ab11-4a984f10f62d
ms.service: logic-apps
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/15/2016
ms.author: padmavc
translationtype: Human Translation
ms.sourcegitcommit: 219dcbfdca145bedb570eb9ef747ee00cc0342eb
ms.openlocfilehash: 54626bdb7b07a91e5a2b1d16fed10be6cccacc74


---
# <a name="get-started-with-encode-x12-message"></a>编码 X12 消息入门
验证 EDI 和特定于合作伙伴的属性，将 XML 编码的消息转换为交换中的 EDI 事务集并请求技术和/或功能确认

## <a name="create-the-connection"></a>创建连接
### <a name="prerequisites"></a>先决条件
* Azure 帐户；可以创建[免费帐户](https://azure.microsoft.com/free)
* 使用编码 x12 消息连接器需要集成帐户。 请参阅有关如何创建[集成帐户](app-service-logic-enterprise-integration-create-integration-account.md)、[合作伙伴](app-service-logic-enterprise-integration-partners.md)和 [X12 协议](app-service-logic-enterprise-integration-x12.md)的详细信息

### <a name="connect-to-encode-x12-message-using-the-following-steps"></a>可使用以下步骤连接到编码 X12 消息：
1. [创建逻辑应用](app-service-logic-create-a-logic-app.md)提供了一个示例
2. 此连接器没有任何触发器。 使用其他触发器启动逻辑应用（如请求触发器）。  在逻辑应用设计器中，添加触发器和操作。  在下拉列表中选择“显示 Microsoft 托管的 API”，然后在搜索框中输入“x12”。  按协议名称选择 X12 - 编码 X12 消息，或者按标识选择 X12 - 编码为 X 12 消息。  
   
    ![搜索 x12](./media/app-service-logic-enterprise-integration-x12connector/x12decodeimage1.png) 
3. 如果以前尚未创建任何与集成帐户的连接，则系统提示你输入连接详细信息
   
    ![集成帐户连接](./media/app-service-logic-enterprise-integration-x12connector/x12encodeimage1.png) 
4. 输入集成帐户详细信息。  带星号的属性是必填的
   
   | 属性 | 详细信息 |
   | --- | --- |
   | 连接名称 * |为连接输入任何名称 |
   | 集成帐户 * |输入集成帐户名称。 确保集成帐户和逻辑应用处于相同 Azure 位置 |
   
    完成之后，连接详细信息与下面的内容类似
   
    ![集成帐户连接已创建](./media/app-service-logic-enterprise-integration-x12connector/x12encodeimage2.png) 
5. 选择“创建”
6. 请注意，连接已创建。
   
    ![集成帐户连接详细信息](./media/app-service-logic-enterprise-integration-x12connector/x12encodeimage3.png) 

#### <a name="x12---encode-x12-message-by-agreement-name"></a>X12 - 编码 X12 消息(按协议名称)
1. 从下拉列表中选择 X12 协议和要编码的 xml 消息。
   
    ![提供必填字段](./media/app-service-logic-enterprise-integration-x12connector/x12encodeimage4.png) 

#### <a name="x12---encode-x12-message-by-identities"></a>X12 - 编码 X12 消息(按标识)
1. 提供 X12 协议中配置的发送方标识符、发送方限定符、接收方标识符和接收方限定符。  选择要编码的 xml 消息
   
   ![提供必填字段](./media/app-service-logic-enterprise-integration-x12connector/x12encodeimage5.png) 

## <a name="x12-encode-does-following"></a>X12 编码执行以下操作：
* 通过匹配发送方和接收方上下文属性进行协议解析。
* 序列化 EDI 交换，将 XML 编码的消息转换成交换中的 EDI 事务集。
* 应用事务集标头和尾部段
* 为每个传出交换生成交换控制编号、组控制编号和事务集控制编号
* 替换有效负载数据中的分隔符
* 验证 EDI 和特定于合作伙伴的属性
  * 针对消息架构进行的事务集数据元素架构验证
  * 对事务集数据元素执行的 EDI 验证。
  * 对事务集数据元素执行的扩展验证
* 请求技术和/或功能确认（如果已配置）。
  * 技术确认作为标头验证的结果生成。 技术确认报告地址接收方进行的交换标头和尾部处理的状态
  * 功能确认作为正文验证的结果生成。 功能确认报告在处理收到的文档时遇到的每个错误

## <a name="next-steps"></a>后续步骤
[了解有关 Enterprise Integration Pack 的详细信息](app-service-logic-enterprise-integration-overview.md "了解 Enterprise Integration Pack") 




<!--HONumber=Nov16_HO3-->


