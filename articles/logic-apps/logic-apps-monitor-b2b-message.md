---
title: "监视 B2B 交易中的消息 - Azure 逻辑应用 | Microsoft 文档"
description: "在集成帐户中使用逻辑应用，监视和跟踪在进程和应用之间进行的 B2B 通信的消息。"
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
ms.date: 01/27/2017
ms.author: padmavc
ms.custom: H1Hack27Feb2017
translationtype: Human Translation
ms.sourcegitcommit: 5913c81088724ef946ae147f4f3154fa6aefd22e
ms.openlocfilehash: dc760b4c08d0e1afff3bc1276f6ed2367d67629e
ms.lasthandoff: 03/01/2017


---
# <a name="start-or-enable-logging-of-as2-x12-and-edifact-messages-to-monitor-success-errors-and-message-properties"></a>启动或启用对 AS2、X12 和 EDIFACT 消息的日志记录，监视成功、错误和消息属性

B2B 通信涉及到两个正在运行的业务流程或应用程序之间的消息交换。 该关系定义业务流程之间的协议。 建立通信后，可以设置消息监视，检查通信是否按预期进行。 若要获取更丰富的详细信息并进行调试，可为集成帐户设置诊断。

消息跟踪适用于以下 B2B 协议：AS2、X12 和 EDIFACT。 

## <a name="prerequisites"></a>先决条件

* Azure 帐户；可以创建[免费帐户](https://azure.microsoft.com/free)。
* 集成帐户；可以创建[集成帐户](logic-apps-enterprise-integration-create-integration-account.md)。
* 逻辑应用；可以创建[逻辑应用](logic-apps-create-a-logic-app.md)并[启用日志记录](logic-apps-monitor-your-logic-apps.md)。

## <a name="enable-logging-for-an-integration-account"></a>为集成帐户启用日志记录

可通过 **Azure 门户**或**监视器**为集成帐户启用日志记录。

### <a name="enable-logging-with-azure-portal"></a>使用 Azure 门户启用日志记录

1. 选择集成帐户，然后选择“诊断日志”。

    ![选择集成帐户](media/logic-apps-monitor-b2b-message/pic5.png)

2. 选择“订阅”和“资源组”。 在“资源类型”中，选择“集成帐户”。 在“资源”中选择集成帐户。 单击“开启诊断”为所选集成帐户启用诊断。

    ![为集成帐户设置诊断](media/logic-apps-monitor-b2b-message/pic2.png)

3. 选择状态“开启”。

    ![打开诊断状态](media/logic-apps-monitor-b2b-message/pic3.png)

4. 选择“发送到 Log Analytics”并配置 Log Analytics 以发出数据。

    ![将诊断数据发送到日志](media/logic-apps-monitor-b2b-message/pic4.png)

### <a name="enable-logging-with-monitor"></a>使用监视器启用日志记录

0. 选择“监视器”、“诊断日志”。

    ![选择“监视器”、“诊断日志”](media/logic-apps-monitor-b2b-message/pic1.png)

0. 选择“订阅”和“资源组”。 在“资源类型”中，选择“集成帐户”。 在“资源”中选择集成帐户。 单击“开启诊断”为所选集成帐户启用诊断。

    ![为集成帐户设置诊断](media/logic-apps-monitor-b2b-message/pic2.png)

0. 选择状态“开启”。

    ![打开诊断状态](media/logic-apps-monitor-b2b-message/pic3.png) 

0. 选择“发送到 Log Analytics”并配置 Log Analytics 以发出数据。

    ![将诊断数据发送到日志](media/logic-apps-monitor-b2b-message/pic4.png)

## <a name="extend-your-solutions"></a>扩展解决方案

除了“Log Analytics”，还可以将集成帐户和[逻辑应用](./logic-apps-monitor-your-logic-apps.md)配置到事件中心或存储帐户。

![Azure 诊断设置](./media/logic-apps-monitor-your-logic-apps/diagnostics.png)

可以从事件中心或存储将此遥测用于其他服务（如 [Azure 流分析](https://azure.microsoft.com/services/stream-analytics/)和 [Power BI](https://powerbi.com)），以便实时监视集成工作流。

## <a name="supported-tracking-schema"></a>支持的跟踪架构

我们支持这些跟踪架构类型，它们都具有除自定义类型以外的固定架构。

* [自定义跟踪架构](logic-apps-track-integration-account-custom-tracking-schema.md)
* [AS2 跟踪架构](logic-apps-track-integration-account-as2-tracking-schemas.md)
* [X12 跟踪架构](logic-apps-track-integration-account-x12-tracking-schema.md)

## <a name="next-steps"></a>后续步骤

[在 OMS 门户中跟踪 B2B 消息](logic-apps-track-b2b-messages-omsportal.md "跟踪 B2B 消息")

[了解有关 Enterprise Integration Pack 的详细信息](logic-apps-enterprise-integration-overview.md "了解 Enterprise Integration Pack")


