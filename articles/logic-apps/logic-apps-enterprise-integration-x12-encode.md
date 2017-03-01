---
title: "在 Azure 逻辑应用中编码 X12 消息 | Microsoft 文档"
description: "如何在逻辑应用中使用 Enterprise Integration Pack 内的 X12 编码器"
services: logic-apps
documentationcenter: .net,nodejs,java
author: padmavc
manager: anneta
editor: 
ms.assetid: a01e9ca9-816b-479e-ab11-4a984f10f62d
ms.service: logic-apps
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/27/2017
ms.author: padmavc
translationtype: Human Translation
ms.sourcegitcommit: bd339e60aff1edca7f86e0ae82746f55eb67b296
ms.openlocfilehash: 9f7b53cfafa8c14ab46cf80015afc8da4e0fda2b


---
# <a name="get-started-with-encode-x12-message"></a>编码 X12 消息入门
验证 EDI 和特定于合作伙伴的属性，将 XML 编码的消息转换为交换中的 EDI 事务集并请求技术和/或功能确认

## <a name="prereqs"></a>先决条件
* Azure 帐户；可以创建[免费帐户](https://azure.microsoft.com/free)
* 使用编码 x12 消息连接器需要集成帐户。 请参阅有关如何创建[集成帐户](logic-apps-enterprise-integration-create-integration-account.md)、[合作伙伴](logic-apps-enterprise-integration-partners.md)和 [X12 协议](logic-apps-enterprise-integration-x12.md)的详细信息

## <a name="encode-x12-messages"></a>编码 X12 消息

1. [创建逻辑应用](logic-apps-create-a-logic-app.md)。
2. 此连接器没有任何触发器。 使用其他触发器启动逻辑应用（如请求触发器）。  在逻辑应用设计器中，添加触发器和操作。  在下拉列表中选择“显示 Microsoft 托管的 API”，然后在搜索框中输入“x12”。  选择“X12 - 编码 X12 消息(按协议名称)”，或者选择“X12 - 编码为 X 12 消息(按标识)”：  
   
    ![搜索 x12](./media/logic-apps-enterprise-integration-x12-encode/x12decodeimage1.png) 
3. 如果事先未与集成帐户建立任何连接，系统会提示输入连接详细信息：
   
    ![集成帐户连接](./media/logic-apps-enterprise-integration-x12-encode/x12encodeimage1.png) 
4. 输入集成帐户详细信息。  带星号的属性是必填的：
   
   | 属性 | 详细信息 |
   | --- | --- |
   | 连接名称 * |为连接输入任何名称 |
   | 集成帐户 * |输入集成帐户名称。 确保集成帐户和逻辑应用处于相同 Azure 位置 |
   
    完成之后，连接详细信息会类似于下面这样：
   
    ![集成帐户连接已创建](./media/logic-apps-enterprise-integration-x12-encode/x12encodeimage2.png) 
5. 选择“创建” 。
6. 请注意，连接已创建。
   
    ![集成帐户连接详细信息](./media/logic-apps-enterprise-integration-x12-encode/x12encodeimage3.png) 

#### <a name="encode-x12-messages-by-agreement-name"></a>编码 X12 消息(按协议名称)
从下拉列表中选择 X12 协议和要编码的 XML 消息：     
    ![提供必填字段](./media/logic-apps-enterprise-integration-x12-encode/x12encodeimage4.png) 

#### <a name="encode-x12-messages-by-identities"></a>编码 X12 消息(按标识)
输入 X12 协议中配置的发送方标识符、发送方限定符、接收方标识符和接收方限定符。  选择要编码的 XML 消息：
   
   ![提供必填字段](./media/logic-apps-enterprise-integration-x12-encode/x12encodeimage5.png) 

## <a name="x12-encode-details"></a>X12 编码详细信息

X12 编码连接器执行以下操作：

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
[了解有关 Enterprise Integration Pack 的详细信息](logic-apps-enterprise-integration-overview.md "了解 Enterprise Integration Pack") 




<!--HONumber=Jan17_HO5-->


