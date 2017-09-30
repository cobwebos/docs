---
title: "Azure Log Analytics 中的 IT Service Management Connector | Microsoft Docs"
description: "使用 IT Service Management Connector 集中监视和管理 Azure Log Analytics 中的 ITSM 工作项，并快速解决任何问题。"
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
ms.date: 06/19/2017
ms.author: v-jysur
ms.translationtype: HT
ms.sourcegitcommit: c3a2462b4ce4e1410a670624bcbcec26fd51b811
ms.openlocfilehash: 411d6103852cbf534d3c420d5ea7b2146df5164e
ms.contentlocale: zh-cn
ms.lasthandoff: 09/25/2017

---
# <a name="centrally-manage-itsm-work-items-using-it-service-management-connector-preview"></a>使用 IT Service Management Connector（预览）集成管理 ITSM 工作项

![IT Service Management Connector 符号](./media/log-analytics-itsmc/itsmc-symbol.png)

IT Service Management Connector 在支持的 IT 服务管理 (ITSM) 产品/服务和 Log Analytics 之间提供双向集成。  通过此连接，可根据 Log Analytics 警报或日志记录在 ITSM 产品中创建事件、警报或事件。 该连接器还可将 ITSM 产品中的事件和更改请求等数据导入 OMS Log Analytics。

使用 IT Service Management Connector 可以：

  - 在所选 ITSM 工具中将操作警报与事件管理做法集成。
    - 通过 OMS 警报和日志搜索在 ITSM 中创建工作项（例如警报和事件）。
    - 通过操作组中的 ITSM 操作，根据 Azure 活动日志警报创建工作项。 
  
  - 统一整个组织中使用的监视、日志和服务管理数据。
    - 将 ITSM 工具中的事件和更改请求数据与 Log Analytics 工作区中的相关日志数据关联。   
    - 查看顶级仪表板，获取有关事件、更改请求和受影响系统的概述。
    - 编写 Log Analytics 查询，深入了解服务管理数据。
      
## <a name="adding-the-it-service-management-connector-solution"></a>添加 IT Service Management Connector 解决方案

使用[从解决方案库中添加 Log Analytics 解决方案](log-analytics-add-solutions.md)中所述的过程，将 IT Service Management Connector 解决方案添加到 Log Analytics 工作区。

解决方案库中显示的 IT Service Management Connector 磁贴：

![连接器磁贴](./media/log-analytics-itsmc/itsmc-solutions-tile.png)

成功添加 IT Service Management Connector 后，它会显示在“OMS” > “设置” > “连接的源”下面。

![已连接 ITSMC](./media/log-analytics-itsmc/itsmc-overview-solution-in-connected-sources.png)

> [!NOTE]

> 默认情况下，IT Service Management Connector 每隔 24 小时刷新一次连接的数据。 若要立即刷新连接的数据以获取执行的任何编辑或模板更新，可单击连接旁边显示的“刷新”按钮。

 ![ITSMC 刷新](./media/log-analytics-itsmc/itsmc-connection-refresh.png)


## <a name="configuring-the-connection-with-your-itsm-software"></a>配置与 ITSM 软件的连接

IT Service Management Connector 解决方案支持连接到 System Center Service Manager、ServiceNow、Provance 和 Cherwell。 配置与以下各项的连接

