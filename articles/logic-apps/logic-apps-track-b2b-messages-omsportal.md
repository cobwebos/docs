---
title: "跟踪中 Operations Management Suite-Azure Logic Apps 的 B2B 消息 |Microsoft 文档"
description: "集成帐户和逻辑应用中 Operations Management Suite (OMS) 的 Azure 日志分析的跟踪 B2B 通信"
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
ms.sourcegitcommit: 02e69c4a9d17645633357fe3d46677c2ff22c85a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/03/2017
---
# <a name="track-b2b-communication-in-the-microsoft-operations-management-suite-oms"></a>跟踪 B2B 通信在 Microsoft Operations Management Suite (OMS)

设置两个 B2B 通信后运行业务流程或应用程序通过集成你的帐户，这些实体可以与每个其他的消息交换。 用于检查是否已正确处理这些消息、 你可以跟踪 X12、 AS2 和 EDIFACT 消息[Azure 日志分析](../log-analytics/log-analytics-overview.md)中[Operations Management Suite (OMS)](../operations-management-suite/operations-management-suite-overview.md)。 例如，你可以使用这些基于 web 的跟踪功能为跟踪消息：

* 消息计数和状态
* 确认状态
* 已确认的关联消息
* 有关故障的详细错误说明
* 搜索功能

## <a name="requirements"></a>要求

