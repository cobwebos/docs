---
title: "了解 Enterprise Integration Pack 解码 EDIFACT 消息连接器 | Microsoft Docs"
description: "了解如何将合作伙伴与 Enterprise Integration Pack 和逻辑应用一起使用"
services: logic-apps
documentationcenter: .net,nodejs,java
author: padmavc
manager: erikre
editor: 
ms.assetid: 0e61501d-21a2-4419-8c6c-88724d346e81
ms.service: logic-apps
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/15/2016
ms.author: padmavc
translationtype: Human Translation
ms.sourcegitcommit: 219dcbfdca145bedb570eb9ef747ee00cc0342eb
ms.openlocfilehash: 0c09660c805615364b164b5410508e49b0d9ad6f


---
# <a name="get-started-with-decode-edifact-message"></a>解码 EDIFACT 消息入门
验证 EDI 和特定于合作伙伴的属性，为每个事务集生成 XML 文档并为处理的事务生成确认。

## <a name="create-the-connection"></a>创建连接
### <a name="prerequisites"></a>先决条件
* Azure 帐户；可以创建[免费帐户](https://azure.microsoft.com/free)
* 使用解码 EDIFACT 消息连接器需要集成帐户。 请参阅有关如何创建[集成帐户](app-service-logic-enterprise-integration-create-integration-account.md)、[合作伙伴](app-service-logic-enterprise-integration-partners.md)和 [EDIFACT 协议](app-service-logic-enterprise-integration-edifact.md)的详细信息

### <a name="connect-to-decode-edifact-message-using-the-following-steps"></a>可使用以下步骤连接到解码 EDIFACT 消息：
1. [创建逻辑应用](app-service-logic-create-a-logic-app.md)提供了一个示例。
2. 此连接器没有任何触发器。 使用其他触发器启动逻辑应用（如请求触发器）。  在逻辑应用设计器中，添加触发器和操作。  在下拉列表中选择“显示 Microsoft 托管的 API”，然后在搜索框中输入“EDIFACT”。  选择“解码 EDIFACT 消息”
   
    ![搜索 EDIFACT](./media/app-service-logic-enterprise-integration-edifactorconnector/edifactdecodeimage1.png)
3. 如果以前尚未创建任何与集成帐户的连接，则系统提示你输入连接详细信息
   
    ![创建集成帐户](./media/app-service-logic-enterprise-integration-edifactorconnector/edifactdecodeimage2.png)  
4. 输入集成帐户详细信息。  带星号的属性是必填的
   
   | 属性 | 详细信息 |
   | --- | --- |
   | 连接名称 * |为连接输入任何名称 |
   | 集成帐户 * |输入集成帐户名称。 确保集成帐户和逻辑应用处于相同 Azure 位置 |
   
    完成之后，连接详细信息与下面的内容类似
   
    ![集成帐户已创建](./media/app-service-logic-enterprise-integration-edifactorconnector/edifactdecodeimage3.png)  
5. 选择“创建”
6. 请注意，连接已创建
   
    ![集成帐户连接详细信息](./media/app-service-logic-enterprise-integration-edifactorconnector/edifactdecodeimage5.png)  
7. 选择要解码的 EDIFACT 平面文件消息
   
    ![提供必填字段](./media/app-service-logic-enterprise-integration-edifactorconnector/edifactdecodeimage5.png)  

## <a name="edifact-decode-does-following"></a>EDIFACT 解码执行以下操作
* 通过将发送方限定符和标识符与接收方限定符和标识符进行匹配来解析协议
* 将单个消息中的多个交换拆分为单独对象。
* 针对贸易合作伙伴协议验证信封
* 分解交换。
* 验证 EDI 和特定于合作伙伴的属性包括
  * 交换信封结构的验证。
  * 针对控制架构进行的信封架构验证。
  * 针对消息架构进行的事务集数据元素架构验证。
  * 对事务集数据元素执行的 EDI 验证
* 验证交换、组和事务集控制编号不重复（如果已配置） 
  * 针对以前收到的交换检查交换控制编号。 
  * 针对交换中的其他组控制编号检查组控制编号。 
  * 针对该组中的其他事务集控制编号检查事务集控制编号。
* 为每个事务集生成 XML 文档。
* 将整个交换转换为 XML 
  * 将交换拆分为事务集 - 出错时挂起事务集：将交换中的每个事务集分析为单独 XML 文档。 如果交换中的一个或多个事务集未能通过验证，则 EDIFACT 解码只挂起这些事务集。 
  * 将交换拆分为事务集 - 出错时挂起交换：将交换中的每个事务集分析为单独 XML 文档。  如果交换中的一个或多个事务集未能通过验证，则 EDIFACT 解码会挂起整个交换。
  * 保留交换 - 出错时挂起事务集：为整个批处理交换创建 XML 文档。 EDIFACT 解码只挂起未能通过验证的事务集，同时继续处理所有其他事务集
  * 保留交换 - 出错时挂起交换：为整个批处理交换创建 XML 文档。 如果交换中的一个或多个事务集未能通过验证，则 EDIFACT 解码会挂起整个交换， 
* 生成技术（控制）和/或功能确认（如果已配置）。
  * 技术确认或 CONTRL ACK 报告收到的完整交换的语法检查结果。
  * 功能确认会确认接受或拒绝收到的交换或组

## <a name="next-steps"></a>后续步骤
[了解有关 Enterprise Integration Pack 的详细信息](app-service-logic-enterprise-integration-overview.md "了解 Enterprise Integration Pack") 




<!--HONumber=Nov16_HO3-->


