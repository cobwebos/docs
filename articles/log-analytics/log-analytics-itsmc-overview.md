---
title: "Azure Log Analytics 中的 IT Service Management Connector | Microsoft Docs"
description: "本文提供 IT 服务管理连接器 (ITSMC) 的概述以及有关如何使用此解决方案集中监视和管理 Azure Log Analytics 中的 ITSM 工作项并快速解决任何问题的信息。"
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
ms.date: 01/23/2018
ms.author: v-jysur
ms.openlocfilehash: 56da2d4349a4a32eed783045381e504b529b1a1c
ms.sourcegitcommit: 9d317dabf4a5cca13308c50a10349af0e72e1b7e
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/01/2018
---
# <a name="connect-azure-to-itsm-tools-using-it-service-management-connector"></a>使用 IT 服务管理连接器将 Azure 连接到 ITSM 工具

![IT Service Management Connector 符号](./media/log-analytics-itsmc/itsmc-symbol.png)

使用 IT 服务管理连接器 (ITSMC) 可以连接 Azure 和支持的 IT 服务管理 (ITSM) 产品/服务。

Log Analytics 和 Azure Monitor 等 Azure 服务提供所需的工具用于检测、分析和排查 Azure 与非 Azure 资源的问题。 但是，与问题相关的工作项通常驻留在 ITSM 产品/服务中。 ITSM 连接器在 Azure 与 ITSM 工具之间提供双向连接，有助于更快解决问题。

ITSMC 支持使用以下 ITSM 工具建立的连接：

-   ServiceNow
-   System Center Service Manager
-   Provance
-   Cherwell

使用 ITSMC，可以：

-  在 ITSM 工具中，根据 Azure 警报（指标警报、活动日志警报和 Log Analytics 警报）创建工作项。
-  可以选择将 ITSM 工具中的事件和更改请求数据同步到 Azure Log Analytics 工作区。


执行以下步骤即可开始使用 ITSM 连接器：

