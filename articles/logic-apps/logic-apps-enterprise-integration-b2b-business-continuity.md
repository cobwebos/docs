---
title: "B2B 集成帐户的灾难恢复 - Azure 逻辑应用 | Microsoft Docs"
description: "逻辑应用 B2B 灾难恢复"
services: logic-apps
documentationcenter: .net,nodejs,java
author: padmavc
manager: anneta
editor: 
ms.assetid: cf44af18-1fe5-41d5-9e06-cc57a968207c
ms.service: logic-apps
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/10/2017
ms.author: LADocs; padmavc
ms.openlocfilehash: 4896d9da456bcc17b1a4d92259ef3d57f8575d8b
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/11/2017
---
# <a name="logic-apps-b2b-cross-region-disaster-recovery"></a>逻辑应用 B2B 跨区域灾难恢复

B2B 工作负荷涉及订单和发票等现金交易。 对于企业而言，在灾难事件中快速恢复以满足与合作伙伴达成一致意见的企业级 SLA，这非常重要。 本文演示为 B2B 工作负荷生成业务连续性计划。 

* 灾难恢复就绪性 
* 在发生灾难事件时故障转移到次要区域 
* 在发生灾难事件后，故障回复到主区域

## <a name="disaster-recovery-readiness"></a>灾难恢复就绪性  

1. 确定次要区域并在其中创建[集成帐户](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md)。

2. 为所需的消息流添加合作伙伴、架构和协议，在该消息流中，运行状态需要复制到次要区域集成帐户。

   > [!TIP]
   > 确保集成帐户项目命名约定在各个区域中保持一致。 

3. 为了从主要区域提取运行状态，请在次要区域中创建逻辑应用。 

   此逻辑应用应该具有一个触发器和一个操作。 
   触发器应连接到主要区域集成帐户，操作应连接到次要区域集成帐户。 
   基于时间间隔，触发器将轮询主要区域的运行状态表并提取新记录（如果有）。 操作会将这些新记录更新到次要区域集成帐户。 
   这有助于将主要区域的增量运行时状态获取到次要区域。

4. 根据 B2B 协议（X12、AS2 和 EDIFACT），逻辑应用集成帐户中的业务连续性旨在支持这种行为。 若要查找详细步骤，请选择各自的链接。

5. 建议也在次要区域中部署所有主要区域资源。 

   主要区域资源包括 Azure SQL 数据库或 Azure Cosmos DB、用于消息传送的 Azure 服务总线和 Azure 事件中心、Azure API 管理，以及 Azure 应用服务中的 Azure 逻辑应用功能。   

6. 在主要区域和次要区域之间建立连接。 为了从主要区域提取运行状态，请在次要区域中创建逻辑应用。 

   逻辑应用应该具有一个触发器和一个操作。 
   触发器应连接到主要区域集成帐户。 
   操作应连接到次要区域集成帐户。 
   基于时间间隔，触发器将轮询主要区域的运行状态表并提取新记录（如果有）。 
   操作会将这些新记录更新到次要区域集成帐户。 
   此过程有助于将主要区域的增量运行时状态获取到次要区域。

