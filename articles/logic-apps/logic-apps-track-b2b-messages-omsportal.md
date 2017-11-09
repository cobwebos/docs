---
title: "在 Operations Management Suite 中跟踪 B2B 消息 - Azure 逻辑应用 | Microsoft Docs"
description: "在 Operations Management Suite (OMS) 中使用 Azure Log Analytics 跟踪集成帐户和逻辑应用的 B2B 通信"
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
ms.date: 07/21/2017
ms.author: LADocs; padmavc
ms.openlocfilehash: 3ef7a4054be80547b0d91ad1f13777d915005f8b
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/11/2017
---
# <a name="track-b2b-communication-in-the-microsoft-operations-management-suite-oms"></a>在 Microsoft Operations Management Suite (OMS) 中跟踪 B2B 通信

通过集成帐户设置两个运行的业务流程或应用程序的 B2B 通信后，这些实体可以彼此交换消息。 若要检查这些消息是否得到正确处理，可以在 [Operations Management Suite (OMS)](../operations-management-suite/operations-management-suite-overview.md) 中使用 [Azure Log Analytics](../log-analytics/log-analytics-overview.md) 跟踪 AS2、X12 和 EDIFACT 消息。 例如，可以使用下面这些 Web 跟踪功能来跟踪消息：

* 消息计数和状态
* 确认状态
* 已确认的关联消息
* 有关故障的详细错误说明
* 搜索功能

## <a name="requirements"></a>要求

