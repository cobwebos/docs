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
ms.date: 06/26/2017
ms.author: jeffstok
ms.translationtype: Human Translation
ms.sourcegitcommit: bb794ba3b78881c967f0bb8687b1f70e5dd69c71
ms.openlocfilehash: a0c9c771c273728cf35d9cd727e1570e2a9bff5a
ms.contentlocale: zh-cn
ms.lasthandoff: 07/06/2017


---
# <a name="set-up-alerts-for-azure-stream-analytics-jobs"></a>为 Azure 流分析作业设置警报
## <a name="introduction-monitor-page"></a>简介：监视页
可以设置警报，以便在指标达到指定的条件时触发警报。 例如，可为如下条件设置警报：

`If there are zero input events in the last 5 minutes, send email notification to sa-admin@example.com`

可以通过门户对指标设置规则，也可以依据操作日志数据[通过编程方式](https://code.msdn.microsoft.com/windowsazure/Receive-Email-Notifications-199e2c9a)进行配置。

## <a name="set-up-alerts-in-the-azure-portal"></a>在 Azure 门户中设置警报
1. 在 Azure 门户中，打开要为其创建警报的流分析作业。 

2. 在“作业”边栏选项卡中，单击“监视”部分。  

3. 在“指标”边栏选项卡中，单击“添加警报”命令。

      ![Azure 门户设置](./media/stream-analytics-set-up-alerts/06-stream-analytics-set-up-alerts.png)  

4. 输入名称和描述。

5. 使用选择器定义需发送警报的条件。

6. 提供有关警报应发送到何处的信息。

      ![为 Azure 流分析作业设置警报](./media/stream-analytics-set-up-alerts/stream-analytics-add-alert.png)  

有关在 Azure 门户中配置警报的详细信息，请参阅[接收警报通知](../monitoring-and-diagnostics/insights-receive-alert-notifications.md)。  


## <a name="get-help"></a>获取帮助
如需进一步的帮助，请尝试我们的 [Azure 流分析论坛](https://social.msdn.microsoft.com/Forums/home?forum=AzureStreamAnalytics)

## <a name="next-steps"></a>后续步骤
* [Azure 流分析简介](stream-analytics-introduction.md)
* [Azure 流分析入门](stream-analytics-get-started.md)
* [缩放 Azure 流分析作业](stream-analytics-scale-jobs.md)
* [Azure 流分析查询语言参考](https://msdn.microsoft.com/library/azure/dn834998.aspx)
* [Azure 流分析管理 REST API 参考](https://msdn.microsoft.com/library/azure/dn835031.aspx)


