---
title: "逻辑应用 B2B 集成帐户灾难恢复 - Azure 逻辑应用 | Microsoft Docs"
description: "Azure 逻辑应用 B2B 灾难恢复"
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
ms.author: padmavc
translationtype: Human Translation
ms.sourcegitcommit: 7f469fb309f92b86dbf289d3a0462ba9042af48a
ms.openlocfilehash: 0ebaa553f360cc7deb52139fe9adaaca39a2ee23
ms.lasthandoff: 04/13/2017


---

# <a name="logic-apps-b2b-cross-region-disaster-recovery"></a>跨区域灾难恢复的逻辑应用 B2B
B2B 工作负荷涉及订单、发票等现金交易。  对于企业而言，在灾难事件中快速恢复以满足与合作伙伴达成一致意见的企业级 SLA，这非常重要。  本主题演示为 B2B 工作负荷生成业务连续性计划。  包含的内容

* 在次要区域中创建集成帐户
* 在主要区域和次要区域之间建立连接 
* 在发生灾难事件时故障转移到次要区域
* 在发生灾难事件后，故障回复到主要区域

## <a name="create-an-integration-account-in-secondary-region"></a>在次要区域中创建集成帐户
1. 选择次要区域并创建[集成帐户](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md)。  

2. 为所需的消息流添加合作伙伴、架构和协议，在该消息流中，运行状态需要复制到次要区域集成帐户。

    > [!Tip]
    > 确保集成帐户项目命名约定在各个区域中保持一致 
    > 
    > 

3. 建议的方法是在次要区域中也部署所有主要区域资源（例如 SQL Azure 或 DocumentDB 数据库、用于传递消息的服务总线 / 事件中心、APIM、逻辑应用）。  

## <a name="establish-a-connection-from-primary-to-secondary"></a>在主要区域和次要区域之间建立连接 
为了从主要区域提取运行状态，请在次要区域中创建逻辑应用。  它应该具有一个**触发器**和一个**操作**。  触发器应连接到主要区域集成帐户，操作应连接到次要区域集成帐户。  基于时间间隔，触发器将轮询主要区域的运行状态表并提取新记录（如果有），操作会将这些新记录更新到次要区域集成帐户。 这有助于将主要区域的增量运行时状态获取到次要区域。

根据 B2B 协议（X12、AS2 和 EDIFACT），逻辑应用集成帐户中的业务连续性旨在支持这种行为。  若要查找详细步骤，请选择各自的链接。

