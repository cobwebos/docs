---
title: "为流分析中的查询设置警报 | Microsoft 文档"
description: "了解流分析警报"
keywords: "设置警报"
services: stream-analytics
documentationcenter: 
author: jeffstokes72
manager: jhubbard
editor: cgronlun
ms.assetid: 9952e2cf-b335-4a5c-8f45-8d3e1eda2e20
ms.service: stream-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: data-services
ms.date: 03/28/2017
ms.author: jeffstok
ms.translationtype: Human Translation
ms.sourcegitcommit: 7f8b63c22a3f5a6916264acd22a80649ac7cd12f
ms.openlocfilehash: d9578aa13fc22a148f76d95a2c548b88b699e723
ms.contentlocale: zh-cn
ms.lasthandoff: 05/01/2017


---
# <a name="set-up-alerts-for-azure-stream-analytics-jobs"></a>为 Azure 流分析作业设置警报
## <a name="introduction-monitor-page"></a>简介：监视页
可以设置警报，以便在指标达到指定的条件时触发警报。

例如，“如果最近 15 分钟的输出事件数 < 100，则向电子邮件 ID：xyz@company.com 发送电子邮件通知”。

可以通过门户对指标设置规则，也可以依据操作日志数据[通过编程方式](https://code.msdn.microsoft.com/windowsazure/Receive-Email-Notifications-199e2c9a)进行配置。

## <a name="set-up-alerts-through-the-azure-classic-portal"></a>通过 Azure 经典门户设置警报
可在 Azure 经典门户中通过两种方式设置警报：  

1. 流分析作业的“监视”选项卡  
2. 管理服务中的“操作日志”  

## <a name="set-up-alert-through-the-monitor-tab-of-the-job-in-the-portal"></a>通过门户中作业的“监视”页设置警报
1. 在“监视”选项卡中选择指标，再单击仪表板底部的“添加规则”按钮，然后设置规则。  
   
   ![仪表板](./media/stream-analytics-set-up-alerts/01-stream-analytics-set-up-alerts.png)  
2. 定义警报的名称和描述  
   
   ![创建规则](./media/stream-analytics-set-up-alerts/02-stream-analytics-set-up-alerts.png)  
3. 输入阈值、警报评估窗口和针对警报的操作  
   
   ![定义条件](./media/stream-analytics-set-up-alerts/03-stream-analytics-set-up-alerts.png)  

## <a name="set-up-alerts-through-the-operations-logs"></a>通过操作日志设置警报
1. 在 [Azure 经典门户](https://manage.windowsazure.com) 中的“管理服务”中，转到 **警报** 选项卡。  
2. 单击“添加规则”  
   
   ![条件](./media/stream-analytics-set-up-alerts/04-stream-analytics-set-up-alerts.png)  
3. 定义警报的名称和描述。 选择“流分析”作为服务类型，然后选择作业名称作为服务名称。  
   
   ![定义警报](./media/stream-analytics-set-up-alerts/05-stream-analytics-set-up-alerts.png)  

## <a name="set-up-alerts-in-the-azure-portal"></a>在 Azure 门户中设置警报
在 Azure 门户中，浏览到想要提出警报的流分析作业并单击“监视”部分。  在打开的“指标”边栏选项卡中，单击“添加警报”命令。

  ![Azure 门户设置](./media/stream-analytics-set-up-alerts/06-stream-analytics-set-up-alerts.png)  

可以命名警报规则，并选择显示在通知电子邮件中的说明。

当选择“指标”时，将为该指标选择一个条件和阈值。

  ![Azure 门户选择指标](./media/stream-analytics-set-up-alerts/07-stream-analytics-set-up-alerts.png)  

有关在 Azure 门户中配置警报的详细信息，请参阅[接收警报通知](../monitoring-and-diagnostics/insights-receive-alert-notifications.md)。  

## <a name="get-help"></a>获取帮助
如需进一步的帮助，请尝试我们的 [Azure 流分析论坛](https://social.msdn.microsoft.com/Forums/home?forum=AzureStreamAnalytics)

## <a name="next-steps"></a>后续步骤
* [Azure 流分析简介](stream-analytics-introduction.md)
* [Azure 流分析入门](stream-analytics-get-started.md)
* [缩放 Azure 流分析作业](stream-analytics-scale-jobs.md)
* [Azure 流分析查询语言参考](https://msdn.microsoft.com/library/azure/dn834998.aspx)
* [Azure 流分析管理 REST API 参考](https://msdn.microsoft.com/library/azure/dn835031.aspx)