1.  [添加 ITSM 连接器解决方案](#adding-the-it-service-management-connector-solution)
2.  [创建 ITSM 连接](#creating-an-itsm-connection)
3.  [使用连接](#using-the-solution)


##  <a name="adding-the-it-service-management-connector-solution"></a>添加 IT Service Management Connector 解决方案

在创建连接之前，需要先添加 ITSM 连接器解决方案。

1.  在 Azure 门户中，单击“+ 新建”图标。

    ![Azure 新资源](./media/log-analytics-itsmc/azure-add-new-resource.png)

2.  在 Marketplace 中搜索“IT 服务管理连接器”，然后单击“创建”。

    ![添加 ITSMC 解决方案](./media/log-analytics-itsmc/add-itsmc-solution.png)

3.  在“OMS 工作区”部分，选择要在其中安装解决方案的 Azure Log Analytics 工作区。
4.  在“OMS 工作区设置”部分，选择要在其中创建解决方案资源的资源组。

    ![ITSMC 工作区](./media/log-analytics-itsmc/itsmc-solution-workspace.png)

5.  单击“创建”。

部署解决方案资源时，窗口右上角会显示通知。


## <a name="creating-an-itsm--connection"></a>创建 ITSM 连接

安装解决方案后，可以创建连接。

若要创建连接，需要准备好 ITSM 工具，以允许从 ITSM 连接器解决方案建立连接。  

根据要连接到 ITSM 产品执行以下步骤：

- [System Center Service Manager (SCSM)](log-analytics-itsmc-connections.md#connect-system-center-service-manager-to-it-service-management-connector-in-azure)
- [ServiceNow](log-analytics-itsmc-connections.md#connect-servicenow-to-it-service-management-connector-in-azure)
- [Provance](log-analytics-itsmc-connections.md#connect-provance-to-it-service-management-connector-in-azure)  
- [Cherwell](log-analytics-itsmc-connections.md#connect-cherwell-to-it-service-management-connector-in-azure)

准备好 ITSM 工具之后，请遵循以下步骤创建连接：

1.  转到“所有资源”，找到“ServiceDesk(YourWorkspaceName)”。
2.  在左窗格中的“工作区数据源”下，单击“ITSM 连接”。
    ![ITSM 连接](./media/log-analytics-itsmc/itsm-connections.png)

    此页显示连接列表。
3.  单击“添加连接”。

    ![添加 ITSM 连接](./media/log-analytics-itsmc/add-new-itsm-connection.png)

4.  根据[使用 ITSM 产品/服务配置 ITSMC 连接](log-analytics-itsmc-connections.md)一文中所述指定连接设置。

    > [!NOTE]

    > 默认情况下，ITSMC 每隔 24 小时刷新连接配置数据一次。 若要即时刷新连接的数据以获取执行的任何编辑或模板更新，可单击连接旁边显示的“刷新”按钮。

    ![连接刷新](./media/log-analytics-itsmc/itsmc-connections-refresh.png)


## <a name="using-the-solution"></a>使用解决方案
   使用 ITSM 连接器解决方案，可以基于 Azure 警报、Log Analytics 警报和 Log Analytics 日志记录创建工作项。

## <a name="create-itsm-work-items-from-azure-alerts"></a>根据 Azure 警报创建 ITSM 工作项

创建 ITSM 连接后，可以使用**操作组**中的 **ITSM 操作**，在 ITSM 工具中基于 Azure 警报创建工作项。

操作组对 Azure 警报提供模块化且可重用的方法来触发操作。 可以在 Azure 门户结合指标警报、活动日志警报和 Azure Log Analytics 警报使用操作组。

请按以下过程操作：

1. 在 Azure 门户中，单击“监视器”。
2. 在左窗格中，单击“操作组”。 “添加操作组”窗口随即显示。

    ![操作组](media/log-analytics-itsmc/action-groups.png)

3. 为操作组提供“名称”和“短名称”。 选择要创建操作组的“资源组”和“订阅”。

    ![操作组详细信息](media/log-analytics-itsmc/action-groups-details.png)

4. 在“操作”列表中，从“操作类型”下拉列表菜单中选择“ITSM”。 提供操作的“名称”并单击“编辑详细信息”。
5. 选择 Log Analytics 工作区所在的“订阅”。 选择后跟工作区名称的“连接”名称（你的 ITSM 连接器名称）。 例如，“MyITSMMConnector(MyWorkspace)。”

    ![ITSM 操作详细信息](./media/log-analytics-itsmc/itsm-action-details.png)

6. 从下拉列表菜单中选择“工作项”类型。
   选择使用现有模板或填充 ITSM 产品要求的字段。
7. 单击“确定”。

创建/编辑 Azure 警报规则时，使用具有 ITSM 操作的操作组。 警报触发时，会在 ITSM 工具中创建/更新工作项。

>[!NOTE]

> 有关 ITSM 操作的定价信息，请参阅操作组的[定价页](https://azure.microsoft.com/pricing/details/monitor/)。


## <a name="create-itsm-work-items-from-log-analytics-alerts"></a>基于 Log Analytics 警报创建 ITSM 工作项

可以使用以下过程在 Azure Log Analytics 门户中配置警报规则，以便在 ITSM 工具中创建工作项。

1. 在“日志搜索”窗口中，运行一个用于查看数据的日志搜索查询。 查询结果即是工作项的源。
2. 在“日志搜索”中，单击“警报”打开“添加警报规则”页。

    ![Log Analytics 屏幕](./media/log-analytics-itsmc/itsmc-work-items-for-azure-alerts.png)

3. 在“添加警报规则”窗口中，为“名称”、“严重性”、“搜索查询”和“警报条件”（时间范围/指标度量值）提供所需的详细信息。
4. 为“ITSM 操作”选择“是”。
5. 在“选择连接”列表中选择 ITSM 连接。
6. 提供所需的详细信息。
7. 若要为此警报的每个日志项单独创建工作项，请选中“为每个日志项目创建单独的工作项”复选框。

    或

    如果将此复选框保留未选中状态，则对于此警报下的任何数量的日志项，只会创建一个工作项。

7. 单击“ **保存**”。

可以在“设置”>“警报”下查看创建的 Log Analytics 警报。 符合指定警报的条件时，将创建相应 ITSM 连接的工作项。


## <a name="create-itsm-work-items-from-log-analytics-log-records"></a>基于 Log Analytics 日志记录创建 ITSM 工作项

此外，还可从日志记录直接在连接的 ITSM 源中创建工作项。 可以使用此工作项来测试连接是否正常。


1. 在“日志搜索”中搜索所需的数据，选择详细信息，并单击“创建工作项”。

    此时会显示“创建 ITSM 工作项”窗口：

    ![Log Analytics 屏幕](media/log-analytics-itsmc/itsmc-work-items-from-azure-logs.png)

2.   添加以下详细信息：

  - **工作项标题**：工作项的标题。
  - **工作项说明**：新工作项的说明。
  - **受影响的计算机**：在其中找到此日志数据的计算机的名称。
  - **选择连接**：要在其中创建此工作项的 ITSM 连接。
  - **工作项**：工作项的类型。

3. 要对某个事件使用现有的工作项模板，请在“基于模板生成工作项”选项下单击“是”，然后单击“创建”。

    或者，

    如果想要提供自定义值，请单击“否”。

4. 在“联系人类型”、“影响”、“紧急性”、“类别”和“子类别”文本框中提供相应的值，并单击“创建”。


##<a name="visualize-and-analyze-the-incident-and-change-request-data"></a>可视化和分析事件与更改请求数据

根据配置，在设置连接时，ITSM 连接器可以同步最长 120 天的事件和更改请求数据。 [下一部分](#additional-information)提供了此数据的日志记录架构。

可以在解决方案中使用 ITSM 连接器仪表板将事件和更改请求数据可视化。

![Log Analytics 屏幕](./media/log-analytics-itsmc/itsmc-overview-sample-log-analytics.png)

仪表板还提供有关连接器状态的信息，在分析任何连接问题时，可以从这些信息着手。

还可以在服务映射解决方案中，将针对受影响计算机同步的事件可视化。

服务映射自动发现 Windows 和 Linux 系统上的应用程序组件并映射服务之间的通信。 它允许如所想一般作为提供重要服务的互连系统查看服务器。 服务映射显示任何 TCP 连接的体系结构中服务器、进程和端口之间的连接，只需安装代理，无需任何其他配置。 [了解详细信息](../operations-management-suite/operations-management-suite-service-map.md)。

如果使用了服务映射解决方案，则可以查看 ITSM 解决方案中创建的服务台工作项，如以下示例中所示：

![Log Analytics 屏幕](./media/log-analytics-itsmc/itsmc-overview-integrated-solutions.png)

详细信息：[服务映射](../operations-management-suite/operations-management-suite-service-map.md)


## <a name="additional-information"></a>其他信息

### <a name="data-synced-from-itsm-product"></a>从 ITSM 产品同步的数据
事件和更改请求根据连接配置从 ITSM 产品同步到 Log Analytics 工作区。

以下信息显示 ITSMC 收集的数据示例：

> [!NOTE]

> 根据导入 Log Analytics 的工作项类型，**ServiceDesk_CL** 包含以下字段：

**工作项：****事件**  
ServiceDeskWorkItemType_s="Incident"

**字段**

- 服务台连接名称
- 服务台 ID
- State
- 紧急性
- 影响
- Priority
- 升级
- 创建者
- 解决者
- 关闭者
- Source
- 分配给
- 类别
- 标题
- 说明
- 创建日期
- 关闭日期
- 解决日期
- 上次修改日期
- Computer


**工作项：****更改请求**

ServiceDeskWorkItemType_s="ChangeRequest"

**字段**
- 服务台连接名称
- 服务台 ID
- 创建者
- 关闭者
- Source
- 分配给
- 标题
- Type
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
- Computer

## <a name="output-data-for-a-servicenow-incident"></a>ServiceNow 事件的输出数据

| Log Analytics 字段 | ServiceNow 字段 |
|:--- |:--- |
| ServiceDeskId_s| Number |
| IncidentState_s | State |
| Urgency_s |紧急性 |
| Impact_s |影响|
| Priority_s | Priority |
| CreatedBy_s | 打开者 |
| ResolvedBy_s | 解决者|
| ClosedBy_s  | 关闭者 |
| Source_s| 联系类型 |
| AssignedTo_s | 已分配到  |
| Category_s | 类别 |
| Title_s|  简短说明 |
| Description_s|  说明 |
| CreatedDate_t|  已打开 |
| ClosedDate_t| closed|
| ResolvedDate_t|已解决|
| Computer  | 配置项 |

## <a name="output-data-for-a-servicenow-change-request"></a>ServiceNow 更改请求的输出数据

| Log Analytics | ServieNow 字段 |
|:--- |:--- |
| ServiceDeskId_s| Number |
| CreatedBy_s | 请求者 |
| ClosedBy_s | 关闭者 |
| AssignedTo_s | 已分配到  |
| Title_s|  简短说明 |
| Type_s|  Type |
| Category_s|  类别 |
| CRState_s|  State|
| Urgency_s|  紧急性 |
| Priority_s| Priority|
| Risk_s| 风险|
| Impact_s| 影响|
| RequestedDate_t  | 请求日期 |
| ClosedDate_t | 关闭日期 |
| PlannedStartDate_t  |     计划开始日期 |
| PlannedEndDate_t  |   计划结束日期 |
| WorkStartDate_t  | 实际开始日期 |
| WorkEndDate_t | 实际结束日期|
| Description_s | 说明 |
| Computer  | 配置项 |


## <a name="troubleshoot-itsm-connections"></a>排查 ITSM 连接问题
1.  如果从连接源的 UI 建立连接失败，并出现“保存连接时出错”消息，请执行以下步骤：
- 对于 ServiceNow、Cherwell 和 Provance 连接，  
           - 请确保正确输入每个连接的用户名、密码、客户端 ID 和客户端密码。  
           检查在相应 ITSM 产品中是否拥有足够的权限来建立连接。  
- 对于 Service Manager 连接，  
           - 确保成功部署 Web 应用并创建混合连接。 若要验证是否已成功建立与本地 Service Manager 计算机的连接，请访问建立[混合连接](log-analytics-itsmc-connections.md#configure-the-hybrid-connection)文档中详细介绍的 Web 应用 URL。  

2.  如果未向 Log Analytics 同步来自 ServiceNow 的数据，请确保 ServiceNow 实例处于非休眠状态。 如果 ServiceNow 开发实例长时间处于空闲状态，有时会进入休眠状态。 否则，请报告问题。
3.  如果 OMS 警报触发但未在 ITSM 产品中创建工作项，或配置项未创建/未链接到工作项，或出于任何一般信息的目的，请查看以下位置：
 -  ITSMC：此解决方案显示连接/工作项/计算机等的摘要。单击显示“连接器状态”的磁贴，可以跳转到具有相关查询的“日志搜索”。 查看含有 LogType_S as ERROR 的日志记录，了解详细信息。
 - “日志搜索”页：使用 Type=ServiceDeskLog_CL 查询直接查看错误/相关信息。

## <a name="troubleshoot-service-manager-web-app-deployment"></a>Service Manager Web 应用部署故障排除
1.  如果 Web 应用部署出现任何问题，请确保在订阅中拥有提及的足够权限，以能够创建/部署资源。
2.  如果在运行[脚本](log-analytics-itsmc-service-manager-script.md)时出现“对象引用未设置为某个对象的实例”消息，请确保在“用户配置”部分下输入有效的值。
3.  如果未能创建服务总线中继命名空间，请确保在订阅中注册所需的资源提供程序。 如果未注册，请手动从 Azure 门户创建服务总线中继命名空间。 从 Azure 门户[创建混合连接](log-analytics-itsmc-connections.md#configure-the-hybrid-connection)时，也可进行创建。


## <a name="contact-us"></a>联系我们

在 IT Service Management Connector 方面如有任何咨询或反馈，请通过 [omsitsmfeedback@microsoft.com](mailto:omsitsmfeedback@microsoft.com) 联系我们。

## <a name="next-steps"></a>后续步骤
[将 ITSM 产品/服务添加到 IT Service Management Connector](log-analytics-itsmc-connections.md)。
