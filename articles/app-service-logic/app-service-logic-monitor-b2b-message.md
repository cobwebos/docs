---
title: "监视 B2B 消息 |Microsoft Docs"
description: "如何监视集成帐户"
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
ms.date: 10/21/2016
ms.author: padmavc
translationtype: Human Translation
ms.sourcegitcommit: 63ba6e8fec8df1cdbc9928b011130f47c5facf89
ms.openlocfilehash: 8ce3585a557f6b2c9fa4a4c85142cdce9357a90b


---
# <a name="monitor-b2b-messages"></a>监视 B2B 消息
B2B 通信涉及到两个正在运行的业务流程或应用程序之间的消息交换。 该关系定义业务流程之间的协议。 建立通信之后，需要有一种方法来监视通信是否如预期一样正常进行。  已针对 B2B 协议实现了信息跟踪：AS2、X12 和 EDIFACT。  可配置集成帐户以使用诊断，从而获得更丰富的详细信息和调试

## <a name="prerequisites"></a>先决条件
* Azure 帐户；可以创建[免费帐户](https://azure.microsoft.com/free)
* 集成帐户；可创建一个[集成帐户](app-service-logic-enterprise-integration-create-integration-account.md)
* 逻辑应用；可创建[逻辑应用](app-service-logic-create-a-logic-app.md)并启用日志记录；可在[此处](app-service-logic-monitor-your-logic-apps.md)找到步骤

## <a name="enable-logging-for-an-integration-account"></a>为集成帐户启用日志记录
可通过 **Azure 门户**或**监视器**为集成帐户启用日志记录

### <a name="enable-logging-with-azure-portal"></a>使用 Azure 门户启用日志记录

1. 选择“集成帐户”****并选择“诊断日志”**** 
![选择集成帐户](./media/app-service-logic-monitor-integration-account/Pic5.png)  

2. 从资源类型选择你的“订阅”、“资源组”和“集成帐户”，并从资源下拉列表中选择你的“集成帐户”以启用诊断。  单击“开启诊断”为所选集成帐户启用诊断               
![选择“集成帐户”](./media/app-service-logic-monitor-integration-account/Pic2.png) 

3. 选择状态“开启”       
![开启诊断](./media/app-service-logic-monitor-integration-account/Pic3.png) 

4. 选择“发送到 Log Analytics”并配置 Log Analytics 以发出数据               
![开启诊断](./media/app-service-logic-monitor-integration-account/Pic4.png)

### <a name="enable-logging-with-monitor"></a>使用监视器启用日志记录

1. 选择“监视器”并单击“诊断日志”   
![选择“监视器”](./media/app-service-logic-monitor-integration-account/Pic1.png)

2. 从资源类型选择你的“订阅”、“资源组”和“集成帐户”，并从资源下拉列表中选择你的“集成帐户”以启用诊断。  单击“开启诊断”为所选集成帐户启用诊断               
![选择“集成帐户”](./media/app-service-logic-monitor-integration-account/Pic2.png)

3. 选择状态“开启”       
![开启诊断](./media/app-service-logic-monitor-integration-account/Pic3.png) 

4. 选择“发送到 Log Analytics”并配置 Log Analytics 以发出数据 ![开启诊断](./media/app-service-logic-monitor-integration-account/Pic4.png)

## <a name="extending-your-solutions"></a>扩展解决方案
除了“Log Analytics”，还可以将集成帐户和[逻辑应用](./app-service-logic-monitor-your-logic-apps.md)配置到事件中心或存储帐户         
![Azure 诊断设置](./media/app-service-logic-monitor-your-logic-apps/diagnostics.png)

可以从事件中心或存储将此遥测用于其他服务（如 [Azure 流分析](https://azure.microsoft.com/services/stream-analytics/)和 [Power BI](https://powerbi.com)），以便实时监视集成工作流。

## <a name="supported-tracking-schema"></a>支持的跟踪架构
目前支持以下跟踪架构类型。  除“自定义”类型外，所有这些类型均具有固定架构。

* [自定义跟踪架构](app-service-logic-track-integration-account-custom-tracking-shema.md)   
* [AS2 跟踪架构](app-service-logic-track-integration-account-as2-tracking-shemas.md)   
* [X12 跟踪架构](app-service-logic-track-integration-account-x12-tracking-shemas.md)  

## <a name="next-steps"></a>后续步骤
[在 OMS 门户中跟踪 B2B 消息](app-service-logic-track-b2b-messages-omsportal.md "Tracking B2B messages")   
[了解有关 Enterprise Integration Pack 的详细信息](app-service-logic-enterprise-integration-overview.md "Learn about Enterprise Integration Pack") 




<!--HONumber=Nov16_HO3-->


