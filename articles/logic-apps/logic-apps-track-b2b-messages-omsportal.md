---
title: "在 Operations Management Suite 门户中跟踪 B2B 消息 - Azure | Microsoft Docs"
description: "如何在 Operations Management Suite 门户中跟踪 B2B 消息"
author: padmavc
manager: anneta
editor: 
services: logic-apps
documentationcenter: 
ms.assetid: bb7d9432-b697-44db-aa88-bd16ddfad23f
ms.service: logic-apps
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/13/2016
ms.author: LADocs; padmavc
ms.translationtype: Human Translation
ms.sourcegitcommit: 74f34bdbf5707510c682814716aa0b95c19a5503
ms.openlocfilehash: d1c61ba220b4334f053428a23e620e8004fc60f7
ms.contentlocale: zh-cn
ms.lasthandoff: 06/09/2017


---
# <a name="track-b2b-messages-in-the-operations-management-suite-portal"></a>在 Operations Management Suite 门户中跟踪 B2B 消息

![日志应用 B2B 符号](./media/logic-apps-track-b2b-messages-omsportal/logic-apps-b2b-symbol.png)

B2B 通信涉及到两个正在运行的业务流程或应用程序之间的消息交换。 在 Operations Management Suite 门户中使用以下基于 Web 的跟踪功能，以确认消息是否已正确地得到了处理：

* 消息计数和状态
* 确认状态
* 已确认的关联消息
* 有关故障的详细错误说明
* 搜索功能

