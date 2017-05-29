---
title: "OMS 中的 IT Service Management Connector | Microsoft Docs"
description: "使用 IT Service Management Connector 集中监视和管理 OMS 中的 ITSM 工作项，并快速解决任何问题。"
services: log-analytics
documentationcenter: 
author: JYOTHIRMAISURI
manager: riyazp
editor: 
ms.assetid: 0b1414d9-b0a7-4e4e-a652-d3a6ff1118c4
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/27/2017
ms.author: v-jysur
ms.translationtype: Human Translation
ms.sourcegitcommit: 2db2ba16c06f49fd851581a1088df21f5a87a911
ms.openlocfilehash: 0aa41bbc0e0135737d352553607f48a39757bcc3
ms.contentlocale: zh-cn
ms.lasthandoff: 05/09/2017


---
# <a name="centrally-manage-itsm-work-items-using-it-service-management-connector-preview"></a>使用 IT Service Management Connector（预览）集成管理 ITSM 工作项

可以使用 OMS Log Analytics 中的 IT Service Management Connector (ITSMC) 来集中监视和管理所有 ITSM 产品/服务中的工作项。

IT Service Management Connector 可将现有的 IT 服务管理 (ITSM) 产品和服务与 OMS Log Analytics 相集成。  该解决方案与 ITSM 产品/服务双向集成，为 OMS 用户提供在 ITSM 解决方案中创建事件或警报的选项。 该连接器还可将 ITSM 解决方案中的事件和更改请求等数据导入 OMS Log Analytics。

使用 IT Service Management Connector 可以：

  - 集中监视和管理在整个组织中使用的 ITSM 产品/服务的工作项。
  - 通过 OMS 警报和日志搜索在 ITSM 中创建 ITSM 工作项（例如警报和事件）。
  - 从 ITSM 解决方案读取事件和更改请求，并将其关联到 Log Analytics 工作区中的相关日志数据。
  - 查找并解决任何意外和异常的事件，即使是在最终用户呼叫技术支持人员汇报这些事件之前，也能做到这一点。
  - 将工作项数据导入 Log Analytics 并创建关键绩效指标 (KPI) 报告。  使用这些报告，可以识别、评估并处理一些重要事项，例如恶意软件评估。
  - 查看组织有序的仪表板，获取有关事件、更改请求和受影响系统的更深入见解。
  - 通过与 Log Analytics 中的其他管理解决方案建立关联，加快排查问题的速度。


## <a name="prerequisites"></a>先决条件

为了将 ITSM 工作项导入 OMS Log Analytics，该解决方案需要在 OMS 中的 IT Service Management Connector 与要从中导入工作项的 ITSM 产品/服务之间建立连接。


## <a name="configuration"></a>配置

使用[从解决方案库中添加 Log Analytics 解决方案](log-analytics-add-solutions.md)中所述的过程，将 IT Service Management Connector 解决方案添加到 OMS 工作区。

解决方案库中显示的 IT Service Management Connector 磁贴：

![连接器磁贴](./media/log-analytics-itsmc/itsmc-solutions-tile.png)

成功添加 IT Service Management Connector 后，它会显示在“OMS” > “设置” > “连接的源”下面。

![已连接 ITSMC](./media/log-analytics-itsmc/itsmc-overview-solution-in-connected-sources.png)

## <a name="management-packs"></a>管理包
此解决方案不需要任何管理包。

## <a name="connected-sources"></a>连接的源

IT Service Management Connector 支持以下 ITSM 产品/服务：