* 使用诊断日志记录设置逻辑应用。 了解[如何创建逻辑应用](logic-apps-create-a-logic-app.md)和[如何设置对该逻辑应用的日志记录](../logic-apps/logic-apps-monitor-your-logic-apps.md#azure-diagnostics)。

* 使用监视和日志记录设置集成帐户。 了解[如何创建集成帐户](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md)和[如何设置监视和日志记录为该帐户](../logic-apps/logic-apps-monitor-b2b-message.md)。

* 如果尚未准备好，[将诊断数据发布到日志分析](../logic-apps/logic-apps-track-b2b-messages-omsportal.md)OMS 中。

> [!NOTE]
> 你已满足的以前的要求后，你应准备工作区[Operations Management Suite (OMS)](../operations-management-suite/operations-management-suite-overview.md)。 用于跟踪您在 OMS 中的 B2B 通信，应使用同一个 OMS 工作区。 
>  
> 如果你没有 OMS 工作区，了解[如何创建 OMS 工作区](../log-analytics/log-analytics-get-started.md)。

## <a name="add-the-logic-apps-b2b-solution-to-the-operations-management-suite-oms"></a>将逻辑应用 B2B 解决方案添加到 Operations Management Suite (OMS)

若要使跟踪 B2B 消息应用程序逻辑的 OMS，你必须添加**逻辑应用 B2B**对 OMS 门户的解决方案。 详细了解[将解决方案添加到 OMS](../log-analytics/log-analytics-get-started.md)。

1. 在[Azure 门户](https://portal.azure.com)，选择**更服务**。 搜索"日志分析"，然后选择**日志分析**如下所示：

   ![查找日志分析](media/logic-apps-track-b2b-messages-omsportal/browseloganalytics.png)

2. 下**日志分析**，查找并选择你的 OMS 工作区。 

   ![选择你的 OMS 工作区](media/logic-apps-track-b2b-messages-omsportal/selectla.png)

3. 下**管理**，选择**OMS 门户**。

   ![选择 OMS 门户](media/logic-apps-track-b2b-messages-omsportal/omsportalpage.png)

4. OMS 主页上打开后，请选择**解决方案库**。    

   ![选择解决方案库](media/logic-apps-track-b2b-messages-omsportal/omshomepage1.png)

5. 下**所有解决方案**、 查找和选择**逻辑应用 B2B**。     

   ![选择逻辑应用 B2B](media/logic-apps-track-b2b-messages-omsportal/omshomepage2.png)

6. 下**逻辑应用 B2B**，选择**添加**。

   ![选择“添加”](media/logic-apps-track-b2b-messages-omsportal/omshomepage3.png)

   在 OMS 主页上的磁贴**逻辑应用 B2B 消息**现在会出现。 
   当处理 B2B 消息时，此磁贴更新消息计数。

   ![OMS 主页上，逻辑应用 B2B 消息磁贴](media/logic-apps-track-b2b-messages-omsportal/omshomepage4.png)

<a name="message-status-details"></a>

## <a name="track-message-status-and-details-in-the-operations-management-suite"></a>跟踪消息状态和详细信息中 Operations Management Suite

1. 在处理 B2B 消息之后，你可以查看的状态和这些消息的详细信息。 在 OMS 主页上，选择**逻辑应用 B2B 消息**磁贴。

   ![更新的消息计数](media/logic-apps-track-b2b-messages-omsportal/omshomepage6.png)

   > [!NOTE]
   > 默认情况下，**逻辑应用 B2B 消息**磁贴将显示基于一天的数据。 若要将数据作用域更改为不同的时间间隔中，选择 OMS 页顶部的作用域控件：
   > 
   > ![更改数据作用域](media/logic-apps-track-b2b-messages-omsportal/change-interval.png)
   >

2. 消息后将显示状态仪表板，你可以查看对于特定消息类型，显示基于一天的数据的更多详细信息。 选择的磁贴**AS2**， **X12**，或**EDIFACT**。

   ![查看消息状态](media/logic-apps-track-b2b-messages-omsportal/omshomepage5.png)

   为你选择的磁贴显示的消息列表。 
   若要了解有关每种消息类型的属性的详细信息，请参阅这些消息属性说明：

   * [AS2 消息属性](#as2-message-properties)
   * [X12 消息属性](#x12-message-properties)
   * [EDIFACT 消息属性](#EDIFACT-message-properties)

   例如，下面是 AS2 消息列表可能如下所示：

   ![查看 AS2 消息](media/logic-apps-track-b2b-messages-omsportal/as2messagelist.png)

3. 若要查看或导出的输入和输出的特定消息，请选择这些消息，并选择**下载**。 当系统提示你时，该.zip 文件保存到本地计算机，并将该文件然后提取。 

   提取的文件夹包含用于每个选定的消息的文件夹。 
   如果你设置了确认，消息文件夹还包含与确认详细信息的文件。 
   每个消息文件夹具有至少这些文件： 
   
   * 使用输入的负载和输出负载详细信息的用户可读的文件
   * 具有输入和输出的编码的文件

   对于每种消息类型，您可以找到的文件夹和文件名称格式：

   * [AS2 文件夹和文件名称格式](#as2-folder-file-names)
   * [X12 文件夹和文件名称格式](#x12-folder-file-names)
   * [EDIFACT 文件夹和文件名称格式](#edifact-folder-file-names)

   ![下载消息文件](media/logic-apps-track-b2b-messages-omsportal/download-messages.png)

4. 若要查看所有具有相同的操作运行 ID，在**日志搜索**页上，从消息列表中选择一条消息。

   您可以进行排序列或搜索特定结果的这些的操作。

   ![具有相同的操作的运行 ID](media/logic-apps-track-b2b-messages-omsportal/logsearch.png)

   * 若要搜索包含预构建查询的结果，请选择**收藏夹**。

   * 了解[如何通过添加筛选器生成查询](logic-apps-track-b2b-messages-omsportal-query-filter-control-number.md)。 
   或了解有关[如何在日志分析中找到使用日志搜索数据](../log-analytics/log-analytics-log-searches.md)。

   * 若要更改的搜索框中的查询，请使用的列和你想要用作筛选器的值更新查询。

<a name="message-list-property-descriptions"></a>

## <a name="property-descriptions-and-name-formats-for-as2-x12-and-edifact-messages"></a>属性描述和名称格式以了解 AS2 X12 和 EDIFACT 消息

对于每种消息类型，这里有属性说明和下载的消息文件的名称格式。

<a name="as2-message-properties"></a>

### <a name="as2-message-property-descriptions"></a>AS2 消息属性说明

以下是为每个 AS2 消息的属性说明。

| 属性 | 说明 |
| --- | --- |
| 发送方 | 中指定的来宾合作伙伴**接收设置**，或者中指定的托管合作伙伴**发送设置**对于 AS2 协议 |
| 接收方 | 中指定的托管合作伙伴**接收设置**，或者中指定的来宾合作伙伴**发送设置**对于 AS2 协议 |
| 逻辑应用 | 逻辑应用的 AS2 操作设置 |
| 状态 | AS2 消息状态 <br>成功 = 接收或发送有效的 AS2 消息。 没有 MDN 设置。 <br>成功 = 接收或发送有效的 AS2 消息。 设置和接收 MDN 或发送 MDN。 <br>失败 = 已接收了无效的 AS2 消息。 没有 MDN 设置。 <br>挂起 = 接收或发送有效的 AS2 消息。 MDN 设置和预期 MDN。 |
| Ack | MDN 消息状态 <br>接受 = 接收或发送正 MDN。 <br>挂起 = 等待接收或发送 MDN。 <br>拒绝 = 接收或发送负 MDN。 <br>不需要 = 在协议中未设置 MDN。 |
| 方向 | AS2 消息方向 |
| 相关性 ID | 关联的所有触发器和逻辑应用中的操作 ID |
| 消息 ID | AS2 消息标头中的 AS2 消息 ID |
| Timestamp | AS2 操作时处理消息的时间 |
|          |             |

<a name="as2-folder-file-names"></a>

### <a name="as2-name-formats-for-downloaded-message-files"></a>AS2 为下载的消息文件的的名称格式

以下是为每个下载的 AS2 消息文件夹和文件的名称格式。

| 文件夹或文件 | 名称格式 |
| :------------- | :---------- |
| 消息文件夹 | [发件人]\_[接收方]\_AS2\_[相关 ID]\_[消息 ID]\_[时间戳] |
| 输入、 输出和如果设置，确认文件 | **输入的负载**: [发件人]\_[接收方]\_AS2\_[相关 ID]\_input_payload.txt </p>**输出负载**: [发件人]\_[接收方]\_AS2\_[相关 ID]\_输出\_payload.txt </p></p>**输入**: [发件人]\_[接收方]\_AS2\_[相关 ID]\_inputs.txt </p></p>**输出**: [发件人]\_[接收方]\_AS2\_[相关 ID]\_outputs.txt |
|          |             |

<a name="x12-message-properties"></a>

### <a name="x12-message-property-descriptions"></a>X12 消息属性说明

以下是每个 X12 属性描述消息。

| 属性 | 说明 |
| --- | --- |
| 发送方 | 中指定的来宾合作伙伴**接收设置**，或者中指定的托管合作伙伴**发送设置**对于 X12 协议 |
| 接收方 | 中指定的托管合作伙伴**接收设置**，或者中指定的来宾合作伙伴**发送设置**对于 X12 协议 |
| 逻辑应用 | 逻辑应用，其中 X12 操作设置 |
| 状态 | X12 消息时状态 <br>成功 = 接收或发送有效的 X12 消息。 没有功能确认设置。 <br>成功 = 接收或发送有效的 X12 消息。 设置和收到，功能 ack 或发送功能确认。 <br>失败 = 接收或发送无效的 X12 消息。 <br>挂起 = 接收或发送有效的 X12 消息。 功能 ack 设置和预期功能确认。 |
| Ack | 功能确认 (997) 状态 <br>接受 = 接收或发送正功能确认使用 <br>拒绝 = 接收或发送负功能确认使用 <br>挂起 = 预期功能确认，但未收到。 <br>挂起 = 生成功能确认，但无法发送到合作伙伴。 <br>不需要 = 功能确认未设置。 |
| 方向 | X12 消息方向 |
| 相关性 ID | 关联的所有触发器和逻辑应用中的操作 ID |
| 消息类型 | EDI X 12 消息类型 |
| ICN | 交换控制编号 x12 消息 |
| TSCN | 事务集控制编号 x12 消息 |
| Timestamp | 时间时 X12 处理消息的操作 |
|          |             |

<a name="x12-folder-file-names"></a>

### <a name="x12-name-formats-for-downloaded-message-files"></a>X12 名称下载的消息文件格式

以下是名称格式，以每个下载 X12 消息文件夹和文件。

| 文件夹或文件 | 名称格式 |
| :------------- | :---------- |
| 消息文件夹 | [发件人]\_[接收方]\_X12\_[交换控制编号]\_[全局控制编号]\_[事务-集-控制-编号]\_[时间戳] |
| 输入、 输出和如果设置，确认文件 | **输入的负载**: [发件人]\_[接收方]\_X12\_[交换控制编号]\_input_payload.txt </p>**输出负载**: [发件人]\_[接收方]\_X12\_[交换控制编号]\_输出\_payload.txt </p></p>**输入**: [发件人]\_[接收方]\_X12\_[交换控制编号]\_inputs.txt </p></p>**输出**: [发件人]\_[接收方]\_X12\_[交换控制编号]\_outputs.txt |
|          |             |

<a name="EDIFACT-message-properties"></a>

### <a name="edifact-message-property-descriptions"></a>EDIFACT 消息属性说明

以下是为每个 EDIFACT 消息的属性说明。

| 属性 | 说明 |
| --- | --- |
| 发送方 | 中指定的来宾合作伙伴**接收设置**，或者中指定的托管合作伙伴**发送设置**EDIFACT 协议 |
| 接收方 | 中指定的托管合作伙伴**接收设置**，或者中指定的来宾合作伙伴**发送设置**EDIFACT 协议 |
| 逻辑应用 | 逻辑应用的 EDIFACT 操作设置 |
| 状态 | EDIFACT 消息状态 <br>成功 = 接收或发送有效的 EDIFACT 消息。 没有功能确认设置。 <br>成功 = 接收或发送有效的 EDIFACT 消息。 设置和收到，功能 ack 或发送功能确认。 <br>失败 = 接收或发送了无效的 EDIFACT 消息 <br>挂起 = 接收或发送有效的 EDIFACT 消息。 功能 ack 设置和预期功能确认。 |
| Ack | 功能确认 (997) 状态 <br>接受 = 接收或发送正功能确认使用 <br>拒绝 = 接收或发送负功能确认使用 <br>挂起 = 预期功能确认，但未收到。 <br>挂起 = 生成功能确认，但无法发送到合作伙伴。 <br>不需要 = 功能确认未设置。 |
| 方向 | EDIFACT 消息方向 |
| 相关性 ID | 关联的所有触发器和逻辑应用中的操作 ID |
| 消息类型 | EDIFACT 消息类型 |
| ICN | EDIFACT 消息交换控制编号 |
| TSCN | 事务集控制编号 EDIFACT 消息 |
| Timestamp | EDIFACT 操作时处理消息的时间 |
|          |               |

<a name="edifact-folder-file-names"></a>

### <a name="edifact-name-formats-for-downloaded-message-files"></a>EDIFACT 为下载的消息文件的的名称格式

以下是为每个下载的 EDIFACT 消息文件夹和文件的名称格式。

| 文件夹或文件 | 名称格式 |
| :------------- | :---------- |
| 消息文件夹 | [发件人]\_[接收方]\_EDIFACT\_[交换控制编号]\_[全局控制编号]\_[事务-集-控制-编号]\_[时间戳] |
| 输入、 输出和如果设置，确认文件 | **输入的负载**: [发件人]\_[接收方]\_EDIFACT\_[交换控制编号]\_input_payload.txt </p>**输出负载**: [发件人]\_[接收方]\_EDIFACT\_[交换控制编号]\_输出\_payload.txt </p></p>**输入**: [发件人]\_[接收方]\_EDIFACT\_[交换控制编号]\_inputs.txt </p></p>**输出**: [发件人]\_[接收方]\_EDIFACT\_[交换控制编号]\_outputs.txt |
|          |             |

## <a name="next-steps"></a>后续步骤

* [在 Operations Management Suite 的 B2B 消息查询](../logic-apps/logic-apps-track-b2b-messages-omsportal-query-filter-control-number.md)
* [AS2 跟踪架构](../logic-apps/logic-apps-track-integration-account-as2-tracking-schemas.md)
* [X12 跟踪架构](../logic-apps/logic-apps-track-integration-account-x12-tracking-schema.md)
* [自定义跟踪架构](../logic-apps/logic-apps-track-integration-account-custom-tracking-schema.md)