## <a name="prerequisites"></a>先决条件
* 一个 Azure 帐户。 可以创建[免费帐户](https://azure.microsoft.com/free)。
* 一个集成帐户。 可以创建[集成帐户](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md)并设置日志记录。 若要设置日志记录，请参阅[监视 B2B 消息](logic-apps-monitor-b2b-message.md)。
* 一个逻辑应用。 可以创建[逻辑应用](../logic-apps/logic-apps-create-a-logic-app.md)并设置日志记录。 若要设置日志记录，请参阅 [Azure 诊断和警报](../logic-apps/logic-apps-monitor-your-logic-apps.md#azure-diagnostics-and-alerts)。

## <a name="add-logic-apps-b2b-solution-to-the-operations-management-suite-portal"></a>将逻辑应用 B2B 解决方案添加到 Operations Management Suite 门户中

1. 在 Azure 门户中，选择“更多服务”，搜索“Log Analytics”，然后选择“Log Analytics”。   
![搜索 Log Analytics](media/logic-apps-track-b2b-messages-omsportal/browseloganalytics.png)  

2. 选择“Log Analytics”。  
![选择 Log Analytics](media/logic-apps-track-b2b-messages-omsportal/selectla.png)

3. 选择“OMS 门户”。 此时将显示 Operations Management Suite 门户主页。   
![浏览 Operations Management Suite 门户](media/logic-apps-track-b2b-messages-omsportal/omsportalpage.png)

4. 选择“解决方案库”。    
![选择解决方案库](media/logic-apps-track-b2b-messages-omsportal/omshomepage1.png)

5. 选择“逻辑应用 B2B”。     
![选择逻辑应用 B2B](media/logic-apps-track-b2b-messages-omsportal/omshomepage2.png)

6. 选择“添加”以将“逻辑应用 B2B”消息添加到主页。  
![选择添加](media/logic-apps-track-b2b-messages-omsportal/omshomepage3.png)

7. 主页上会显示“逻辑应用 B2B消息”。   
![选择主页](media/logic-apps-track-b2b-messages-omsportal/omshomepage4.png)

## <a name="track-data-in-the-operations-management-suite-portal"></a>在 Operations Management Suite 门户中跟踪数据

1. 在处理消息后，将显示更新的消息计数。   
![更新的消息](media/logic-apps-track-b2b-messages-omsportal/omshomepage6.png)

2. 在主页上选择“逻辑应用 B2B 消息”以查看 AS2 和 X12 消息状态。  数据以一天为基础。
![选择逻辑应用 B2B 消息](media/logic-apps-track-b2b-messages-omsportal/omshomepage5.png)

3. 按状态选择 AS2、X12 或 EDIFACT 消息以转到消息列表。 下一张屏幕快照显示了 AS2 消息状态。 AS2 和 X12 消息状态属性说明稍后将显示在“消息列表属性说明”中。  
![选择 AS2 消息状态](media/logic-apps-track-b2b-messages-omsportal/as2messagelist.png)

4. 选择 AS2、X12 或 EDIFACT 消息列表中的某行以转到日志搜索。  日志搜索将列出具有相同运行 ID 的所有操作。
![选择消息状态](media/logic-apps-track-b2b-messages-omsportal/logsearch.png)

## <a name="message-list-property-descriptions"></a>消息列表属性说明

#### <a name="as2-message-list-property-descriptions"></a>AS2 消息列表属性说明

| 属性 | 说明 |
| --- | --- |
| 发送方 | 在接收设置中配置的来宾合作伙伴，或在 AS2 协议的发送设置中配置的主机合作伙伴。 |
| 接收方 | 在接收设置中配置的主机合作伙伴，或在 AS2 协议的发送设置中配置的来宾合作伙伴。 |
| 逻辑应用 | 其中配置了 AS2 操作的逻辑应用。 |
| 状态 | AS2 消息状态 <br>成功 = 接收或发送了正确的 AS2 消息，未配置 MDN <br>成功 = 接收或发送了正确的 AS2 消息，MDN 已配置和接收，或者 MDN 已发送 <br>失败 = 接收了错误的 AS2 消息，未配置 MDN <br>挂起 = 接收或发送了正确的 AS2 消息，MDN 已配置，预计会收到 MDN |
| Ack | MDN 消息状态 <br>已接受 = 接收或发送了正面的 MDN <br>挂起 = 等待接收或发送 MDN <br>已拒绝 = 接收或发送了负面的 MDN <br>不需要 = 协议中未配置 MDN |
| Direction | AS2 消息传送方向。 |
| 相关性 ID | 用于关联逻辑应用中所有触发器和操作的 ID。 |
| 消息 ID |  AS2 消息 ID，来自 AS2 消息的标头。 |
| Timestamp | AS2 操作处理消息的时间。 |

#### <a name="x12-message-list-property-descriptions"></a>X12 消息列表属性说明

| 属性 | 说明 |
| --- | --- |
| 发送方 | 在接收设置中配置的来宾合作伙伴，或在 X12 协议的发送设置中配置的主机合作伙伴。 |
| 接收方 | 在接收设置中配置的主机合作伙伴，或在 X12 协议的发送设置中配置的来宾合作伙伴。 |
| 逻辑应用 | 其中配置了 AS2 操作的逻辑应用。 |
| 状态 | X12 消息状态 <br>成功 = 接收或发送了正确的 X12 消息，未配置功能确认 <br>成功 = 接收或发送了正确的 X12 消息，已配置和接收功能确认，或者已发送功能确认 <br>失败 = 接收或发送了错误的 X12 消息 <br>挂起 = 接收或发送了正确的 X12 消息，已配置功能确认，预计可以进行功能确认。 |
| Ack | 功能确认 (997) 状态 <br>已接受 = 接收或发送了正面的功能确认 <br>已拒绝 = 接收或发送了负面的功能确认 <br>挂起 = 预计会有功能确认，但未收到 <br>挂起 = 生成了功能确认，但无法将其发送给合作伙伴 <br>不需要 = 未配置功能确认 |
| Direction | X12 消息传送方向。 |
| 相关性 ID | 用于关联逻辑应用中所有触发器和操作的 ID。 |
| 消息类型 |  EDI X12 消息类型。 |
| ICN | X12 消息的交换控制编号。 |
| TSCN | X12 消息的事务集控制编号。 |
| Timestamp | X12 操作处理消息的时间。 |


#### <a name="edifact-message-list-property-descriptions"></a>EDIFACT 消息列表属性说明

| 属性 | 说明 |
| --- | --- |
| 发送方 | 在接收设置中配置的来宾合作伙伴，或在 EDIFACT 协议的发送设置中配置的主机合作伙伴。 |
| 接收方 | 在接收设置中配置的主机合作伙伴，或在 EDIFACT 协议的发送设置中配置的来宾合作伙伴。 |
| 逻辑应用 | 其中配置了 AS2 操作的逻辑应用。 |
| 状态 | EDIFACT 消息状态 <br>成功 = 接收或发送了正确的 X12 消息，未配置功能确认 <br>成功 = 接收或发送了正确的 X12 消息，已配置和接收功能确认，或者已发送功能确认 <br>失败 = 接收或发送了错误的 X12 消息 <br>挂起 = 接收或发送了正确的 X12 消息，已配置功能确认，预计可以进行功能确认。 |
| Ack | 功能确认 (997) 状态 <br>已接受 = 接收或发送了正面的功能确认 <br>已拒绝 = 接收或发送了负面的功能确认 <br>挂起 = 预计会有功能确认，但未收到 <br>挂起 = 生成了功能确认，但无法将其发送给合作伙伴 <br>不需要 = 未配置功能确认 |
| 方向 | EDIFACT 消息方向。 |
| 相关性 ID | 用于关联逻辑应用中所有触发器和操作的 ID。 |
| 消息类型 |  EDIFACT 消息类型。 |
| ICN | EDIFACT 消息的交换控制编号。 |
| TSCN | EDIFACT 消息的事务集控制编号。 |
| Timestamp | EDIFACT 操作处理消息的时间。 |


## <a name="queries-in-the-operations-management-suite-portal"></a>Operations Management Suite 门户中的查询

可在“搜索”页上创建查询。 搜索时，可使用 Facet 控件筛选结果。

### <a name="create-a-query"></a>创建查询

1. 在日志搜索中，编写查询并选择“保存”。 将显示“保存搜索”。 若要编写查询，请参阅[使用查询在 Operations Management Suite 门户中跟踪 B2B 消息](logic-apps-track-b2b-messages-omsportal-query-filter-control-number.md)。
![选择主页](media/logic-apps-track-b2b-messages-omsportal/logsearchaddquery.png)

2. 在“保存搜索”中，添加“名称”和“类别”，然后选择“保存”。   
![选择主页](media/logic-apps-track-b2b-messages-omsportal/logsearchaddquery1.png)

3. 若要查看查询，请选择“收藏夹”。    
![选择主页](media/logic-apps-track-b2b-messages-omsportal/logsearchaddquery3.png)

    ![选择主页](media/logic-apps-track-b2b-messages-omsportal/logsearchaddquery4.png)

### <a name="use-a-saved-query"></a>使用已保存的查询

* 在日志搜索中，选择“收藏夹”，查看已保存的查询。  若要查看查询结果，请选择一个查询。
![选择主页](media/logic-apps-track-b2b-messages-omsportal/logsearchaddquery5.png)


## <a name="next-steps"></a>后续步骤
[自定义跟踪架构](logic-apps-track-integration-account-custom-tracking-schema.md "Learn about Custom Tracking Schema")   
[AS2 跟踪架构](logic-apps-track-integration-account-as2-tracking-schemas.md "Learn about AS2 Tracking Schema")    
[X12 跟踪架构](logic-apps-track-integration-account-x12-tracking-schema.md "Learn about X12 Tracking Schema")  
[了解有关 Enterprise Integration Pack 的详细信息](../logic-apps/logic-apps-enterprise-integration-overview.md "Learn about Enterprise Integration Pack")