* 已设置诊断日志记录的逻辑应用。 了解[如何创建逻辑应用](logic-apps-create-a-logic-app.md)以及[如何为逻辑应用设置日志记录](../logic-apps/logic-apps-monitor-your-logic-apps.md#azure-diagnostics)。

* 已设置监视和日志记录的集成帐户。 了解[如何创建集成帐户](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md)以及[如何为集成帐户设置监视和日志记录](../logic-apps/logic-apps-monitor-b2b-message.md)。

* 如果尚未准备好，请在 OMS 中[将诊断数据发布到 Log Analytics](../logic-apps/logic-apps-track-b2b-messages-omsportal.md)。

> [!NOTE]
> 满足上述要求后，应在 [Operations Management Suite (OMS)](../operations-management-suite/operations-management-suite-overview.md) 中有一个工作区。 应使用相同的 OMS 工作区来跟踪你在 OMS 上的 B2B 通信。 
>  
> 如果没有 OMS 工作区，了解[如何创建 OMS 工作区](../log-analytics/log-analytics-get-started.md)。

## <a name="add-the-logic-apps-b2b-solution-to-the-operations-management-suite-oms"></a>将逻辑应用 B2B 解决方案添加到 Operations Management Suite (OMS)

若要让 OMS 跟踪逻辑应用的 B2B 消息，必须将逻辑应用 B2B 解决方案添加到 OMS 门户。 详细了解如何[将解决方案添加到 OMS](../log-analytics/log-analytics-get-started.md)。

1. 在 [Azure 门户](https://portal.azure.com)中，选择“更多服务”。 搜索“日志分析”，然后选择“Log Analytics”，如下所示：

   ![查找 Log Analytics](media/logic-apps-track-b2b-messages-omsportal/browseloganalytics.png)

2. 在“Log Analytics”下，查找并选择 OMS 工作区。 

   ![选择你的 OMS 工作区](media/logic-apps-track-b2b-messages-omsportal/selectla.png)

3. 在“管理”下，选择“OMS 门户”。

   ![选择“OMS 门户”](media/logic-apps-track-b2b-messages-omsportal/omsportalpage.png)

4. OMS 主页打开后，选择“解决方案库”。    

   ![选择“解决方案库”](media/logic-apps-track-b2b-messages-omsportal/omshomepage1.png)

5. 在“所有解决方案”下，查找并选择“逻辑应用 B2B”。     

   ![选择“逻辑应用 B2B”](media/logic-apps-track-b2b-messages-omsportal/omshomepage2.png)

6. 在“逻辑应用 B2B”下，选择“添加”。

   ![选择“添加”](media/logic-apps-track-b2b-messages-omsportal/omshomepage3.png)

   在 OMS 主页上，此时会显示“逻辑应用 B2B 消息”磁贴。 
   此磁贴更新 B2B 消息经过处理后的消息计数。

   ![OMS 主页上的“逻辑应用 B2B 消息”磁贴](media/logic-apps-track-b2b-messages-omsportal/omshomepage4.png)

<a name="message-status-details"></a>

## <a name="track-message-status-and-details-in-the-operations-management-suite"></a>在 Operations Management Suite 中跟踪消息状态和详细信息

1. 在 B2B 消息经过处理后，可以查看这些消息的状态和详细信息。 在 OMS 主页上，选择“逻辑应用 B2B 消息”磁贴。

   ![更新后的消息计数](media/logic-apps-track-b2b-messages-omsportal/omshomepage6.png)

   > [!NOTE]
   > 默认情况下，“逻辑应用 B2B 消息”磁贴显示的是一天数据。 若要将数据范围更改为其他时间间隔，请选择 OMS 页面顶部的范围控件：
   > 
   > ![更改数据范围](media/logic-apps-track-b2b-messages-omsportal/change-interval.png)
   >

2. 当消息状态仪表板显示后，可以查看特定消息类型的更多详细信息（显示的也是一天数据）。 选择“AS2”、“X12”或“EDIFACT”磁贴。

   ![查看消息状态](media/logic-apps-track-b2b-messages-omsportal/omshomepage5.png)

   系统会针对选择的磁贴显示消息列表。 
   若要详细了解每种消息类型的属性，请参阅这些消息属性说明：

   * [AS2 消息属性](#as2-message-properties)
   * [X12 消息属性](#x12-message-properties)
   * [EDIFACT 消息属性](#EDIFACT-message-properties)

   例如，AS2 消息列表如下所示：

   ![查看 AS2 消息](media/logic-apps-track-b2b-messages-omsportal/as2messagelist.png)

3. 若要查看或导出特定消息的的输入和输出内容，请依次选择这些消息和“下载”。 看到提示时，将 .zip 文件保存到本地计算机，再解压缩此文件。 

   在解压缩的文件夹中，选择的每个消息都对应一个文件夹。 
   如果设置了确认信息，消息文件夹中还有包含确认详细信息的文件。 
   每个消息文件夹至少有下面这些文件： 
   
   * 包含输入和输出有效负载详细信息的人工可读文件
   * 包含输入和输出内容的编码文件

   对于每种消息类型，可以单击下面的链接，了解文件夹和文件名格式：

   * [AS2 文件夹和文件名格式](#as2-folder-file-names)
   * [X12 文件夹和文件名格式](#x12-folder-file-names)
   * [EDIFACT 文件夹和文件名格式](#edifact-folder-file-names)

   ![下载消息文件](media/logic-apps-track-b2b-messages-omsportal/download-messages.png)

4. 若要查看运行 ID 相同的所有操作，请在“日志搜索”页上的消息列表中选择一条消息。

   可以按列对这些操作进行排序，也可以搜索特定结果。

   ![运行 ID 相同的操作](media/logic-apps-track-b2b-messages-omsportal/logsearch.png)

   * 若要使用预生成查询搜索结果，请选择“收藏夹”。

   * 了解[如何通过添加筛选器生成查询](logic-apps-track-b2b-messages-omsportal-query-filter-control-number.md)。 
   或详细了解[如何在 Log Analytics 中使用日志搜索查找数据](../log-analytics/log-analytics-log-searches.md)。

   * 若要更改搜索框中的查询，请使用要用作筛选器的列和值来更新查询。

<a name="message-list-property-descriptions"></a>

## <a name="property-descriptions-and-name-formats-for-as2-x12-and-edifact-messages"></a>AS2、X12 和 EDIFACT 消息的属性说明和名称格式

对于每种消息类型，下面介绍了已下载消息文件的属性说明和名称格式。

<a name="as2-message-properties"></a>

### <a name="as2-message-property-descriptions"></a>AS2 消息属性说明

下面列出了各个 AS2 消息的属性说明。

| 属性 | 说明 |
| --- | --- |
| 发送方 | “接收设置”中指定的来宾合作伙伴，或 AS2 协议的“发送设置”中指定的托管合作伙伴 |
| 接收方 | “接收设置”中指定的托管合作伙伴，或 AS2 协议的“发送设置”中指定的来宾合作伙伴 |
| 逻辑应用 | 设置了 AS2 操作的逻辑应用 |
| 状态 | AS2 消息状态 <br>成功 = 收到或发送了有效的 AS2 消息。 未设置 MDN。 <br>成功 = 收到或发送了有效的 AS2 消息。 设置并收到了 MDN，或已发送 MDN。 <br>失败 = 收到的 AS2 消息无效。 未设置 MDN。 <br>挂起 = 收到或发送了有效的 AS2 消息。 已设置 MDN，且 MDN 符合预期。 |
| Ack | MDN 消息状态 <br>已接受 = 收到或发送了肯定的 MDN。 <br>挂起 = 等待接收或发送 MDN。 <br>已拒绝 = 收到或发送了否定的 MDN。 <br>不需要 = 协议中未设置 MDN。 |
| 方向 | AS2 消息传送方向 |
| 相关性 ID | 用于关联逻辑应用中所有触发器和操作的 ID |
| 消息 ID | AS2 消息头中的 AS2 消息 ID |
| Timestamp | AS2 操作处理消息的时间 |
|          |             |

<a name="as2-folder-file-names"></a>

### <a name="as2-name-formats-for-downloaded-message-files"></a>已下载消息文件的 AS2 名称格式

下面列出了每个已下载 AS2 消息文件夹和文件的名称格式。

| 文件夹或文件 | 名称格式 |
| :------------- | :---------- |
| 消息文件夹 | [发送方]\_[接收方]\_AS2\_[关联 ID]\_[消息 ID]\_[时间戳] |
| 输入、输出文件，以及确认文件（若设置） | **输入有效负载**：[发送方]\_[接收方]\_AS2\_[关联 ID]\_input_payload.txt </p>**输出有效负载**：[发送方]\_[接收方]\_AS2\_[关联 ID]\_output\_payload.txt </p></p>**输入**：[发送方]\_[接收方]\_AS2\_[关联 ID]\_inputs.txt </p></p>**输出**：[发送方]\_[接收方]\_AS2\_[关联 ID]\_outputs.txt |
|          |             |

<a name="x12-message-properties"></a>

### <a name="x12-message-property-descriptions"></a>X12 消息属性说明

下面列出了各个 X12 消息的属性说明。

| 属性 | 说明 |
| --- | --- |
| 发送方 | “接收设置”中指定的来宾合作伙伴，或 X12 协议的“发送设置”中指定的托管合作伙伴 |
| 接收方 | “接收设置”中指定的托管合作伙伴，或 X12 协议的“发送设置”中指定的来宾合作伙伴 |
| 逻辑应用 | 设置了 X12 操作的逻辑应用 |
| 状态 | X12 消息状态 <br>成功 = 收到或发送了有效的 X12 消息。 未设置功能确认。 <br>成功 = 收到或发送了有效的 X12 消息。 设置并收到了功能确认，或已发送功能确认。 <br>失败 = 收到或发送的 X12 消息无效。 <br>挂起 = 收到或发送了有效的 X12 消息。 已设置功能确认，且功能确认符合预期。 |
| Ack | 功能确认 (997) 状态 <br>已接受 = 收到或发送了肯定的功能确认。 <br>已拒绝 = 收到或发送了否定的功能确认。 <br>挂起 = 预计有功能确认，但未收到。 <br>挂起 = 生成了功能确认，但无法发送给合作伙伴。 <br>不需要 = 未设置功能确认。 |
| 方向 | X12 消息传送方向 |
| 相关性 ID | 用于关联逻辑应用中所有触发器和操作的 ID |
| 消息类型 | EDI X12 消息类型 |
| ICN | X12 消息的交换控制编号 |
| TSCN | X12 消息的交易集控制编号 |
| Timestamp | X12 操作处理消息的时间 |
|          |             |

<a name="x12-folder-file-names"></a>

### <a name="x12-name-formats-for-downloaded-message-files"></a>已下载消息文件的 X12 名称格式

下面列出了每个已下载 X12 消息文件夹和文件的名称格式。

| 文件夹或文件 | 名称格式 |
| :------------- | :---------- |
| 消息文件夹 | [发送方]\_[接收方]\_X12\_[交换控制编号]\_[全局控制编号]\_[交易集控制编号]\_[时间戳] |
| 输入、输出文件，以及确认文件（若设置） | **输入有效负载**：[发送方]\_[接收方]\_X12\_[交换控制编号]\_input_payload.txt </p>**输出有效负载**：[发送方]\_[接收方]\_X12\_[交换控制编号]\_output\_payload.txt </p></p>**输入**：[发送方]\_[接收方]\_X12\_[交换控制编号]\_inputs.txt </p></p>**输出**：[发送方]\_[接收方]\_X12\_[交换控制编号]\_outputs.txt |
|          |             |

<a name="EDIFACT-message-properties"></a>

### <a name="edifact-message-property-descriptions"></a>EDIFACT 消息属性说明

下面列出了各个 EDIFACT 消息的属性说明。

| 属性 | 说明 |
| --- | --- |
| 发送方 | “接收设置”中指定的来宾合作伙伴，或 EDIFACT 协议的“发送设置”中指定的托管合作伙伴 |
| 接收方 | “接收设置”中指定的托管合作伙伴，或 EDIFACT 协议的“发送设置”中指定的来宾合作伙伴 |
| 逻辑应用 | 设置了 EDIFACT 操作的逻辑应用 |
| 状态 | EDIFACT 消息状态 <br>成功 = 收到或发送了有效的 EDIFACT 消息。 未设置功能确认。 <br>成功 = 收到或发送了有效的 EDIFACT 消息。 设置并收到了功能确认，或已发送功能确认。 <br>失败 = 收到或发送了的 EDIFACT 消息无效 <br>挂起 = 收到或发送了有效的 EDIFACT 消息。 已设置功能确认，且功能确认符合预期。 |
| Ack | 功能确认 (997) 状态 <br>已接受 = 收到或发送了肯定的功能确认。 <br>已拒绝 = 收到或发送了否定的功能确认。 <br>挂起 = 预计有功能确认，但未收到。 <br>挂起 = 生成了功能确认，但无法发送给合作伙伴。 <br>不需要 = 未设置功能确认。 |
| 方向 | EDIFACT 消息传送方向 |
| 相关性 ID | 用于关联逻辑应用中所有触发器和操作的 ID |
| 消息类型 | EDIFACT 消息类型 |
| ICN | EDIFACT 消息的交换控制编号 |
| TSCN | EDIFACT 消息的交易集控制编号 |
| Timestamp | EDIFACT 操作处理消息的时间 |
|          |               |

<a name="edifact-folder-file-names"></a>

### <a name="edifact-name-formats-for-downloaded-message-files"></a>已下载消息文件的 EDIFACT 名称格式

下面列出了每个已下载 EDIFACT 消息文件夹和文件的名称格式。

| 文件夹或文件 | 名称格式 |
| :------------- | :---------- |
| 消息文件夹 | [发送方]\_[接收方]\_EDIFACT\_[交换控制编号]\_[全局控制编号]\_[交易集控制编号]\_[时间戳] |
| 输入、输出文件，以及确认文件（若设置） | **输入有效负载**：[发送方]\_[接收方]\_EDIFACT\_[交换控制编号]\_input_payload.txt </p>**输出有效负载**：[发送方]\_[接收方]\_EDIFACT\_[交换控制编号]\_output\_payload.txt </p></p>**输入**：[发送方]\_[接收方]\_EDIFACT\_[交换控制编号]\_inputs.txt </p></p>**输出**：[发送方]\_[接收方]\_EDIFACT\_[交换控制编号]\_outputs.txt |
|          |             |

## <a name="next-steps"></a>后续步骤

* [在 Operations Management Suite 中查询 B2B 消息](../logic-apps/logic-apps-track-b2b-messages-omsportal-query-filter-control-number.md)
* [AS2 跟踪架构](../logic-apps/logic-apps-track-integration-account-as2-tracking-schemas.md)
* [X12 跟踪架构](../logic-apps/logic-apps-track-integration-account-x12-tracking-schema.md)
* [自定义跟踪架构](../logic-apps/logic-apps-track-integration-account-custom-tracking-schema.md)