根据 B2B 协议（X12、AS2 和 EDIFACT），逻辑应用集成帐户中的业务连续性旨在提供这种行为。 有关使用 X12 和 AS2 的详细步骤，请参阅本文中的 [X12](../logic-apps/logic-apps-enterprise-integration-b2b-business-continuity.md#x12) 和 [AS2](../logic-apps/logic-apps-enterprise-integration-b2b-business-continuity.md#as2)。

## <a name="fail-over-to-a-secondary-region-during-a-disaster-event"></a>在发生灾难事件时故障转移到次要区域

在发生灾难事件时，如果主要区域不可用于业务连续性，则将流量定向到次要区域。 次要区域可帮助企业根据与其合作伙伴达成的的 RPO/RTO 快速恢复功能。 另外，也可以最大限度地减少将故障从一个区域转移到另一个区域的工作。 

将控制编号从主要区域复制到次要区域时，可能会像预期的那样出现延迟。 要避免在发生灾难事件时会将生成的重复控制编号发送给合作伙伴，建议使用 [PowerShell cmdlet](https://blogs.msdn.microsoft.com/david_burgs_blog/2017/03/09/fresh-of-the-press-new-azure-powershell-cmdlets-for-upcoming-x12-connector-disaster-recovery) 在次要区域协议中增加控制编号。

## <a name="fall-back-to-a-primary-region-post-disaster-event"></a>在发生灾难事件后，故障回复到主要区域

可以故障回复到主要区域时，请执行以下步骤：

1. 停止接收在次要区域中来自合作伙伴的消息。  

2. 使用 [PowerShell cmdlet](https://blogs.msdn.microsoft.com/david_burgs_blog/2017/03/09/fresh-of-the-press-new-azure-powershell-cmdlets-for-upcoming-x12-connector-disaster-recovery) 为所有的主要区域协议增加生成的控制编号。  

3. 将流量从次要区域定向到主要区域。

4. 检查是否已启用逻辑应用，该应用在次要区域中创建，用于提取主要区域的运行状态。

## <a name="x12"></a>X12 

根据控制编号，设计 EDI X12 文档的业务连续性：

> [!TIP]
> 还可使用 [X12 快速入门模板](https://azure.microsoft.com/documentation/templates/201-logic-app-x12-disaster-recovery-replication/)创建逻辑应用。 使用该模板的先决条件是创建主要和次要的集成帐户。 该模板有助于创建两个逻辑应用，一个用于接收的控制编号，另一个用于生成的控制编号。 各自的触发器和操作会在逻辑应用中创建，然后将触发器连接到主要集成帐户，将操作连接到次要集成帐户。

**先决条件**

若要启用入站消息的灾难恢复，请选择 X12 协议“接收设置”中的重复检查设置。

![选择重复检查设置](./media/logic-apps-enterprise-integration-b2b-business-continuity/dupcheck.png)  

1. 在次要区域中创建[逻辑应用](../logic-apps/logic-apps-create-a-logic-app.md)。    

2. 搜索“X12” ，然后选择“X12 - 当修改控制编号时” 。   

   ![搜索 X12](./media/logic-apps-enterprise-integration-b2b-business-continuity/x12cn1.png)

   触发器提示与集成帐户建立连接。 
   触发器应已连接到主要区域集成帐户。

3. 输入连接名称，从列表中选择“主要区域集成帐户”，并选择“创建”。   

   ![主要区域集成帐户名称](./media/logic-apps-enterprise-integration-b2b-business-continuity/x12cn2.png)

4. “开始同步控制编号的 DateTime”设置是可选的。 “频率”可以设置为“天”、“小时”、“分钟”或“秒”，中间要有间隔。   

   ![DateTime 和频率](./media/logic-apps-enterprise-integration-b2b-business-continuity/x12cn3.png)

5. 选择“新建步骤” > “添加操作”。

   ![“新建步骤”、“添加操作”](./media/logic-apps-enterprise-integration-b2b-business-continuity/x12cn4.png)

6. 搜索“X12”，然后选择“X12 - 添加或更新控制编号”。   

   ![添加或更新控制编号](./media/logic-apps-enterprise-integration-b2b-business-continuity/x12cn5.png)

7. 要将操作连接到次要区域集成帐户，请选择“更改连接” > “添加新连接”，显示可用集成帐户的列表。 输入连接名称，从列表中选择“次要区域集成帐户”，并选择“创建”。 

   ![次要区域集成帐户名称](./media/logic-apps-enterprise-integration-b2b-business-continuity/x12cn6.png)

8. 通过单击右上角的图标，切换到原始输入。

   ![切换到原始输入](./media/logic-apps-enterprise-integration-b2b-business-continuity/x12rawinputs.png)

9. 从动态内容选取器选择正文，并保存逻辑应用。

   ![“动态内容”字段](./media/logic-apps-enterprise-integration-b2b-business-continuity/x12cn7.png)

   基于时间间隔，触发器将轮询主要区域收到的控制编号表并提取新记录。 
   该操作会更新次要区域集成帐户中的记录。 
   如果没有更新，触发器状态显示为“已跳过”。   

   ![控制编号表](./media/logic-apps-enterprise-integration-b2b-business-continuity/x12recevicedcn8.png)

基于时间间隔，增量的运行时状态将从主要区域复制到次要区域。 在发生灾难事件时，如果主要区域不可用，则针对业务连续性将流量定向到次要区域。 

## <a name="edifact"></a>EDIFACT 

EDI EDIFACT 文档的业务连续性基于控制编号。

**先决条件**

若要启用入站消息的灾难恢复，请选择 EDIFACT 协议接受设置中的重复检查设置。

![选择重复检查设置](./media/logic-apps-enterprise-integration-b2b-business-continuity/edifactdupcheck.png)  

1. 在次要区域中创建[逻辑应用](../logic-apps/logic-apps-create-a-logic-app.md)。    

2. 搜索“EDIFACT” ，然后选择“EDIFACT - 当修改控制编号时”。

   ![搜索 EDIFACT](./media/logic-apps-enterprise-integration-b2b-business-continuity/edifactcn1.png)

   触发器提示与集成帐户建立连接。 
   触发器应已连接到主要区域集成帐户。 

3. 输入连接名称，从列表中选择“主要区域集成帐户”，并选择“创建”。    

   ![主要区域集成帐户名称](./media/logic-apps-enterprise-integration-b2b-business-continuity/X12CN2.png)

4. “开始同步控制编号的 DateTime”设置是可选的。 “频率”可以设置为“天”、“小时”、“分钟”或“秒”，中间要有间隔。    

   ![DateTime 和频率](./media/logic-apps-enterprise-integration-b2b-business-continuity/x12cn3.png)

6. 选择“新建步骤” > “添加操作”。    

   ![“新建步骤”、“添加操作”](./media/logic-apps-enterprise-integration-b2b-business-continuity/x12cn4.png)

7. 搜索“EDIFACT”，然后选择“EDIFACT - 添加或更新控制编号”。   

   ![添加或更新控制编号](./media/logic-apps-enterprise-integration-b2b-business-continuity/EdifactChooseAction.png)

8. 要将操作连接到次要区域集成帐户，请选择“更改连接” > “添加新连接”，显示可用集成帐户的列表。 输入连接名称，从列表中选择“次要区域集成帐户”，并选择“创建”。

   ![次要区域集成帐户名称](./media/logic-apps-enterprise-integration-b2b-business-continuity/x12cn6.png)

9. 通过单击右上角的图标，切换到原始输入。

   ![切换到原始输入](./media/logic-apps-enterprise-integration-b2b-business-continuity/Edifactrawinputs.png)

10. 从动态内容选取器选择正文，并保存逻辑应用。   

   ![“动态内容”字段](./media/logic-apps-enterprise-integration-b2b-business-continuity/X12CN7.png)

   基于时间间隔，触发器将轮询主要区域收到的控制编号表并提取新记录。
   操作会将这些记录更新到次要区域集成帐户中。 
   如果没有更新，触发器状态显示为“已跳过”。

   ![控制编号表](./media/logic-apps-enterprise-integration-b2b-business-continuity/x12recevicedcn8.png)

基于时间间隔，增量的运行时状态将从主要区域复制到次要区域。 在发生灾难事件时，如果主要区域不可用，则针对业务连续性将流量定向到次要区域。 

## <a name="as2"></a>AS2 

根据消息 ID 和 MIC 值，设计针对使用 AS2 协议的文档的业务连续性。

> [!TIP]
> 还可使用 [AS2 快速入门模板](https://github.com/Azure/azure-quickstart-templates/pull/3302)创建逻辑应用。 使用该模板的先决条件是创建主要和次要的集成帐户。 该模板有助于创建具有一个触发器和一个操作的逻辑应用。 逻辑应用在主要集成帐户的触发器和次要集成帐户的操作之间建立连接。

1. 在次要区域中创建[逻辑应用](../logic-apps/logic-apps-create-a-logic-app.md)。  

2. 搜索“AS2”，并选择“AS2 - 当创建 MIC 值时”。   

   ![搜索 AS2](./media/logic-apps-enterprise-integration-b2b-business-continuity/as2messageid1.png)

   触发器提示与集成帐户建立连接。 
   触发器应已连接到主要区域集成帐户。 
   
3. 输入连接名称，从列表中选择“主要区域集成帐户”，并选择“创建”。

   ![主要区域集成帐户名称](./media/logic-apps-enterprise-integration-b2b-business-continuity/as2messageid2.png)

4. “开始同步 MIC 值的 DateTime”设置是可选的。 “频率”可以设置为“天”、“小时”、“分钟”或“秒”，中间要有间隔。   

   ![DateTime 和频率](./media/logic-apps-enterprise-integration-b2b-business-continuity/as2messageid3.png)

5. 选择“新建步骤” > “添加操作”。  

   ![“新建步骤”、“添加操作”](./media/logic-apps-enterprise-integration-b2b-business-continuity/as2messageid4.png)

6. 搜索“AS2”，并选择“AS2 - 添加或更新 MIC 内容”。  

   ![MIC 添加或更新](./media/logic-apps-enterprise-integration-b2b-business-continuity/as2messageid5.png)

7. 要将操作连接到次要区域集成帐户，请选择“更改连接” > “添加新连接”，显示可用集成帐户的列表。 输入连接名称，从列表中选择“次要区域集成帐户”，并选择“创建”。

   ![次要区域集成帐户名称](./media/logic-apps-enterprise-integration-b2b-business-continuity/as2messageid6.png)

8. 通过单击右上角的图标，切换到原始输入。

   ![切换到原始输入](./media/logic-apps-enterprise-integration-b2b-business-continuity/as2rawinputs.png)

9. 从动态内容选取器选择正文，并保存逻辑应用。   

   ![动态内容](./media/logic-apps-enterprise-integration-b2b-business-continuity/as2messageid7.png)

   基于时间间隔，触发器将轮询主要区域表并提取新记录。 操作会将这些新记录更新到次要区域集成帐户。 
   如果没有更新，触发器状态显示为“已跳过”。  

   ![主要区域表](./media/logic-apps-enterprise-integration-b2b-business-continuity/as2messageid8.png)

基于时间间隔，增量的运行时状态将从主要区域复制到次要区域。 在发生灾难事件时，如果主要区域不可用，则针对业务连续性将流量定向到次要区域。 

## <a name="next-steps"></a>后续步骤

[监视 B2B 消息](logic-apps-monitor-b2b-message.md)

