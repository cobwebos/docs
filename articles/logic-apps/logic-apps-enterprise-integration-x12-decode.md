---
title: "了解 Enterprise Integration Pack 解码 X12 消息连接器 | Microsoft Docs"
description: "了解如何将合作伙伴与 Enterprise Integration Pack 和逻辑应用一起使用"
services: logic-apps
documentationcenter: .net,nodejs,java
author: padmavc
manager: anneta
editor: 
ms.assetid: 4fd48d2d-2008-4080-b6a1-8ae183b48131
ms.service: logic-apps
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/15/2016
ms.author: padmavc
translationtype: Human Translation
ms.sourcegitcommit: dc8c9eac941f133bcb3a9807334075bfba15de46
ms.openlocfilehash: b6ce911b8435f3d6ed1c12c78ba81abe180346a2


---
# <a name="get-started-with-decode-x12-message"></a>解码 X12 消息入门
验证 EDI 和特定于合作伙伴的属性，为每个事务集生成 XML 文档并为处理的事务生成确认。

## <a name="create-the-connection"></a>创建连接
### <a name="prerequisites"></a>先决条件
* Azure 帐户；可以创建[免费帐户](https://azure.microsoft.com/free)
* 使用解码 X12 消息连接器需要集成帐户。 请参阅有关如何创建[集成帐户](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md)、[合作伙伴](../logic-apps/logic-apps-enterprise-integration-partners.md)和 [X12 协议](../logic-apps/logic-apps-enterprise-integration-x12.md)的详细信息

### <a name="connect-to-decode-x12-message-using-the-following-steps"></a>可使用以下步骤连接到解码 X12 消息：
1. [创建逻辑应用](../logic-apps/logic-apps-create-a-logic-app.md)提供了一个示例
2. 此连接器没有任何触发器。 使用其他触发器启动逻辑应用（如请求触发器）。  在逻辑应用设计器中，添加触发器和操作。  在下拉列表中选择“显示 Microsoft 托管的 API”，然后在搜索框中输入“x12”。  选择“X12 – 解码 X12 消息”
   
    ![搜索 x12](media/logic-apps-enterprise-integration-x12-decode/x12decodeimage1.png)  
3. 如果以前尚未创建任何与集成帐户的连接，则系统提示你输入连接详细信息
   
    ![集成帐户连接](media/logic-apps-enterprise-integration-x12-decode/x12decodeimage4.png)     
4. 输入集成帐户详细信息。  带星号的属性是必填的
   
   | 属性 | 详细信息 |
   | --- | --- |
   | 连接名称 * |为连接输入任何名称 |
   | 集成帐户 * |输入集成帐户名称。 确保集成帐户和逻辑应用处于相同 Azure 位置 |
   
    完成之后，连接详细信息与下面的内容类似
   
    ![集成帐户连接已创建](media/logic-apps-enterprise-integration-x12-decode/x12decodeimage5.png) 
5. 选择“创建”
6. 请注意，连接已创建。
   
    ![集成帐户连接详细信息](media/logic-apps-enterprise-integration-x12-decode/x12decodeimage6.png) 
7. 选择要解码的 X12 平面文件消息
   
    ![提供必填字段](media/logic-apps-enterprise-integration-x12-decode/x12decodeimage7.png) 

## <a name="x12-decode-does-following"></a>X12 解码执行以下操作
* 针对贸易合作伙伴协议验证信封
* 为每个事务集生成 XML 文档。
* 验证 EDI 和特定于合作伙伴的属性
  * EDI 结构验证和扩展架构验证
  * 交换信封结构的验证。
  * 针对控制架构进行的信封架构验证。
  * 针对消息架构进行的事务集数据元素架构验证。
  * 对事务集数据元素执行的 EDI 验证 
* 验证交换、组和事务集控制编号不重复
  * 针对以前收到的交换检查交换控制编号。
  * 针对交换中的其他组控制编号检查组控制编号。
  * 针对该组中的其他事务集控制编号检查事务集控制编号。
* 将整个交换转换为 XML 
  * 将交换拆分为事务集 - 出错时挂起事务集：将交换中的每个事务集分析为单独 XML 文档。 如果交换中的一个或多个事务集未能通过验证，则 X12 解码只挂起这些事务集。
  * 将交换拆分为事务集 - 出错时挂起交换：将交换中的每个事务集分析为单独 XML 文档。  如果交换中的一个或多个事务集未能通过验证，则 X12 解码会挂起整个交换。
  * 保留交换 - 出错时挂起事务集：为整个批处理交换创建 XML 文档。 X12 解码只挂起未能通过验证的事务集，同时继续处理所有其他事务集
  * 保留交换 - 出错时挂起交换：为整个批处理交换创建 XML 文档。 如果交换中的一个或多个事务集未能通过验证，则 X12 解码会挂起整个交换， 
* 生成技术和/或功能确认（如果已配置）。
  * 技术确认作为标头验证的结果生成。 技术确认报告地址接收方进行的交换标头和尾部处理的状态。
  * 功能确认作为正文验证的结果生成。 功能确认报告在处理收到的文档时遇到的每个错误

## <a name="next-steps"></a>后续步骤
[了解有关 Enterprise Integration Pack 的详细信息](../logic-apps/logic-apps-enterprise-integration-overview.md "了解 Enterprise Integration Pack") 




<!--HONumber=Jan17_HO3-->