* [X12](../logic-apps/logic-apps-enterprise-integration-b2b-business-continuity.md#x12)
* [AS2](../logic-apps/logic-apps-enterprise-integration-b2b-business-continuity.md#as2)
* EDIFACT（即将推出）

## <a name="fail-over-to-secondary-region-during-a-disaster-event"></a>在发生灾难事件时故障转移到次要区域
在发生灾难事件时，如果主要区域不可用于业务连续性，则将流量定向到次要区域。 次要区域有助于快速恢复业务功能，以满足与合作伙伴达成一致意见的恢复时间/点目标 (RPO/RTO)。  另外，也可以最大限度地减少将故障从一个区域转移到另一个区域的工作。 

将控制编号从主要区域复制到次要区域时，可能会像预期的那样出现延迟。  若要避免在发生灾难事件时将生成的重复控制编号发送给合作伙伴，建议使用 [PowerShell cmdlet](https://blogs.msdn.microsoft.com/david_burgs_blog/2017/03/09/fresh-of-the-press-new-azure-powershell-cmdlets-for-upcoming-x12-connector-disaster-recovery) 在**次要区域协议**中增加控制编号。

## <a name="fall-back-to-primary-region-post-disaster-event"></a>在发生灾难事件后，故障回复到主要区域
当主要区域可用时，遵循以下步骤故障回复到主要区域     
* 停止接收在**次要区域**中来自合作伙伴的消息   
* 使用 [PowerShell cmdlet](https://blogs.msdn.microsoft.com/david_burgs_blog/2017/03/09/fresh-of-the-press-new-azure-powershell-cmdlets-for-upcoming-x12-connector-disaster-recovery) 为所有的**主要区域协议**增加生成的控制编号   
* 将流量从次要区域定向到主要区域   
* 检查是否已启用逻辑应用，该应用在次要区域中创建，用于提取主要区域的运行状态    

## <a name="x12"></a>X12 
根据控制编号，设计针对 EDI X12 文档的业务连续性   
* 从合作伙伴接收的控制编号（入站消息）  
* 生成的控制编号（出站消息），发送给合作伙伴  
    
    > [!Tip]
    > 还可使用 [X12 快速入门模板](https://azure.microsoft.com/documentation/templates/201-logic-app-x12-disaster-recovery-replication/)创建逻辑应用。  使用该模板的先决条件是创建主要和次要的集成帐户。  该模板有助于创建 2 个逻辑应用，一个用于接收的控制编号，另一个用于生成的控制编号。  各自的触发器和操作将在逻辑应用中创建，然后将触发器连接到主要集成帐户，将操作连接到次要集成帐户。
    > 
    >

### <a name="control-numbers-received-from-the-partners"></a>从合作伙伴接收的控制编号
1. 在次要区域中创建[逻辑应用](../logic-apps/logic-apps-create-a-logic-app.md)   

2. 搜索“X12”，然后选择“X12 - 当修改接收的控制编号时”    
![](./media/logic-apps-enterprise-integration-b2b-business-continuity/X12recevicedCN1.png)

3. 选择触发器提示，以与集成帐户建立连接。 连接到主要区域集成帐户的触发器。  提供连接名称，从列表中选择“主要区域集成帐户”，然后单击“创建”。   
![](./media/logic-apps-enterprise-integration-b2b-business-continuity/X12recevicedCN2.png)

4. 开始同步控制编号的 DateTime 为可选项。  频率可以设置为“天”、“小时”、“分钟”或“秒”，中间要有间隔。    
![](./media/logic-apps-enterprise-integration-b2b-business-continuity/X12recevicedCN3.png)

5. 依次单击“新建步骤”和“添加操作”    
![](./media/logic-apps-enterprise-integration-b2b-business-continuity/X12recevicedCN4.png)

6. 搜索“X12”，然后选择“X12 - 添加或更新接收的控制编号”   
![](./media/logic-apps-enterprise-integration-b2b-business-continuity/X12recevicedCN5.png)

7. 连接到次要集成帐户的操作。  选择“更改连接”，“添加新连接”将列出可用的集成帐户。  提供连接名称，从列表中选择“次要区域集成帐户”，然后单击“创建”。     
![](./media/logic-apps-enterprise-integration-b2b-business-continuity/X12recevicedCN6.png)

8. 选择动态内容，并保存逻辑应用   
![](./media/logic-apps-enterprise-integration-b2b-business-continuity/X12recevicedCN7.png)

9. 基于时间间隔，触发器将轮询主要区域的接收控制编号表并提取新记录，操作会将这些新记录更新到次要区域集成帐户。  如果没有更新，触发器状态显示为已跳过。
    
    > [!Tip]
    > 启用对协议接收设置的重复检查将保留接收的控制编号运行时状态，并有助于定期引发触发器。
    > 
    >

![](./media/logic-apps-enterprise-integration-b2b-business-continuity/X12recevicedCN8.png)


### <a name="control-numbers-generated-and-send-to-the-partners"></a>生成的控制编号，发送给合作伙伴
1. 在次要区域中创建[逻辑应用](../logic-apps/logic-apps-create-a-logic-app.md)  

2. 搜索“X12”，然后选择“X12 - 当修改生成的控制编号时”   
![](./media/logic-apps-enterprise-integration-b2b-business-continuity/X12generatedCN1.png)

3. 选择触发器提示，以与集成帐户建立连接。 连接到主要区域集成帐户的触发器。  提供连接名称，从列表中选择“主要区域集成帐户”，然后单击“创建”。   
![](./media/logic-apps-enterprise-integration-b2b-business-continuity/X12generatedCN2.png) 

4. 开始同步控制编号的 DateTime 为可选项。  频率可以设置为“天”、“小时”、“分钟”或“秒”，中间要有间隔。   
![](./media/logic-apps-enterprise-integration-b2b-business-continuity/X12generatedCN3.png)  

5. 依次单击“新建步骤”和“添加操作”   
![](./media/logic-apps-enterprise-integration-b2b-business-continuity/X12generatedCN4.png)

6. 搜索“X12”，然后选择“X12 - 添加或更新生成的控制编号”   
![](./media/logic-apps-enterprise-integration-b2b-business-continuity/X12generatedCN5.png)

7. 连接到次要集成帐户的操作。  选择“更改连接”，“添加新连接”将列出可用的集成帐户。  提供连接名称，从列表中选择“次要区域集成帐户”，然后单击“创建”。    
![](./media/logic-apps-enterprise-integration-b2b-business-continuity/X12generatedCN6.png)

8. 选择动态内容，并保存逻辑应用   
![](./media/logic-apps-enterprise-integration-b2b-business-continuity/X12generatedCN7.png)

9. 基于时间间隔，触发器将轮询主要区域的接收控制编号表并提取新记录，操作会将这些新记录更新到次要区域集成帐户。  如果没有更新，触发器状态显示为已跳过。  
![](./media/logic-apps-enterprise-integration-b2b-business-continuity/X12generatedCN8.png)

基于时间间隔，增量的运行时状态将从主要区域复制到次要区域。  在发生灾难事件时，如果主要区域不可用，则针对业务连续性将流量定向到次要区域。 

## <a name="as2"></a>AS2 
根据消息 ID 和 MIC 值，设计针对使用 AS2 协议的文档的业务连续性

> [!Tip]
    > 还可使用 [AS2 快速入门模板](https://github.com/Azure/azure-quickstart-templates/pull/3302)创建逻辑应用。  使用该模板的先决条件是创建主要和次要的集成帐户。  该模板有助于创建具有一个触发器和一个操作的逻辑应用。  在主要集成帐户的触发器和次要集成帐户的操作之间建立连接。
    > 
    >

1. 在次要区域中创建[逻辑应用](../logic-apps/logic-apps-create-a-logic-app.md)  

2. 搜索“AS2”，然后选择“AS2 - 当创建 MIC 值时”   
![](./media/logic-apps-enterprise-integration-b2b-business-continuity/AS2messageid1.png)

3. 选择触发器提示，以与集成帐户建立连接。 连接到主要区域集成帐户的触发器。  提供连接名称，从列表中选择“主要区域集成帐户”，然后单击“创建”。   
![](./media/logic-apps-enterprise-integration-b2b-business-continuity/AS2messageid2.png)

4. 开始同步 MIC 值的 DateTime 为可选项。  频率可以设置为“天”、“小时”、“分钟”或“秒”，中间要有间隔。   
![](./media/logic-apps-enterprise-integration-b2b-business-continuity/AS2messageid3.png)

5. 依次单击“新建步骤”和“添加操作”   
![](./media/logic-apps-enterprise-integration-b2b-business-continuity/AS2messageid4.png)

6. 搜索“AS2”，然后选择“AS2 - 添加或更新 MIC”   
![](./media/logic-apps-enterprise-integration-b2b-business-continuity/AS2messageid5.png)

7. 连接到次要集成帐户的操作。  选择“更改连接”，“添加新连接”将列出可用的集成帐户。  提供连接名称，从列表中选择“次要区域集成帐户”，然后单击“创建”。    
![](./media/logic-apps-enterprise-integration-b2b-business-continuity/AS2messageid6.png)

8. 选择动态内容，并保存逻辑应用   
![](./media/logic-apps-enterprise-integration-b2b-business-continuity/AS2messageid7.png)

9. 基于时间间隔，触发器将轮询主要区域表并提取新记录，操作会将这些新记录更新到次要区域集成帐户。  如果没有更新，触发器状态显示为已跳过。  
![](./media/logic-apps-enterprise-integration-b2b-business-continuity/AS2messageid8.png)

基于时间间隔，增量的运行时状态将从主要区域复制到次要区域。  在发生灾难事件时，如果主要区域不可用，则针对业务连续性将流量定向到次要区域。 

## <a name="next-steps"></a>后续步骤
* 了解有关[监视 B2B 消息](logic-apps-monitor-b2b-message.md)的详细信息。   