- [System Center Service Manager (SCSM)](log-analytics-itsmc-connections.md#connect-system-center-service-manager-to-it-service-management-connector-in-oms)

- [ServiceNow](log-analytics-itsmc-connections.md#connect-servicenow-to-it-service-management-connector-in-oms)

- [Provance](log-analytics-itsmc-connections.md#connect-provance-to-it-service-management-connector-in-oms)  

- [Cherwell](log-analytics-itsmc-connections.md#connect-cherwell-to-it-service-management-connector-in-oms)

## <a name="using-the-solution"></a>使用解决方案

将 OMS IT Service Management Connector 连接到 ITSM 服务后，Log Analytics 将开始从连接的 ITSM 产品/服务收集数据。

> [!NOTE]
> - IT Service Management Connector 解决方案导入的数据以名为 **ServiceDesk_CL** 的事件形式显示在 Log Analytics 中。
- 事件包含一个名为 **ServiceDeskWorkItemType_s** 的字段， 该字段的值可以是事件或更改请求，具体取决于 **ServiceDesk_CL** 事件中包含的工作项数据。

以下信息显示 IT Service Management Connector 收集的数据示例：

> [!NOTE]
> 根据导入 Log Analytics 的工作项类型，**ServiceDesk_CL** 包含以下字段：

**工作项：****事件**  
ServiceDeskWorkItemType_s="Incident"

**字段**

- 服务台连接名称
- 服务台 ID
- 状态
- 紧急性
- 影响
- Priority
- 升级
- 创建者
- 解决者
- 关闭者
- 源
- 分配给
- 类别
- 标题
- 说明
- 创建日期
- 关闭日期
- 解决日期
- 上次修改日期
- 计算机


**工作项：****更改请求**

ServiceDeskWorkItemType_s="ChangeRequest"

**字段**
- 服务台连接名称
- 服务台 ID
- 创建者
- 关闭者
- 源
- 分配给
- 标题
- 类型
- 类别
- 状态
- 升级
- 冲突状态
- 紧急性
- Priority
- 风险
- 影响
- 分配给
- 创建日期
- 关闭日期
- 上次修改日期
- 请求日期
- 计划开始日期
- 计划结束日期
- 工作开始日期
- 工作结束日期
- 说明
- 计算机

ITSM 数据的示例 Log Analytics 屏幕：

![Log Analytics 屏幕](./media/log-analytics-itsmc/itsmc-overview-sample-log-analytics.png)

## <a name="it-service-management-connector--integration-with-other-oms-solutions"></a>IT Service Management Connector – 与其他 OMS 解决方案集成

IT Service Management Connector 目前支持与服务映射解决方案集成。

服务映射自动发现 Windows 和 Linux 系统上的应用程序组件并映射服务之间的通信。 它允许你如所想一般作为提供重要服务的互连系统查看服务器。 服务映射显示任何 TCP 连接的体系结构中服务器、进程和端口之间的连接，只需安装代理，无需任何其他配置。 详细信息：[服务映射](../operations-management-suite/operations-management-suite-service-map.md)。

通过这种集成，可以查看 ITSM 解决方案中创建的服务台工作项，如以下示例中所示：

![集成解决方案 ](./media/log-analytics-itsmc/itsmc-overview-integrated-solutions.png)
## <a name="create-itsm-work-items-for-oms-alerts"></a>为 OMS 警报创建 ITSM 工作项

对于 OMS 警报，可以在连接的 ITSM 源中创建关联的工作项。  为此，请使用以下过程：

1. 在“日志搜索”窗口中，运行一个用于查看数据的日志搜索查询。 查询结果即是工作项的源。
2. 在“日志搜索”中，单击“警报”打开“添加警报规则”页。

    ![Log Analytics 屏幕](./media/log-analytics-itsmc/itsmc-work-items-for-oms-alerts.png)

3. 在“添加警报规则”窗口中，为“名称”、“严重性”、“搜索查询”和“警报条件”（时间范围/指标度量值）提供所需的详细信息。
4. 为“ITSM 操作”选择“是”。
5. 在“选择连接”列表中选择你的 ITSM 连接。
6. 提供所需的详细信息。
7. 若要为此警报的每个日志项单独创建工作项，请选中“为每个日志项目创建单独的工作项”复选框。

    或

    如果将此复选框保留未选中状态，则对于此警报下的任何数量的日志项，只会创建一个工作项。

7. 单击“保存” 。

将在“警报”下创建 OMS 警报。 符合指定警报的条件时，将创建相应 ITSM 连接的工作项。

## <a name="create-itsm-work-items-from-oms-logs"></a>从 OMS 日志创建 ITSM 工作项

可以使用 OMS 日志搜索在连接的 ITSM 源中创建工作项。 为此，请使用以下过程：

1. 在“日志搜索”中搜索所需的数据，选择详细信息，然后单击“创建工作项”。

    此时将显示“创建 ITSM 工作项”窗口：

    ![Log Analytics 屏幕](media/log-analytics-itsmc/itsmc-work-items-from-oms-logs.png)

2.   添加以下详细信息：

  - **工作项标题**：工作项的标题。
  - **工作项说明**：新工作项的说明。
  - **受影响的计算机**：在其中找到此日志数据的计算机的名称。
  - **选择连接**：要在其中创建此工作项的 ITSM 连接。
  - **工作项**：工作项的类型。

3. 若要对某个事件使用现有的工作项模板，请在“基于模板生成工作项”选项下面单击“是”，然后单击“创建”。

    或者，

    如果想要提供自定义值，请单击“否”。

4. 在“联系人类型”、“影响”、“紧急性”、“类别”和“子类别”文本框中提供相应的值，然后单击“创建”。

将在 ITSM 中创建该工作项，在 OMS 中也可以查看它。

## <a name="contact-us"></a>联系我们

在 IT Service Management Connector 方面如有任何咨询或反馈，请通过 [omsitsmfeedback@microsoft.com](mailto:omsitsmfeedback@microsoft.com) 联系我们。

## <a name="next-steps"></a>后续步骤
[将 ITSM 产品/服务添加到 IT Service Management Connector](log-analytics-itsmc-connections.md)。

