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
ms.sourcegitcommit: 6e56dae0ab30612a7794bd39e6eba2f84bcecc19
ms.openlocfilehash: eae37a357e3e1c00310c7c638d3164118403ee9f


---
# <a name="create-a-query-in-oms-portal"></a>在 OMS 门户中创建查询 
创建查询（用于筛选特定交换控制编号的数据）的步骤

## <a name="prerequisites"></a>先决条件

为[集成帐户](app-service-logic-monitor-b2b-message.md)和[逻辑应用](app-service-logic-monitor-your-logic-apps.md#azure-diagnostics-and-alerts)启用诊断，这二者都拥有 X12 控制器，可提供丰富的详细信息和调试选项。  请按照[此处](app-service-logic-track-b2b-messages-omsportal.md)的步骤将诊断数据发布到 OMS 门户。

## <a name="create-a-query-to-search-data-for-a-specific-interchange-control-number"></a>创建查询搜索特定交换控制编号的数据

1. 在主页上选择“日志搜索”  
![选择“日志搜索”](./media/app-service-logic-track-b2b-messages-omsportal-query-filter-control-number/logsearch.png)

2. 在搜索窗口键入 **Type="AzureDiagnostics"**，拉取所有数据。  单击“添加”筛选数据  
![选择“查询”](./media/app-service-logic-track-b2b-messages-omsportal-query-filter-control-number/query1.png)

3. 键入 **interchange**，选择 **event_record_messageProperties_interchangeControlNumber_s**，并单击“添加”  
![选择“控制编号”](./media/app-service-logic-track-b2b-messages-omsportal-query-filter-control-number/query2.png)

4. 选择想要筛选数据的控制编号，并单击“应用”  
![选择“控制编号”](./media/app-service-logic-track-b2b-messages-omsportal-query-filter-control-number/query3.png)

5. 将显示为筛选所选控制编号的数据而建的查询   
![选择“控制编号”](./media/app-service-logic-track-b2b-messages-omsportal-query-filter-control-number/query4.png)

6. 命名查询并保存   
![选择“控制编号”](./media/app-service-logic-track-b2b-messages-omsportal-query-filter-control-number/query5.png) 

7. 选择“收藏夹”，查看查询并将其用在以后的搜索中  
![选择“控制编号”](./media/app-service-logic-track-b2b-messages-omsportal-query-filter-control-number/query7.png)

8. 可更新查询以搜索新的交换控制编号  
![选择“控制编号”](./media/app-service-logic-track-b2b-messages-omsportal-query-filter-control-number/query6.png) 


## <a name="next-steps"></a>后续步骤
[自定义跟踪架构](app-service-logic-track-integration-account-custom-tracking-shema.md "Learn about Custom Tracking Schema")   
[AS2 跟踪架构](app-service-logic-track-integration-account-as2-tracking-shemas.md "Learn about AS2 Tracking Schema")    
[X12 跟踪架构](app-service-logic-track-integration-account-x12-tracking-shemas.md "Learn about X12 Tracking Schema")  
[了解有关 Enterprise Integration Pack 的详细信息](app-service-logic-enterprise-integration-overview.md "Learn about Enterprise Integration Pack") 


<!--HONumber=Nov16_HO3-->


