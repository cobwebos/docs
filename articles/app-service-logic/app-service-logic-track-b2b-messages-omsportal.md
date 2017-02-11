---
title: "在 OMS 门户中跟踪 B2B 消息 | Microsoft Docs"
description: "如何在 OMS 门户中跟踪 B2B 消息"
author: padmavc
manager: erikre
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
ms.author: padmavc
translationtype: Human Translation
ms.sourcegitcommit: b3f564e32105708ddbd9027240c897fdd8ae2ac6
ms.openlocfilehash: 673b128c628a13c4af6c73c1a2d82953aadfd45a


---
# <a name="tracking-b2b-messages-in-oms-portal"></a>在 OMS 门户中跟踪 B2B 消息
B2B 通信涉及到两个正在运行的业务流程或应用程序之间的消息交换。 在 OMS 门户中跟踪 B2B 消息，可获得丰富的基于 Web 的跟踪功能，能够查看消息是否已正确处理。  可跟踪

* 计数和消息状态
* 确认状态
* 已确认的关联消息
* 有关故障的详细错误说明
* 搜索功能

## <a name="prerequisites"></a>先决条件
* Azure 帐户；可以创建[免费帐户](https://azure.microsoft.com/free)
* 集成帐户；可创建[集成帐户](app-service-logic-enterprise-integration-create-integration-account.md)并启用日志记录；可在[此处](app-service-logic-monitor-b2b-message.md)找到步骤
* 逻辑应用；可创建[逻辑应用](app-service-logic-create-a-logic-app.md)并启用日志记录；可在[此处](app-service-logic-monitor-your-logic-apps.md)找到步骤

## <a name="adding-logic-apps-b2b-solution-to-oms-portal"></a>将逻辑应用 B2B 解决方案添加到 OMS 门户
1. 在门户中选择“更多服务”****，搜索“Log Analytics”****并选择“Log Analytics”****
![搜索 Log Analytics](./media/app-service-logic-track-b2b-messages-omsportal/browseloganalytics.png)  

2. 选择 **Log Analytics**
![选择 Log Analytics](./media/app-service-logic-track-b2b-messages-omsportal/selectla.png)

3. 选择“OMS 门户”，打开 OMS 门户主页![浏览 OMS 门户](./media/app-service-logic-track-b2b-messages-omsportal/omsportalpage.png)

4. 选择“解决方案库”    
![选择解决方案库](./media/app-service-logic-track-b2b-messages-omsportal/omshomepage1.png)

5. 选择“逻辑应用 B2B”****
![选择逻辑应用 B2B](./media/app-service-logic-track-b2b-messages-omsportal/omshomepage2.png)

6. 单击“添加”将**逻辑应用 B2B 消息**添加到主页 ![选择“添加”](./media/app-service-logic-track-b2b-messages-omsportal/omshomepage3.png)

7. 浏览主页以查看**逻辑应用 B2B 消息**
![选择主页](./media/app-service-logic-track-b2b-messages-omsportal/omshomepage4.png)

8. 发布消息处理；主页更新消息计数 ![选择主页](./media/app-service-logic-track-b2b-messages-omsportal/omshomepage6.png)

9. 在主页上选择“逻辑应用 B2B 消息”将转到 AS2 和 X12 消息状态。  数据以最后一天为基础。
![选择逻辑应用 B2B 消息](./media/app-service-logic-track-b2b-messages-omsportal/omshomepage5.png)

10. 按状态选择 AS2 或 X12 消息，将转至消息列表 ![选择 AS2 消息状态](./media/app-service-logic-track-b2b-messages-omsportal/as2messagelist.png)

    ![选择 X12 消息状态](./media/app-service-logic-track-b2b-messages-omsportal/x12messagelist.png)

11. 选择 AS2 或 X12 消息列表中的一行，将转至日志搜索。  日志搜索列出具有相同**运行 ID** 的所有操作
![选择消息状态](./media/app-service-logic-track-b2b-messages-omsportal/logsearch.png)


## <a name="next-steps"></a>后续步骤
[自定义跟踪架构](app-service-logic-track-integration-account-custom-tracking-shema.md "Learn about Custom Tracking Schema")   
[AS2 跟踪架构](app-service-logic-track-integration-account-as2-tracking-shemas.md "Learn about AS2 Tracking Schema")    
[X12 跟踪架构](app-service-logic-track-integration-account-x12-tracking-shemas.md "Learn about X12 Tracking Schema")  
[了解有关 Enterprise Integration Pack 的详细信息](app-service-logic-enterprise-integration-overview.md "Learn about Enterprise Integration Pack") 


<!--HONumber=Nov16_HO3-->


