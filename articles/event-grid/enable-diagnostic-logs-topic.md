---
title: Azure 事件网格-启用主题的诊断日志
description: 本文提供了有关如何为 Azure 事件网格主题启用诊断日志的分步说明。
services: event-grid
author: spelluru
ms.service: event-grid
ms.topic: how-to
ms.date: 01/30/2020
ms.author: spelluru
ms.openlocfilehash: a32d26629bb9efed4a4f1f49eee2f0534e1873a9
ms.sourcegitcommit: fa6fe765e08aa2e015f2f8dbc2445664d63cc591
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/01/2020
ms.locfileid: "76960498"
---
#  <a name="diagnostic-logs-for-an-azure-event-grid-topic"></a>Azure 事件网格主题的诊断日志
诊断设置允许事件网格用户捕获和查看下列位置之一中的发布和传递失败日志： Azure 存储帐户、事件中心或 Log Analytics 工作区。 本文提供了为事件网格主题启用诊断日志的分步说明。

## <a name="prerequisites"></a>必备组件

- 预配的事件网格主题
- 用于捕获诊断日志的预配目标。 它可以是下列目标之一：
    - Azure 存储帐户
    - 事件中心
    - Log Analytics 工作区


## <a name="steps-for-enabling-diagnostic-logs-for-a-topic"></a>为主题启用诊断日志的步骤

1. 登录 [Azure 门户](https://portal.azure.com)。
2. 导航到要为其启用诊断日志设置的事件网格主题。 
3. 在左侧菜单中选择 "**监视**" 下的 "**诊断设置**"。
4. 在 "**诊断设置**" 页上，选择 "**添加新诊断设置**"。 
    
    ![添加诊断设置按钮](./media/enable-diagnostic-logs-topic/diagnostic-settings-add.png)
5. 指定诊断设置的**名称**。 

    ![诊断设置-名称](./media/enable-diagnostic-logs-topic/diagnostic-settings-name.png)     
6. 为日志启用一个或多个捕获目标，然后通过选择以前创建的捕获资源对其进行配置。 
    - 如果选择 "**存档到存储帐户**"，请选择 "**存储帐户-配置**"，然后选择 Azure 订阅中的存储帐户。 

        ![存档到 Azure 存储帐户](./media/enable-diagnostic-logs-topic/archive-storage.png)
    - 如果选择 "**流式传输到事件中心**"，请选择 "**事件中心"-"配置**"，然后选择 "事件中心命名空间"、"事件中心" 和 "访问策略"。 
        ![流传输到事件中心](./media/enable-diagnostic-logs-topic/archive-event-hub.png)
    - 如果选择 "**发送到 Log Analytics**"，请选择 Log Analytics 工作区。
        ![发送到 Log Analytics](./media/enable-diagnostic-logs-topic/send-log-analytics.png)
7. 选择 "**日志**" 部分中的**DeliveryFailures**和**PublishFailures**选项。 
    ![选择 "失败"](./media/enable-diagnostic-logs-topic/log-failures.png)
8. 选择“保存”。 选择右下角的 " **X** " 以关闭页面。 
9. 现在，返回到 "**诊断设置**" 页，确认在 "**诊断设置**" 表中显示新条目。 
    列表中 ![诊断设置](./media/enable-diagnostic-logs-topic/diagnostic-setting-list.png)

     你还可以为主题启用所有指标的收集。 

## <a name="next-steps"></a>后续步骤
如果需要更多帮助，请将你的问题发布到[Stack Overflow 论坛](https://stackoverflow.com/questions/tagged/azure-eventgrid)，或提出[支持票证](https://azure.microsoft.com/support/options/)。 
