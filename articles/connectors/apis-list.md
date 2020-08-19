---
title: 适用于 Azure 逻辑应用的连接器
description: 使用 Azure 逻辑应用的连接器（例如内置、托管、本地集成帐户、ISE 和企业连接器）自动执行工作流
services: logic-apps
ms.suite: integration
ms.reviewer: jonfan, logicappspm
ms.topic: article
ms.date: 06/11/2020
ms.openlocfilehash: 1dd38f0360a4471124497d8357481283cd98383c
ms.sourcegitcommit: 37afde27ac137ab2e675b2b0492559287822fded
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/18/2020
ms.locfileid: "88566295"
---
# <a name="connectors-for-azure-logic-apps"></a>适用于 Azure 逻辑应用的连接器

使用连接器可以从 Azure 逻辑应用快速访问其他应用、服务、系统、协议和平台上的事件、数据和操作。 使用逻辑应用中的连接器，可以扩展云中和本地应用的功能，以便对创建的数据和现有的数据执行任务。

虽然逻辑应用提供[数百个连接器](/connectors)，但本文只介绍一些流行且较常用的连接器，数千个应用和数百万次执行已成功使用这些连接器来处理数据和信息。 若要查找连接器的完整列表以及每个连接器的参考信息（例如触发器、操作和限制），请查看[连接器概述](/connectors)下的连接器参考页。 另外，请详细了解[触发器和操作](#triggers-actions)、[逻辑应用定价模型](../logic-apps/logic-apps-pricing.md)以及[逻辑应用定价详细信息](https://azure.microsoft.com/pricing/details/logic-apps/)。

> [!TIP]
> 若要与没有连接器的服务或 API 集成，可以通过 HTTP 等协议直接调用该服务，或创建[自定义连接器](#custom)。

## <a name="connector-types"></a>连接器类型

连接器可用作内置触发器和操作，或用作托管的连接器。

<a name="built-in"></a>

* [**内置**](#built-ins)：内置触发器和操作是 Azure 逻辑应用“原有的”，可帮助你针对逻辑应用执行以下任务：

  * 按自定义计划和高级计划运行。

  * 组织和控制逻辑应用的工作流（例如循环和条件），以及处理变量和数据操作。

  * 与其他终结点通信。

  * 接收和响应请求。

  * 调用 Azure 函数、Azure API 应用（Web 应用）、通过 Azure API 管理进行管理和发布的自有 API，以及接收请求的嵌套逻辑应用。

<a name="managed-connectors"></a>

* [**托管连接器**](#managed-api-connectors)：这些连接器由 Microsoft 部署和管理，提供触发器与操作用于访问云服务和/或本地系统，包括 Office 365、Azure Blob 存储、SQL Server、Dynamics、Salesforce、SharePoint 等等。 某些连接器专门支持企业到企业 (B2B) 通信方案，需要一个与逻辑应用链接的[集成帐户](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md)。 在使用某些连接器之前，可能需要先创建由 Azure 逻辑应用管理的连接。

  例如，如果你使用 Microsoft BizTalk Server，则逻辑应用可以使用 [BizTalk Server 本地连接器](#on-premises-connectors)连接到 BizTalk Server 并与其通信。 然后，可以使用[集成帐户连接器](#integration-account-connectors)在逻辑应用中扩展或执行类似于 BizTalk 的操作。

  连接器分类为“标准”或“企业”连接器。 [企业连接器](#enterprise-connectors)提供对 SAP、IBM MQ 和 IBM 3270 等企业系统的访问，但会产生额外的费用。 若要确定某个连接器是标准还是企业连接器，请参阅[连接器概述](/connectors)下每个连接器的参考页中的技术详细信息。

  也可以使用这些类别来识别连接器，不过，某些连接器可能属于多个类别。 例如，SAP 既是企业连接器，也是本地连接器：

  | 类别 | 说明 |
  |----------|-------------|
  | [**托管连接器**](#managed-api-connectors) | 创建使用 Azure Blob 存储、Office 365、Dynamics、Power BI、OneDrive、Salesforce、SharePoint Online 等服务的逻辑应用。 |
  | [**本地连接器**](#on-premises-connectors) | 安装并设置[本地数据网关][gateway-doc]后，这些连接器可以帮助逻辑应用访问 SQL Server、SharePoint Server、Oracle DB、文件共享等本地系统。 |
  | [**集成帐户连接器**](#integration-account-connectors) | 创建和付费购买集成帐户时可以使用这些连接器，它们会转换和验证 XML、编码和解码平面文件，以及使用 AS2、EDIFACT 和 X12 协议处理企业到企业 (B2B) 消息。 |
  |||

<a name="integration-service-environment"></a>

### <a name="connect-from-an-integration-service-environment-ise"></a>从 integration service 环境连接 (ISE) 

对于需要直接访问 Azure 虚拟网络中的资源的逻辑应用，你可以 [ (ISE) 创建专用 integration service 环境 ](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md) ，你可以在该环境中生成、部署和运行针对专用资源的逻辑应用。 在逻辑应用设计器中，当你在 ISE 中浏览要用于逻辑应用的连接器时，内置触发器和操作上将显示一个 **核心** 标签，而 **ISE** 标签显示在某些连接器上。

> [!NOTE]
> 在 ISE 及其连接器中运行的逻辑应用，不管这些连接器的运行位置如何，都遵循固定定价计划与基于消耗的定价计划。 有关详细信息，请参阅 [逻辑应用定价模型](../logic-apps/logic-apps-pricing.md) 和 [逻辑应用定价详细](https://azure.microsoft.com/pricing/details/logic-apps/)信息。

| Label | 示例 | 说明 |
|-------|---------|-------------|
| **转储** | ![ISE 连接器示例](./media/apis-list/example-core-connector.png) | 此标签的内置触发器和操作与逻辑应用在同一 ISE 中运行。 |
| **ISE** | ![ISE 连接器示例](./media/apis-list/example-ise-connector.png) | 此标签的托管连接器与逻辑应用在同一 ISE 中运行。 如果你的本地系统已连接到 Azure 虚拟网络，则 ISE 允许你的逻辑应用直接访问该系统，而无需使用 [本地数据网关](../logic-apps/logic-apps-gateway-connection.md)。 相反，你可以使用该系统的 **ISE** 连接器（如果有）、HTTP 操作或 [自定义连接器](#custom)。 对于没有 **ISE** 连接器的本地系统，请使用本地数据网关。 若要查看可用的 ISE 连接器，请参阅 [ise 连接器](#ise-connectors)。 |
| 无标签 | ![多租户连接器示例](./media/apis-list/example-multi-tenant-connector.png) | 无 **核心** 或 **ISE** 标签的所有其他连接器，你可以继续使用该标签，在全局多租户逻辑应用服务中运行。 |
|||

<a name="built-ins"></a>

## <a name="built-in"></a>内置

逻辑应用提供内置触发器和操作，使你能够创建基于计划的工作流、帮助逻辑应用与其他应用和服务通信、通过逻辑应用控制工作流，以及管理或处理数据。

| 名称 | 说明 |
|------|-------------|
| [![API 图标][schedule-icon]<br>**计划**][schedule-doc] | - 使用[ **“重复周期”** 触发器][schedule-recurrence-doc]，按指定的重复周期以简单到高级的计划运行逻辑应用。 <br>- 使用[ **“滑动窗口”** 触发器][schedule-sliding-window-doc]，运行需要处理连续区块中的数据的逻辑应用。 <br>- 使用[ **“延迟”** 操作][schedule-delay-doc]，将逻辑应用暂停指定的持续时间。 <br>- 使用[ **“延迟截止时间”** 操作][schedule-delay-until-doc]，将逻辑应用暂停到指定的日期和时间。 |
| [![API 图标][batch-icon]<br>**批处理**][batch-doc] | - 使用“批处理消息”触发器来批量处理消息。 <br>- 使用“发送要批量处理的消息”操作调用具有现有批处理触发器的逻辑应用。 |
| [![API 图标][http-icon]<br>**HTTP**][http-doc] | 使用适用于 HTTP 的触发器和操作调用 HTTP 或 HTTPS 终结点。 其他 HTTP 内置触发器和操作包括 [HTTP + Swagger][http-swagger-doc] 和 [HTTP + Webhook][http-webhook-doc]。 |
| [![API 图标][http-request-icon]<br>**请求**][http-request-doc] | - 使用“请求”触发器，使逻辑应用可从其他应用程序或服务调用、针对事件网格资源事件触发，或者针对 Azure 安全中心警报触发。 <br>- 使用“响应”操作将响应发送到应用或服务。 |
| [![API 图标][azure-api-management-icon]<br>**Azure API <br>管理**][azure-api-management-doc] | 调用可以使用 Azure API 管理进行管理和发布的自有 API 定义的触发器与操作。 |
| [![API 图标][azure-app-services-icon]<br>**Azure 应用<br>服务**][azure-app-services-doc] | 调用 Azure 应用服务上托管的 Azure API 应用或 Web 应用。 包含 Swagger 后，这些应用定义的触发器和操作类似于其他任何第一类触发器和操作。 |
| [![API 图标][azure-logic-apps-icon]<br>**Azure 逻辑<br>应用**][nested-logic-app-doc] | 调用使用“请求”触发器启动的其他逻辑应用。 |
|||

### <a name="run-code-from-logic-apps"></a>从逻辑应用运行代码

逻辑应用提供内置操作用于在逻辑应用工作流中运行你自己的代码：

| 名称 | 说明 |
|------|-------------|
| [![API 图标][azure-functions-icon]<br>**Azure Functions**][azure-functions-doc] | 从逻辑应用调用运行自定义代码片段（C# 或 Node.js）的 Azure 函数。 |
| [![API 图标][inline-code-icon]<br>**内联代码**][inline-code-doc] | 从逻辑应用添加和运行 JavaScript 代码片段。 |
|||

### <a name="control-workflow"></a>控制工作流

逻辑应用提供用于在逻辑应用工作流中构建和控制操作的内置操作：

| 名称 | 说明 |
|------|-------------|
| [![“内置”图标][condition-icon]<br>**条件**][condition-doc] | 评估条件，并根据条件是 true 还是 false 运行不同的操作。 |
| [![“内置”图标][for-each-icon]<br>**For each**][for-each-doc] | 对数组中的每个项执行相同的操作。 |
| [![内置图标 ][scope-icon]<br> **范围**][scope-doc] | 将操作分组到范围，以便在该范围内的操作完成运行后，获取这些操作的自身状态。 |
| [![“内置”图标][switch-icon]<br>**Switch**][switch-doc] | 将操作分组到案例，而案例分配有唯一的值（默认案例除外）。 仅运行其分配值与表达式、对象或令牌的结果相匹配的案例。 如果不存在任何匹配项，则运行默认案例。 |
| [![“内置”图标][terminate-icon]<br>**Terminate**][terminate-doc] | 停止当前正在运行的逻辑应用工作流。 |
| [![“内置”图标][until-icon]<br>**Until**][until-doc] | 重复操作，直到指定的条件为 true 或某个状态发生更改。 |
|||

### <a name="manage-or-manipulate-data"></a>管理或处理数据

逻辑应用提供用于处理数据输出及其格式的内置操作：

| 名称 | 说明 |
|------|-------------|
| [![“内置”图标][data-operations-icon]<br>**数据操作**][data-operations-doc] | 对数据执行操作： <p>- **撰写**：基于具有不同类型的多个输入创建单个输出。 <br>- **创建 CSV 表**：基于包含 JSON 对象的数组创建逗号分隔值 (CSV) 表。 <br>- **创建 HTML 表**：基于包含 JSON 对象的数组创建一个 HTML 表。 <br>- **筛选数组**：基于符合条件的另一个数组中的项创建一个数组。 <br>- **联接**：基于数组中的所有项创建一个字符串，并使用指定的分隔符分隔这些项。 <br>- **分析 JSON**：基于 JSON 内容中的属性及其值创建用户友好的令牌，以便可以在工作流中使用这些属性。 <br>- **选择**：通过转换另一数组中的项或值并将这些项映射到指定的属性，创建包含 JSON 对象的数组。 |
| ![内置操作图标][date-time-icon]<br>**日期时间** | 对时间戳执行操作： <p>- **添加到时间**：将指定的单位数添加到时间戳。 <br>- **转换时区**：将时间戳从源时区转换为目标时区。 <br>- **当前时间**：返回字符串形式的当前时间戳。 <br>- **获取将来的时间**：返回当前时间戳加上指定的时间单位。 <br>- **获取过去的时间**：返回当前时间戳减去指定的时间单位。 <br>- **从时间中减去**：从时间戳中减去一定数目的时间单位。 |
| [![“内置”图标][variables-icon]<br>**变量**][variables-doc] | 对变量执行操作： <p>- **追加到数组变量**：插入一个值，作为变量存储的数组中的最后一个项。 <br>- **追加到字符串变量**：插入一个值，作为变量存储的字符串中的最后一个字符。 <br>- **递减变量**：按常量值减小变量。 <br>- **递增变量**：按常量值增大变量。 <br>- **初始化变量**：创建一个变量并声明其数据类型和初始值。 <br>- **设置变量**：将不同的值分配给现有变量。 |
|||

<a name="managed-api-connectors"></a>

## <a name="managed-connectors"></a>托管的连接器

逻辑应用提供以下用于自动化这些服务或系统的任务、流程和工作流的流行标准连接器：

| 名称 | 说明 |
|------|-------------|
| [![API 图标][azure-service-bus-icon]<br>**Azure 服务总线**][azure-service-bus-doc] | 使用逻辑应用中最常用的连接器管理异步消息、会话和主题订阅。 |
| [![API 图标][sql-server-icon]<br>**SQL Server**][sql-server-doc] | 连接到本地 SQL Server 或云中的 Azure SQL 数据库，以便可以管理记录、运行存储过程或执行查询。 |
| [![API 图标][azure-blob-storage-icon]<br>**Azure Blob<br>存储**][azure-blob-storage-doc] | 连接到存储帐户，以便可以创建和管理 Blob 内容。 |
| [![API 图标][office-365-outlook-icon]<br>**Office 365<br>Outlook**][office-365-outlook-doc] | 连接到 Office 365 电子邮件帐户，以便可以创建和管理电子邮件、任务、日历事件和会议、联系人、请求，等等。 |
| [![API 图标][sftp-ssh-icon]<br>**SFTP-SSH**][sftp-ssh-doc] | 使用 SSH 连接到可从 Internet 访问的 SFTP 服务器，以便可以处理文件和文件夹。 |
| [![API 图标][sharepoint-online-icon]<br>**SharePoint<br>Online**][sharepoint-online-doc] | 连接到 SharePoint Online，以便可以管理文件、附件、文件夹，等等。 |
| [![API 图标][azure-queues-icon]<br>**Azure <br>队列**][azure-queues-doc] | 连接到你的 Azure 存储帐户，以便你可以创建和管理队列和消息。 |
| [![API 图标][ftp-icon]<br>**FTP**][ftp-doc] | 连接到可从 Internet 访问的 FTP 服务器，以便可以处理文件和文件夹。 |
| [![API 图标][file-system-icon]<br>**文件<br>系统**][file-system-doc] | 连接到本地文件共享，以便可以创建和管理文件。 |
| [![API 图标][azure-event-hubs-icon]<br>**Azure 事件中心**][azure-event-hubs-doc] | 通过事件中心使用和发布事件。 例如，通过事件中心获取逻辑应用的输出，然后将输出发送到实时分析提供程序。 |
| [![API 图标][azure-event-grid-icon]<br>**Azure 事件**<br>**网格**][azure-event-grid-doc] | 监视事件网格发布的事件（例如，当 Azure 资源或第三方资源发生更改时）。 |
| [![API 图标][salesforce-icon]<br>**Salesforce**][salesforce-doc] | 连接到 Salesforce 帐户，以便可以创建和管理记录、作业、对象，等等。 |
|||

<a name="on-premises-connectors"></a>

## <a name="on-premises-connectors"></a>本地连接器

逻辑应用提供下面这些常用的标准连接器用于访问本地系统中的数据和资源。 在创建与本地系统的连接之前，必须先[下载、安装并设置本地数据网关][gateway-doc]。 此网关提供安全信道，无需设置所需的网络基础结构。

:::row:::
    :::column:::
        [![API 图标][biztalk-server-icon]<br>**BizTalk** <br>**Server**][biztalk-server-doc]
    :::column-end:::
    :::column:::
        [![API 图标][file-system-icon]<br>**文件<br>系统**][file-system-doc]
    :::column-end:::
    :::column:::
        [![API 图标 ][ibm-db2-icon]<br> **IBM DB2**][ibm-db2-doc]
    :::column-end:::
    :::column:::
        [![API 图标 ][ibm-informix-icon]<br> **IBM** <br> **Informix**][ibm-informix-doc]
    :::column-end:::
:::row-end:::
:::row:::
    :::column:::
        [![API 图标][mysql-icon]<br>**MySQL**][mysql-doc]
    :::column-end:::
    :::column:::
        [![API 图标][oracle-db-icon]<br>**Oracle DB**][oracle-db-doc]
    :::column-end:::
    :::column:::
        [![API 图标][postgre-sql-icon]<br>**PostgreSQL**][postgre-sql-doc]
    :::column-end:::
    :::column:::
        [![API 图标][sharepoint-server-icon]<br>**SharePoint <br>Server**][sharepoint-server-doc]
    :::column-end:::
:::row-end:::
:::row:::
    :::column:::
        [![API 图标][sql-server-icon]<br>**SQL <br>Server**][sql-server-doc]
    :::column-end:::
    :::column:::
        [![API 图标][teradata-icon]<br>**Teradata**][teradata-doc]
    :::column-end:::
    :::column:::
        
    :::column-end:::
    :::column:::
        
    :::column-end:::
:::row-end:::

<a name="integration-account-connectors"></a>

## <a name="integration-account-connectors"></a>集成帐户连接器

逻辑应用提供标准连接器，用于在创建和付费购买[集成帐户](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md)（通过 Azure 中的 Enterprise Integration Pack (EIP) 提供）时，在逻辑应用中生成企业到企业 (B2B) 解决方案。 使用此帐户可以创建和存储 B2B 项目，例如贸易合作伙伴、协议、映射、架构、证书，等等。 若要使用这些项目，请将逻辑应用与集成帐户相关联。 如果你当前使用的是 BizTalk Server，则可能已熟悉这些连接器的用法。

:::row:::
    :::column:::
        [![API 图标][as2-icon]<br>**AS2 <br>解码**][as2-doc]
    :::column-end:::
    :::column:::
        [![API 图标][as2-icon]<br>**AS2 <br>编码**][as2-doc]
    :::column-end:::
    :::column:::
        [![API 图标][edifact-icon]<br>**EDIFACT <br>解码**][edifact-decode-doc]
    :::column-end:::
    :::column:::
        [![API 图标][edifact-icon]<br>**EDIFACT <br>编码**][edifact-encode-doc]
    :::column-end:::
:::row-end:::
:::row:::
    :::column:::
        [![API 图标][flat-file-decode-icon]<br>**平面文件<br>解码**][flat-file-decode-doc]
    :::column-end:::
    :::column:::
        [![API 图标][flat-file-encode-icon]<br>**平面文件<br>编码**][flat-file-encode-doc]
    :::column-end:::
    :::column:::
        [![API 图标][integration-account-icon]<br>**集成<br>帐户**][integration-account-doc]
    :::column-end:::
    :::column:::
        [![API 图标][liquid-icon]<br>**Liquid** <br>**转换**][json-liquid-transform-doc]
    :::column-end:::
:::row-end:::
:::row:::
    :::column:::
        [![API 图标][x12-icon]<br>**X12 <br>解码**][x12-decode-doc]
    :::column-end:::
    :::column:::
        [![API 图标][x12-icon]<br>**X12 <br>编码**][x12-encode-doc]
    :::column-end:::
    :::column:::
        [![API 图标][xml-transform-icon]<br>**XML** <br>**转换**][xml-transform-doc]
    :::column-end:::
    :::column:::
        [![API 图标][xml-validate-icon]<br>**XML <br>验证**][xml-validate-doc]
    :::column-end:::
:::row-end:::

<a name="enterprise-connectors"></a>

## <a name="enterprise-connectors"></a>企业连接器

逻辑应用提供以下用于访问 SAP 和 IBM MQ 等企业系统的企业连接器：

:::row:::
    :::column:::
        [![API 图标 ][ibm-3270-icon]<br> **IBM 3270**][ibm-3270-doc]
    :::column-end:::
    :::column:::
        [![API 图标][ibm-mq-icon]<br>**IBM MQ**][ibm-mq-doc]
    :::column-end:::
    :::column:::
        [![API 图标][sap-icon]<br>**SAP**][sap-connector-doc]
    :::column-end:::
    :::column:::
        
    :::column-end:::
:::row-end:::

<a name="ise-connectors"></a>

## <a name="ise-connectors"></a>ISE 连接器

对于在 [ (ISE) 的专用 integration service 环境 ](#integration-service-environment)中创建和运行的逻辑应用，逻辑应用设计器通过使用 " **核心** " 标签来标识在 ISE 中运行的内置触发器和操作。 在 ISE 中运行的托管连接器会显示 **ISE** 标签，而在全局多租户逻辑应用服务中运行的连接器不会显示任何一个标签。 此列表显示当前具有 ISE 版本的连接器：

:::row:::
    :::column:::
        [![API 图标 ][as2-icon]<br> **AS2**][as2-doc]
    :::column-end:::
    :::column:::
        [![API 图标 ][azure-automation-icon]<br> **Azure <br> 自动化**][azure-automation-doc]
    :::column-end:::
    :::column:::
        [![API 图标][azure-blob-storage-icon]<br>**Azure Blob<br>存储**][azure-blob-storage-doc]
    :::column-end:::
    :::column:::
        [![API 图标 ][azure-cosmos-db-icon]<br> **Azure Cosmos <br> DB**][azure-cosmos-db-doc]
    :::column-end:::
:::row-end:::
:::row:::
    :::column:::
        [![API 图标 ][azure-event-hubs-icon]<br> **Azure 事件 <br> 中心**][azure-event-hubs-doc]
    :::column-end:::
    :::column:::
        [![API 图标 ][azure-event-grid-icon]<br> **Azure 事件 <br> 网格**][azure-event-grid-doc]
    :::column-end:::
    :::column:::
        [![API 图标 ][azure-file-storage-icon]<br> **Azure 文件 <br> 存储**][azure-file-storage-doc]
    :::column-end:::
    :::column:::
        [![API 图标 ][azure-key-vault-icon]<br> **Azure 密钥 <br> 保管库**][azure-key-vault-doc]
    :::column-end:::
:::row-end:::
:::row:::
    :::column:::
        [![API 图标 ][azure-monitor-logs-icon]<br> **Azure Monitor <br> 日志**][azure-monitor-logs-doc]
    :::column-end:::
    :::column:::
        [![API 图标 ][azure-service-bus-icon]<br> **Azure 服务 <br> 总线**][azure-service-bus-doc]
    :::column-end:::
    :::column:::
        [![API 图标 ][azure-sql-data-warehouse-icon]<br> **Azure SQL 数据 <br> 仓库**][azure-sql-data-warehouse-doc]
    :::column-end:::
    :::column:::
        [![API 图标 ][azure-table-storage-icon]<br> **Azure 表 <br> 存储**][azure-table-storage-doc]
    :::column-end:::
:::row-end:::
:::row:::
    :::column:::
        [![API 图标][azure-queues-icon]<br>**Azure <br>队列**][azure-queues-doc]
    :::column-end:::
    :::column:::
        [![API 图标 ][edifact-icon]<br> **EDIFACT**][edifact-doc]
    :::column-end:::
    :::column:::
        [![API 图标][file-system-icon]<br>**文件<br>系统**][file-system-doc]
    :::column-end:::
    :::column:::
        [![API 图标][ftp-icon]<br>**FTP**][ftp-doc]
    :::column-end:::
:::row-end:::
:::row:::
    :::column:::
        [![API 图标 ][ibm-3270-icon]<br> **IBM 3270**][ibm-3270-doc]
    :::column-end:::
    :::column:::
        [![API 图标 ][ibm-db2-icon]<br> **IBM DB2**][ibm-db2-doc]
    :::column-end:::
    :::column:::
        [![API 图标][ibm-mq-icon]<br>**IBM MQ**][ibm-mq-doc]
    :::column-end:::
    :::column:::
        [![API 图标][sap-icon]<br>**SAP**][sap-connector-doc]
    :::column-end:::
:::row-end:::
:::row:::
    :::column:::
        [![API 图标][sftp-ssh-icon]<br>**SFTP-SSH**][sftp-ssh-doc]
    :::column-end:::
    :::column:::
        [![API 图标 ][smtp-icon]<br> **SMTP**][smtp-doc]
    :::column-end:::
    :::column:::
        [![API 图标][sql-server-icon]<br>**SQL <br>Server**][sql-server-doc]
    :::column-end:::
    :::column:::
        [![API 图标 ][x12-icon]<br> **X12**][x12-doc]
    :::column-end:::
:::row-end:::

有关详细信息，请参阅以下主题：

* [从 Azure 逻辑应用访问 Azure 虚拟网络资源](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md)
* [逻辑应用定价模型](../logic-apps/logic-apps-pricing.md)
* [从 Azure 逻辑应用连接到 Azure 虚拟网络](../logic-apps/connect-virtual-network-vnet-isolated-environment.md)

<a name="triggers-actions"></a>

## <a name="triggers-and-action-types"></a>触发器和操作类型

连接器可以提供触发器和/或操作。  触发器是任何逻辑应用中的第一个步骤，它通常指定激发触发器并开始运行逻辑应用的事件。 例如，FTP 连接器中的某个触发器会在“添加或修改某个文件时”启动逻辑应用。 某些触发器定期检查指定的事件或数据，并在检测到指定的事件或数据时激发。 其他触发器会保持等待状态，但一旦发生特定的事件或者有新的数据可用，就会立即激发。 触发器还会将任何所需数据传递给逻辑应用。 在整个工作流中，逻辑应用都可以读取和使用该数据。 例如，Office 365 Outlook connector 有一个触发器 "当收到新电子邮件时"，它可以将该电子邮件中的内容传递到逻辑应用的工作流中。

触发器激发后，Azure 逻辑应用将创建逻辑应用的实例，并开始运行逻辑应用工作流中的操作。 操作是激发触发器后的步骤，将执行逻辑应用工作流中的任务。 例如，可以创建一个逻辑应用来获取 SQL 数据库中的客户数据，并在后续的操作中处理该数据。

下面是 Azure 逻辑应用提供的常规类型的触发器：

* 重复周期触发器：此触发器按指定的计划运行，不与特定的服务或系统密切相关。

* *轮询触发器*：此触发器根据指定的计划定期轮询特定的服务或系统，检查是否有可用的新数据或者是否发生了特定的事件。 如果有可用的新数据或者发生了特定的事件，该触发器将创建并运行逻辑应用的新实例，该实例现在可以使用作为输入传递的数据。

* 推送触发器：此触发器等待并侦听新数据或事件的发生。 如果有可用的新数据或者发生了该事件，该触发器将创建并运行逻辑应用的新实例，该实例现在可以使用作为输入传递的数据。

<a name="connections"></a>

## <a name="connector-configuration"></a>连接器配置

每个连接器的触发器和操作提供其自身的属性，你可以配置这些属性。 许多连接器还要求先与目标服务或系统建立连接，并提供身份验证凭据或其他配置详细信息，然后，你才能在逻辑应用中使用某个触发器或操作。 例如，在你可以访问和使用你的 Office 365 Outlook 电子邮件帐户之前，你必须为与该帐户的连接授权。

对于使用 Azure Active Directory (Azure AD) OAuth 的连接器，创建连接意味着要登录到服务（例如 Office 365、Salesforce 或 GitHub），其中的访问令牌[已加密](../security/fundamentals/encryption-overview.md)并安全存储在 Azure 机密存储中。 其他连接器（例如 FTP 和 SQL）需要提供包含服务器地址、用户名和密码等配置详细信息的连接。 这些连接配置详细信息同样加密并安全存储。 详细了解 [Azure 中的加密](../security/fundamentals/encryption-overview.md)。

只要目标服务或系统允许，连接就可以访问该服务或系统。 对于使用 Azure AD OAuth 连接的服务（例如 Office 365 和 Dynamics），Azure 逻辑应用会无限期地刷新访问令牌。 其他服务可能会限制在不刷新令牌的情况下，Azure 逻辑应用能够使用该令牌多长时间。 一般情况下，某些操作（例如更改密码）会使所有访问令牌失效。

<a name="custom"></a>

## <a name="custom-apis-and-connectors"></a>自定义 API 和连接器

若要调用运行自定义代码或者无法作为连接器使用的 API，可以通过[创建自定义 API 应用](../logic-apps/logic-apps-create-api-app.md)来扩展逻辑应用平台。 还可以针对任何基于 REST 或 SOAP 的 API [创建自定义连接器](../logic-apps/custom-connector-overview.md)，使这些 API 可供 Azure 订阅中的任何逻辑应用使用。 若要使自定义 API 应用或连接器可供任何人在 Azure 中使用，可以[提交连接器进行 Microsoft 认证](/connectors/custom-connectors/submit-certification)。

> [!NOTE]
> 在 integration service 环境中部署和运行的逻辑应用 [ (ISE) ](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md) 可以直接访问 Azure 虚拟网络中的资源。 如果你有需要本地数据网关的自定义连接器，并且你在 ISE 外部创建了这些连接器，则 ISE 中的逻辑应用还可以使用这些连接器。
>
> 在 ISE 中创建的自定义连接器不适用于本地数据网关。 但是，这些连接器可以直接访问连接到托管 ISE 的 Azure 虚拟网络的本地数据源。 因此，在与这些资源通信时，ISE 中的逻辑应用最有可能不需要数据网关。
>
> 有关创建 ISEs 的详细信息，请参阅 [从 Azure 逻辑应用连接到 azure 虚拟网络](../logic-apps/connect-virtual-network-vnet-isolated-environment.md)。

<a name="block-connections"></a>

## <a name="block-creating-connections"></a>阻止创建连接

如果组织不允许使用 Azure 逻辑应用中的连接器连接到特定资源，则可以在逻辑应用工作流中使用 [Azure Policy](../governance/policy/overview.md) [阻止为特定连接器创建这些连接的功能](../logic-apps/block-connections-connectors.md)。 有关详细信息，请参阅[在 Azure 逻辑应用中阻止特定连接器创建的连接](../logic-apps/block-connections-connectors.md)。

## <a name="get-ready-for-deployment"></a>准备部署

尽管从逻辑应用中创建连接，但连接是使用其自己的资源定义分隔的 Azure 资源。 若要查看这些连接资源定义，可以 [通过使用 Azure 门户检查逻辑应用的资源组](../logic-apps/logic-apps-azure-resource-manager-templates-overview.md#view-resource-definitions) ，或将 [逻辑应用从 Azure 下载到 Visual Studio](../logic-apps/manage-logic-apps-with-visual-studio.md)中，这是创建最适用于部署的有效参数化逻辑应用模板的最简单方法。

## <a name="next-steps"></a>后续步骤

* 查看[完整连接器列表](/connectors)
* [创建第一个逻辑应用](../logic-apps/quickstart-create-first-logic-app-workflow.md)
* [为逻辑应用创建自定义连接器](/connectors/custom-connectors/)
* [为逻辑应用创建自定义 API](../logic-apps/logic-apps-create-api-app.md)

<!-- Built-ins icons -->
[azure-api-management-icon]: ./media/apis-list/azure-api-management.png
[azure-app-services-icon]: ./media/apis-list/azure-app-services.png
[azure-functions-icon]: ./media/apis-list/azure-functions.png
[azure-logic-apps-icon]: ./media/apis-list/azure-logic-apps.png
[batch-icon]: ./media/apis-list/batch.png
[condition-icon]: ./media/apis-list/condition.png
[data-operations-icon]: ./media/apis-list/data-operations.png
[date-time-icon]: ./media/apis-list/date-time.png
[for-each-icon]: ./media/apis-list/for-each-loop.png
[http-icon]: ./media/apis-list/http.png
[http-request-icon]: ./media/apis-list/request.png
[http-response-icon]: ./media/apis-list/response.png
[http-swagger-icon]: ./media/apis-list/http-swagger.png
[http-webhook-icon]: ./media/apis-list/http-webhook.png
[inline-code-icon]: ./media/apis-list/inline-code.png
[schedule-icon]: ./media/apis-list/recurrence.png
[scope-icon]: ./media/apis-list/scope.png
[switch-icon]: ./media/apis-list/switch.png
[terminate-icon]: ./media/apis-list/terminate.png
[until-icon]: ./media/apis-list/until.png
[variables-icon]: ./media/apis-list/variables.png

<!--Managed connector icons-->
[appfigures-icon]: ./media/apis-list/appfigures.png
[asana-icon]: ./media/apis-list/asana.png
[azure-automation-icon]: ./media/apis-list/azure-automation.png
[azure-blob-storage-icon]: ./media/apis-list/azure-blob-storage.png
[azure-cognitive-services-text-analytics-icon]: ./media/apis-list/azure-cognitive-services-text-analytics.png
[azure-cosmos-db-icon]: ./media/apis-list/azure-cosmos-db.png
[azure-data-lake-icon]: ./media/apis-list/azure-data-lake.png
[azure-devops-icon]: ./media/apis-list/azure-devops.png
[azure-document-db-icon]: ./media/apis-list/azure-document-db.png
[azure-event-grid-icon]: ./media/apis-list/azure-event-grid.png
[azure-event-grid-publish-icon]: ./media/apis-list/azure-event-grid-publish.png
[azure-event-hubs-icon]: ./media/apis-list/azure-event-hubs.png
[azure-file-storage-icon]: ./media/apis-list/azure-file-storage.png
[azure-key-vault-icon]: ./media/apis-list/azure-key-vault.png
[azure-ml-icon]: ./media/apis-list/azure-ml.png
[azure-monitor-logs-icon]: ./media/apis-list/azure-monitor-logs.png
[azure-queues-icon]: ./media/apis-list/azure-queues.png
[azure-resource-manager-icon]: ./media/apis-list/azure-resource-manager.png
[azure-service-bus-icon]: ./media/apis-list/azure-service-bus.png
[azure-sql-data-warehouse-icon]: ./media/apis-list/azure-sql-data-warehouse.png
[azure-table-storage-icon]: ./media/apis-list/azure-table-storage.png
[basecamp-3-icon]: ./media/apis-list/basecamp.png
[bitbucket-icon]: ./media/apis-list/bitbucket.png
[bitly-icon]: ./media/apis-list/bitly.png
[biztalk-server-icon]: ./media/apis-list/biztalk.png
[blogger-icon]: ./media/apis-list/blogger.png
[campfire-icon]: ./media/apis-list/campfire.png
[common-data-service-icon]: ./media/apis-list/common-data-service.png
[dynamics-365-financials-icon]: ./media/apis-list/dynamics-365-financials.png
[dynamics-365-operations-icon]: ./media/apis-list/dynamics-365-operations.png
[easy-redmine-icon]: ./media/apis-list/easyredmine.png
[file-system-icon]: ./media/apis-list/file-system.png
[ftp-icon]: ./media/apis-list/ftp.png
[github-icon]: ./media/apis-list/github.png
[google-calendar-icon]: ./media/apis-list/google-calendar.png
[google-drive-icon]: ./media/apis-list/google-drive.png
[google-sheets-icon]: ./media/apis-list/google-sheet.png
[google-tasks-icon]: ./media/apis-list/google-tasks.png
[hipchat-icon]: ./media/apis-list/hipchat.png
[ibm-3270-icon]: ./media/apis-list/ibm-3270.png
[ibm-db2-icon]: ./media/apis-list/ibm-db2.png
[ibm-informix-icon]: ./media/apis-list/ibm-informix.png
[ibm-mq-icon]: ./media/apis-list/ibm-mq.png
[insightly-icon]: ./media/apis-list/insightly.png
[instagram-icon]: ./media/apis-list/instagram.png
[instapaper-icon]: ./media/apis-list/instapaper.png
[jira-icon]: ./media/apis-list/jira.png
[mandrill-icon]: ./media/apis-list/mandrill.png
[mysql-icon]: ./media/apis-list/mysql.png
[office-365-outlook-icon]: ./media/apis-list/office-365.png
[onedrive-icon]: ./media/apis-list/onedrive.png
[onedrive-for-business-icon]: ./media/apis-list/onedrive-business.png
[oracle-db-icon]: ./media/apis-list/oracle-db.png
[outlook.com-icon]: ./media/apis-list/outlook.png
[pagerduty-icon]: ./media/apis-list/pagerduty.png
[pinterest-icon]: ./media/apis-list/pinterest.png
[postgre-sql-icon]: ./media/apis-list/postgre-sql.png
[project-online-icon]: ./media/apis-list/projecton-line.png
[redmine-icon]: ./media/apis-list/redmine.png
[salesforce-icon]: ./media/apis-list/salesforce.png
[sap-icon]: ./media/apis-list/sap.png
[send-grid-icon]: ./media/apis-list/sendgrid.png
[sftp-ssh-icon]: ./media/apis-list/sftp.png
[sharepoint-online-icon]: ./media/apis-list/sharepoint-online.png
[sharepoint-server-icon]: ./media/apis-list/sharepoint-server.png
[slack-icon]: ./media/apis-list/slack.png
[smartsheet-icon]: ./media/apis-list/smartsheet.png
[smtp-icon]: ./media/apis-list/smtp.png
[sparkpost-icon]: ./media/apis-list/sparkpost.png
[sql-server-icon]: ./media/apis-list/sql.png
[teradata-icon]: ./media/apis-list/teradata.png
[todoist-icon]: ./media/apis-list/todoist.png
[twilio-icon]: ./media/apis-list/twilio.png
[vimeo-icon]: ./media/apis-list/vimeo.png
[wordpress-icon]: ./media/apis-list/wordpress.png
[youtube-icon]: ./media/apis-list/youtube.png

<!-- Enterprise Integration Pack icons -->
[as2-icon]: ./media/apis-list/as2.png
[edifact-icon]: ./media/apis-list/edifact.png
[flat-file-encode-icon]: ./media/apis-list/flat-file-encoding.png
[flat-file-decode-icon]: ./media/apis-list/flat-file-decoding.png
[integration-account-icon]: ./media/apis-list/integration-account.png
[liquid-icon]: ./media/apis-list/liquid-transform.png
[x12-icon]: ./media/apis-list/x12.png
[xml-validate-icon]: ./media/apis-list/xml-validation.png
[xml-transform-icon]: ./media/apis-list/xsl-transform.png

<!--Other doc links-->
[gateway-doc]: ../logic-apps/logic-apps-gateway-connection.md "通过本地数据网关，从逻辑应用连接到本地数据源"

<!--Built-in doc links-->
[azure-api-management-doc]: ../api-management/get-started-create-service-instance.md "创建 Azure API 管理服务实例用于管理和发布 API"
[azure-app-services-doc]: ../logic-apps/logic-apps-custom-api-host-deploy-call.md "将逻辑应用与应用服务 API 应用集成"
[azure-functions-doc]: ../logic-apps/logic-apps-azure-functions.md "将逻辑应用与 Azure Functions 集成"
[batch-doc]: ../logic-apps/logic-apps-batch-process-send-receive-messages.md "以组或批的形式处理消息"
[condition-doc]: ../logic-apps/logic-apps-control-flow-conditional-statement.md "评估条件并根据条件是 true 还是 false 运行不同的操作"
[for-each-doc]: ../logic-apps/logic-apps-control-flow-loops.md#foreach-loop "对数组中的每个项执行相同的操作"
[http-doc]: ./connectors-native-http.md "从逻辑应用调用 HTTP 或 HTTPS 终结点"
[http-request-doc]: ./connectors-native-reqres.md "在逻辑应用中接收 HTTP 请求"
[http-response-doc]: ./connectors-native-reqres.md "响应来自逻辑应用的 HTTP 请求"
[http-swagger-doc]: ./connectors-native-http-swagger.md "从逻辑应用调用 REST 终结点"
[http-webhook-doc]: ./connectors-native-webhook.md "等待来自 HTTP 或 HTTPS 终结点的特定事件"
[inline-code-doc]: ../logic-apps/logic-apps-add-run-inline-code.md "从逻辑应用添加和运行 JavaScript 代码片段"
[nested-logic-app-doc]: ../logic-apps/logic-apps-http-endpoint.md "将逻辑应用与嵌套工作流集成"
[query-doc]: ../logic-apps/logic-apps-perform-data-operations.md#filter-array-action "通过查询操作选择和筛选数组"
[schedule-doc]: ../logic-apps/concepts-schedule-automated-recurring-tasks-workflows.md "按计划运行逻辑应用"
[schedule-delay-doc]: ./connectors-native-delay.md "延迟运行下一操作"
[schedule-delay-until-doc]: ./connectors-native-delay.md "延迟运行下一操作"
[schedule-recurrence-doc]:  ./connectors-native-recurrence.md "按重复计划运行逻辑应用"
[schedule-sliding-window-doc]: ./connectors-native-sliding-window.md "运行需要处理连续区块中的数据的逻辑应用"
[scope-doc]: ../logic-apps/logic-apps-control-flow-run-steps-group-scopes.md "将操作组织成组，以便在该组中的操作完成运行后获取这些操作的自身状态"
[switch-doc]: ../logic-apps/logic-apps-control-flow-switch-statement.md "将操作组织成分配有唯一值的案例。仅运行其值与表达式、对象或令牌的结果相匹配的案例。如果不存在任何匹配项，则运行默认案例"
[terminate-doc]: ../logic-apps/logic-apps-workflow-actions-triggers.md#terminate-action "停止或取消逻辑应用的正在运行的工作流"
[until-doc]: ../logic-apps/logic-apps-control-flow-loops.md#until-loop "重复操作，直到指定的条件为 true 或某个状态发生更改"
[data-operations-doc]: ../logic-apps/logic-apps-perform-data-operations.md "执行数据操作，如筛选数组或创建 CSV 和 HTML 表"
[variables-doc]: ../logic-apps/logic-apps-create-variables-store-values.md "使用变量执行操作，例如初始化、设置、递增、递减和追加到字符串或数组变量"

<!--Managed connector doc links-->
[azure-automation-doc]: /connectors/azureautomation/ "为云和本地基础结构创建和管理自动化作业"
[azure-blob-storage-doc]: ./connectors-create-api-azureblobstorage.md "使用 Azure Blob 存储连接器管理 Blob 容器中的文件"
[azure-cosmos-db-doc]: /connectors/documentdb/ "连接到 Azure Cosmos DB，以便访问文档和存储过程"
[azure-event-grid-doc]: ../event-grid/monitor-virtual-machine-changes-event-grid-logic-app.md "监视事件网格发布的事件（例如，当 Azure 资源或第三方资源发生更改时）"
[azure-event-hubs-doc]: ./connectors-create-api-azure-event-hubs.md "连接到 Azure 事件中心，以便在逻辑应用与事件中心之间接收和发送事件"
[azure-file-storage-doc]: /connectors/azurefile/ "连接到 Azure 存储帐户，以便创建、更新、获取和删除文件"
[azure-key-vault-doc]: /connectors/keyvault/ "连接到 Azure 密钥保管库，以便管理机密和密钥"
[azure-monitor-logs-doc]: /connectors/azuremonitorlogs/ "在 Log Analytics 工作区和 Application Insights 组件之间针对 Azure Monitor 日志运行查询"
[azure-queues-doc]: /connectors/azurequeues/ "连接到 Azure 存储帐户，以便创建和管理队列与消息"
[azure-service-bus-doc]: ./connectors-create-api-servicebus.md "从服务总线队列和主题发送消息，并从服务总线队列和订阅接收消息"
[azure-sql-data-warehouse-doc]: /connectors/sqldw/ "连接到 Azure SQL 数据仓库，以便查看数据"
[azure-table-storage-doc]: /connectors/azuretables/ "连接到 Azure 存储帐户，以便创建、更新和查询表与其他对象"
[biztalk-server-doc]: /connectors/biztalk/ "连接到 BizTalk Server，以便将基于 BizTalk 的应用程序与 Azure 逻辑应用一起运行"
[file-system-doc]: ../logic-apps/logic-apps-using-file-connector.md "连接到本地文件系统"
[ftp-doc]: ./connectors-create-api-ftp.md "连接到 FTP/FTPS 服务器以执行 FTP 任务，例如上传、获取、删除文件，等等"
[github-doc]: ./connectors-create-api-github.md "连接到 GitHub，对问题进行跟踪"
[google-calendar-doc]: ./connectors-create-api-googlecalendar.md "连接到 Google Calendar，以便管理日历"
[google-sheets-doc]: ./connectors-create-api-googlesheet.md "连接到 Google Sheets，以便修改工作表"
[google-tasks-doc]: ./connectors-create-api-googletasks.md "连接到 Google Tasks，以便管理任务"
[ibm-3270-doc]: ./connectors-run-3270-apps-ibm-mainframe-create-api-3270.md "连接到 IBM 大型机上的 3270 应用"
[ibm-db2-doc]: ./connectors-create-api-db2.md "连接到云中或本地的 IBM DB2。更新行、获取表等"
[ibm-informix-doc]: ./connectors-create-api-informix.md "连接到云中或本地的 Informix。读取行、列出表，等等"
[ibm-mq-doc]: ./connectors-create-api-mq.md "连接到本地或 Azure 中的 IBM MQ 以发送和接收消息"
[instagram-doc]: ./connectors-create-api-instagram.md "连接到 Instagram。触发事件或针对事件进行操作"
[mandrill-doc]: ./connectors-create-api-mandrill.md "连接到 Mandrill 进行通信"
[mysql-doc]: /connectors/mysql/ "连接到本地 MySQL 数据库，以便读取和写入数据"
[office-365-outlook-doc]: ./connectors-create-api-office365-outlook.md "连接到 Office 365 帐户，以便发送和接收电子邮件、管理日历和联系人，以及执行其他操作"
[onedrive-doc]: ./connectors-create-api-onedrive.md "连接到个人 Microsoft OneDrive，以便上传、删除、列出文件，等等"
[onedrive-for-business-doc]: ./connectors-create-api-onedriveforbusiness.md "连接到企业 Microsoft OneDrive，以便上传、删除、列出文件，等等"
[oracle-db-doc]: ./connectors-create-api-oracledatabase.md "连接到 Oracle 数据库，以便添加、插入、删除行，以及执行其他操作"
[outlook.com-doc]: ./connectors-create-api-outlook.md "连接到 Outlook 邮箱，以便管理电子邮件、日历、联系人，以及执行其他操作"
[postgre-sql-doc]: /connectors/postgresql/ "连接到 PostgreSQL 数据库，以便从表中读取数据"
[salesforce-doc]: ./connectors-create-api-salesforce.md "连接到 Salesforce 帐户。管理帐户、潜在客户、商机等"
[sap-connector-doc]: ../logic-apps/logic-apps-using-sap-connector.md "连接到本地 SAP 系统"
[sendgrid-doc]: ./connectors-create-api-sendgrid.md "连接到 SendGrid。发送电子邮件和管理收件人列表"
[sftp-ssh-doc]: ./connectors-sftp-ssh.md "使用 SSH 连接到 SFTP 帐户。上传、获取、删除文件，等等"
[sharepoint-server-doc]: ./connectors-create-api-sharepoint.md "连接到 SharePoint 本地服务器。管理文档、列出项，等等"
[sharepoint-online-doc]: ./connectors-create-api-sharepoint.md "连接到 SharePoint Online。管理文档、列出项，等等"
[slack-doc]: ./connectors-create-api-slack.md "连接到 Slack，并将消息发布到 Slack 通道"
[smtp-doc]: ./connectors-create-api-smtp.md "连接到 SMTP 服务器并发送带附件的电子邮件"
[sparkpost-doc]: ./connectors-create-api-sparkpost.md "连接到 SparkPost 进行通信"
[sql-server-doc]: ./connectors-create-api-sqlazure.md "连接到 Azure SQL 数据库或 SQL Server。在 SQL 数据库表中创建、更新、获取和删除条目"
[teradata-doc]: /connectors/teradata/ "连接到 Teradata 数据库以从表中读取数据"
[twilio-doc]: ./connectors-create-api-twilio.md "连接到 Twilio。发送和获取消息、获取可用号码，管理来电号码，等等"
[youtube-doc]: ./connectors-create-api-youtube.md "连接到 YouTube。管理视频和频道"

<!--Enterprise Intregation Pack doc links-->
[as2-doc]: ../logic-apps/logic-apps-enterprise-integration-as2.md "对使用 AS2 协议的消息进行编码和解码"
[edifact-doc]: ../logic-apps/logic-apps-enterprise-integration-edifact.md "对使用 EDIFACT 协议的消息进行编码和解码"
[edifact-decode-doc]: ../logic-apps/logic-apps-enterprise-integration-EDIFACT-decode.md "对使用 EDIFACT 协议的消息进行解码"
[edifact-encode-doc]: ../logic-apps/logic-apps-enterprise-integration-EDIFACT-encode.md "对使用 EDIFACT 协议的消息进行编码"
[flat-file-decode-doc]:../logic-apps/logic-apps-enterprise-integration-flatfile.md "了解企业集成平面文件"
[flat-file-encode-doc]:../logic-apps/logic-apps-enterprise-integration-flatfile.md "了解企业集成平面文件"
[integration-account-doc]: ../logic-apps/logic-apps-enterprise-integration-metadata.md "管理集成帐户项目的元数据"
[json-liquid-transform-doc]: ../logic-apps/logic-apps-enterprise-integration-liquid-transform.md "使用 Liquid 模板转换 JSON"
[x12-doc]: ../logic-apps/logic-apps-enterprise-integration-x12.md "对使用 X12 协议的消息进行编码和解码"
[x12-decode-doc]: ../logic-apps/logic-apps-enterprise-integration-X12-decode.md "对使用 X12 协议的消息进行解码"
[x12-encode-doc]: ../logic-apps/logic-apps-enterprise-integration-X12-encode.md "对使用 X12 协议的消息进行编码"
[xml-transform-doc]: ../logic-apps/logic-apps-enterprise-integration-transform.md "转换 XML 消息"
[xml-validate-doc]: ../logic-apps/logic-apps-enterprise-integration-xml-validation.md "验证 XML 消息"