- [System Center Service Manager (SCSM)](log-analytics-itsmc-connections.md#connect-system-center-service-manager-to-it-service-management-connector-in-oms)

- [ServiceNow](log-analytics-itsmc-connections.md#connect-servicenow-to-it-service-management-connector-in-oms)

- [Provance](log-analytics-itsmc-connections.md#connect-provance-to-it-service-management-connector-in-oms)  

- [Cherwell](log-analytics-itsmc-connections.md#connect-cherwell-to-it-service-management-connector-in-oms)

## <a name="using-the-solution"></a>使用解决方案

使用 ITSM 软件详细信息配置 IT Service Management Connector 后，该连接器将开始从所连接的 ITSM 产品/服务中收集数据。 根据 ITSM 产品/服务中的事件数和更改请求数，初始同步应在几分钟内完成。 

> [!NOTE]
> - IT Service Management Connector 解决方案从 ITSM 产品中导入的数据在 Log Analytics 中显示为 ServiceDesk_CL 类型的日志记录。
> - 日志记录包含一个名为 ServiceDeskWorkItemType_s 的字段，该名称表示事件或更改请求（两种从 ITSM 产品中导入的数据）

## <a name="data-synced-from-itsm-product"></a>从 ITSM 产品同步的数据
事件和更改请求会从 ITSM 产品同步到 Log Analytics 工作区。 以下信息显示 IT Service Management Connector 收集的数据示例：

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

## <a name="output-data-for-a-servicenow-incident"></a>ServiceNow 事件的输出数据

| OMS 字段 | ITSM 字段 |
|:--- |:--- |
| ServiceDeskId_s| Number |
| IncidentState_s | 状态 |
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
| 计算机  | 配置项 |

## <a name="output-data-for-a-servicenow-change-request"></a>ServiceNow 更改请求的输出数据

| OMS 字段 | ITSM 字段 |
|:--- |:--- |
| ServiceDeskId_s| Number |
| CreatedBy_s | 请求者 |
| ClosedBy_s | 关闭者 |
| AssignedTo_s | 已分配到  |
| Title_s|  简短说明 |
| Type_s|  类型 |
| Category_s|  类别 |
| CRState_s|  状态|
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
| 计算机  | 配置项 |

**ITSM 数据的示例 Log Analytics 屏幕：**

![Log Analytics 屏幕](./media/log-analytics-itsmc/itsmc-overview-sample-log-analytics.png)

## <a name="it-service-management-connector--integration-with-other-oms-solutions"></a>IT Service Management Connector – 与其他 OMS 解决方案集成

IT Service Management Connector 目前支持与服务映射解决方案集成。

服务映射自动发现 Windows 和 Linux 系统上的应用程序组件并映射服务之间的通信。 它允许如所想一般作为提供重要服务的互连系统查看服务器。 服务映射显示任何 TCP 连接的体系结构中服务器、进程和端口之间的连接，只需安装代理，无需任何其他配置。 详细信息：[服务映射](../operations-management-suite/operations-management-suite-service-map.md)。

如果也使用服务映射解决方案，可查看 ITSM 解决方案中创建的服务台工作项，如以下示例中所示：

![ServiceMap 集成](./media/log-analytics-itsmc/itsmc-overview-integrated-solutions.png)
## <a name="create-itsm-work-items-for-oms-alerts"></a>为 OMS 警报创建 ITSM 工作项

通过使用 ITSM Connector 解决方案，可配置 OMS 警报，在连接的 ITSM 工具中触发工作项的创建，如下所示：

1. 在“日志搜索”窗口中，运行一个用于查看数据的日志搜索查询。 查询结果即是工作项的源。
2. 在“日志搜索”中，单击“警报”打开“添加警报规则”页。

    ![Log Analytics 屏幕](./media/log-analytics-itsmc/itsmc-work-items-for-oms-alerts.png)

3. 在“添加警报规则”窗口中，为“名称”、“严重性”、“搜索查询”和“警报条件”（时间范围/指标度量值）提供所需的详细信息。
4. 为“ITSM 操作”选择“是”。
5. 在“选择连接”列表中选择 ITSM 连接。
6. 提供所需的详细信息。
7. 若要为此警报的每个日志项单独创建工作项，请选中“为每个日志项目创建单独的工作项”复选框。

    或

    如果将此复选框保留未选中状态，则对于此警报下的任何数量的日志项，只会创建一个工作项。

7. 单击“保存” 。

可在“设置”>“警报”下查看已创建的 OMS 警报。 符合指定警报的条件时，将创建相应 ITSM 连接的工作项。

## <a name="create-itsm-work-items-from-oms-logs"></a>从 OMS 日志创建 ITSM 工作项

还可从日志记录直接在连接的 ITSM 源中创建工作项，如下所示：

1. 在“日志搜索”中搜索所需的数据，选择详细信息，并单击“创建工作项”。

    此时会显示“创建 ITSM 工作项”窗口：

    ![Log Analytics 屏幕](media/log-analytics-itsmc/itsmc-work-items-from-oms-logs.png)

2.   添加以下详细信息：

  - **工作项标题**：工作项的标题。
  - **工作项说明**：新工作项的说明。
  - **受影响的计算机**：在其中找到此日志数据的计算机的名称。
  - **选择连接**：要在其中创建此工作项的 ITSM 连接。
  - **工作项**：工作项的类型。

3. 要对某个事件使用现有的工作项模板，请在“基于模板生成工作项”选项下面单击“是”，并单击“创建”。

    或者，

    如果想要提供自定义值，请单击“否”。

4. 在“联系人类型”、“影响”、“紧急性”、“类别”和“子类别”文本框中提供相应的值，并单击“创建”。

## <a name="create-itsm-work-items-from-azure-alerts"></a>根据 Azure 警报日志创建 ITSM 工作项
ITSM Connector 现与操作组集成。 [操作组](../monitoring-and-diagnostics/monitoring-action-groups.md)对 Azure 警报提供模块化且可重用的方法来触发操作。 操作组中的 ITSM 操作会使用现有 ITSM Connector 解决方案在 ITSM 产品中创建工作项。

1. 在 Azure 门户中，单击“监视器”
2. 在左窗格中，单击“操作组”

    ![操作组](media/log-analytics-itsmc/ActionGroups.png)

3. 为操作组提供“名称”和“短名称”。 选择要创建操作组的“资源组”和“订阅”。

    ![操作组详细信息](media/log-analytics-itsmc/ActionGroupsDetail.png)

4. 在“操作”列表中，从“操作类型”下拉列表中选择“ITSM”。 提供操作的“名称”并单击“编辑详细信息”。


5. 选择 Log Analytics 工作区所在的“订阅”。 选择以下连接，即 后跟工作区名称的 ITSM Connector 名称。 例如，“MyITSMMConnector(MyWorkspace)。”

    ![ITSM 操作详细信息](./media/log-analytics-itsmc/ITSMActionDetails.png)

6. 从下拉列表中选择“工作项”类型。
7. 选择使用现有模板或填充 ITSM 产品要求的字段。
8. 单击 **“确定”**

创建/编辑 Azure 警报规则时，使用具有 ITSM 操作的操作组。 警报触发时，会在 ITSM 工具中创建工作项。 

>[!NOTE]
>当前只有活动日志警报支持 ITSM 操作。 对于其他 Azure 警报，这是一个 no-op 操作。
>


## <a name="troubleshoot-itsm-connections-in-oms"></a>在 OMS 中对 ITSM 连接进行故障排除
1.  如果从连接源的 UI 建立连接失败，并出现“保存连接时出错”消息，请执行以下步骤：
 - 对于 ServiceNow、Cherwell 和 Provance 连接，
    - 请确保正确输入每个连接的用户名、密码、客户端 ID 和客户端密码。
    - 检查在相应 ITSM 产品中是否拥有建立连接的足够权限。
 - 对于 Service Manager 连接，
     - 确保成功部署 Web 应用并创建混合连接。 若要验证是否已成功建立与本地 Service Manager 计算机的连接，请访问建立[混合连接](log-analytics-itsmc-connections.md#configure-the-hybrid-connection)文档中详细介绍的 Web 应用 URL。
     
2.  如果未向 Log Analytics 同步来自 ServiceNow 的数据，请确保 ServiceNow 实例处于非休眠状态。 如果 ServiceNow 开发实例长时间处于空闲状态，有时会进入休眠状态。 否则，请报告问题。
3.  如果 OMS 警报触发但未在 ITSM 产品中创建工作项，或配置项未创建/未链接到工作项，或出于任何一般信息的目的，请查看以下位置：
 -  IT Service Management Connector 解决方案：此解决方案显示连接/工作项/计算机等摘要。单击显示“连接器状态”的磁贴，可以跳转到具有相关查询的“日志搜索”。 查看含有 LogType_S as ERROR 的日志记录，了解详细信息。
 - 或者使用 Type=ServiceDeskLog_CL 查询直接在“日志搜索”页中查看错误/相关信息。

## <a name="troubleshoot-service-manager-web-app-deployment"></a>Service Manager Web 应用部署故障排除
1.  如果遇到 Web 应用部署问题，请确保在订阅中拥有提及的足够权限，能够创建/部署资源。
2.  如果在运行[脚本](log-analytics-itsmc-service-manager-script.md)时出现“对象引用未设置为某个对象的实例”消息，请确保在“用户配置”部分下输入有效的值。
3.  如果未能创建服务总线中继命名空间，请确保在订阅中注册所需的资源提供程序。 如果未注册，请手动从 Azure 门户创建服务总线中继命名空间。 从 Azure 门户[创建混合连接](log-analytics-itsmc-connections.md#configure-the-hybrid-connection)时，也可进行创建。


## <a name="contact-us"></a>联系我们

在 IT Service Management Connector 方面如有任何咨询或反馈，请通过 [omsitsmfeedback@microsoft.com](mailto:omsitsmfeedback@microsoft.com) 联系我们。

## <a name="next-steps"></a>后续步骤
[将 ITSM 产品/服务添加到 IT Service Management Connector](log-analytics-itsmc-connections.md)。

