---
title: Azure 逻辑应用的连接器
description: 使用 Azure 逻辑应用的连接器（如内置、托管、本地、集成帐户、ISE 和企业连接器）自动执行工作流
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: article
ms.date: 03/05/2020
ms.openlocfilehash: 3010f3c99a5b214c2503f890321cbb73427e3c20
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "79247327"
---
# <a name="connectors-for-azure-logic-apps"></a>Azure 逻辑应用的连接器

使用连接器可以从 Azure 逻辑应用快速访问其他应用、服务、系统、协议和平台的事件、数据和操作。 使用逻辑应用中连接器，可以扩展云和本地应用的功能，以便对创建的数据和现有的数据执行任务。

虽然逻辑应用提供了[数百个连接器](https://docs.microsoft.com/connectors)，本文描述了*流行的和最常用的*连接器，这些连接器被数千个应用成功使用，数百万次执行用于处理数据和信息。 若要查找连接器的完整列表以及每个连接器的参考信息（例如触发器、操作和限制），请查看[连接器概述](https://docs.microsoft.com/connectors)下的连接器参考页。 另外，请详细了解[触发器和操作](#triggers-actions)、[逻辑应用定价模型](../logic-apps/logic-apps-pricing.md)以及[逻辑应用定价详细信息](https://azure.microsoft.com/pricing/details/logic-apps/)。

> [!TIP]
> 要与没有连接器的服务或 API 集成，可以通过 HTTP 等协议直接调用服务，也可以创建[自定义连接器](#custom)。

## <a name="connector-types"></a>连接器类型

连接器可作为内置触发器和操作或托管连接器提供。

<a name="built-in"></a>

* [**内置**](#built-ins)： 内置触发器和操作是 Azure 逻辑应用的"本机"，可帮助您为逻辑应用执行这些任务：

  * 按照自定义和高级计划运行。

  * 组织和控制逻辑应用的工作流，例如循环和条件，以及处理变量和数据操作。

  * 与其他终结点通信。

  * 接收和响应请求。

  * 调用 Azure 函数、Azure API 应用（Web 应用）、使用 Azure API 管理管理和发布的自己的 API 以及可以接收请求的嵌套逻辑应用。

<a name="managed-connectors"></a>

* [**托管连接器**](#managed-api-connectors)：由 Microsoft 部署和管理，这些连接器提供触发器和操作，用于访问云服务、本地系统或两者，包括 Office 365、Azure Blob 存储、SQL Server、动态、销售力量、SharePoint 等。 某些连接器专门支持企业到企业 (B2B) 通信方案，需要一个与逻辑应用链接的[集成帐户](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md)。 在使用某些连接器之前，可能需要先创建由 Azure 逻辑应用管理的连接。

  例如，如果你使用 Microsoft BizTalk Server，则逻辑应用可以使用 [BizTalk Server 本地连接器](#on-premises-connectors)连接到 BizTalk Server 并与其通信。 然后，可以使用[集成帐户连接器](#integration-account-connectors)在逻辑应用中扩展或执行类似于 BizTalk 的操作。

  连接器分类为“标准”或“企业”连接器。 [企业连接器](#enterprise-connectors)提供对 SAP、IBM MQ 和 IBM 3270 等企业系统的访问，但会产生额外的费用。 若要确定某个连接器是标准还是企业连接器，请参阅[连接器概述](https://docs.microsoft.com/connectors)下每个连接器的参考页中的技术详细信息。

  也可以使用这些类别来识别连接器，不过，某些连接器可能属于多个类别。 例如，SAP 既是企业连接器，也是本地连接器：

  |   |   |
  |---|---|
  | [**托管的连接器**](#managed-api-connectors) | 创建使用 Azure Blob 存储、Office 365、Dynamics、Power BI、OneDrive、Salesforce、SharePoint Online 等服务的逻辑应用。 |
  | [**本地连接器**](#on-premises-connectors) | 安装并设置[本地数据网关][gateway-doc]后，这些连接器可以帮助逻辑应用访问 SQL Server、SharePoint Server、Oracle DB、文件共享等本地系统。 |
  | [**集成帐户连接器**](#integration-account-connectors) | 创建和付费购买集成帐户时可以使用这些连接器，它们会转换和验证 XML、编码和解码平面文件，以及使用 AS2、EDIFACT 和 X12 协议处理企业到企业 (B2B) 消息。 |
  |||

<a name="integration-service-environment"></a>

### <a name="connect-from-an-integration-service-environment"></a>从集成服务环境进行连接

对于需要直接访问 Azure 虚拟网络中资源的逻辑应用，您可以创建一个隔离[的集成服务环境 （ISE），](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md)您可以在其中在专用资源上生成、部署和运行逻辑应用。 在逻辑应用设计器中，当您浏览要用于 ISE 中的逻辑应用的连接器时，内置触发器和操作上会出现**CORE**标签，而**ISE**标签出现在某些连接器上：

* **CORE**： 内置触发器和操作，具有此标签，在与逻辑应用相同的 ISE 中运行，例如：

  ![示例 ISE 连接器](./media/apis-list/example-core-connector.png)

* **ISE**： 具有此标签的托管连接器与逻辑应用在 ISE 中运行，例如：

  ![示例 ISE 连接器](./media/apis-list/example-ise-connector.png)

  如果有连接到 Azure 虚拟网络的本地系统，ISE 允许逻辑应用在没有[本地数据网关](../logic-apps/logic-apps-gateway-connection.md)的情况下直接访问该系统。 相反，您可以使用该系统的**ISE**连接器（如果可用）、HTTP 操作或[自定义连接器](#custom)。 对于没有**ISE**连接器的本地系统，请使用本地数据网关。 要查看可用的 ISE 连接器，请参阅[ISE 连接器](#ise-connectors)。

* 在全局多租户逻辑应用服务中运行没有**CORE**或**ISE**标签的所有其他连接器（可以继续使用）：

  ![多租户连接器示例](./media/apis-list/example-multi-tenant-connector.png)

在 ISE 及其连接器中运行的逻辑应用（无论这些连接器在何处运行）遵循固定定价计划，而不是基于消耗的定价计划。 有关详细信息，请参阅以下页面：

* [逻辑应用定价模型](../logic-apps/logic-apps-pricing.md)
* [逻辑应用定价详细信息](https://azure.microsoft.com/pricing/details/logic-apps/)
* [从 Azure 逻辑应用连接到 Azure 虚拟网络](../logic-apps/connect-virtual-network-vnet-isolated-environment.md)

<a name="built-ins"></a>

## <a name="built-in"></a>内置

逻辑应用提供内置触发器和操作，以便您可以创建基于计划的工作流、帮助逻辑应用与其他应用和服务通信、通过逻辑应用控制工作流以及管理或操作数据。

|   |   |   |   |
|---|---|---|---|
| [![API][schedule-icon]<br>图标**计划**][schedule-doc] | - 在指定的定期事件上运行逻辑应用，从基本计划到高级计划（使用[**定期**触发器][schedule-recurrence-doc]）不等。 <p>- 运行一个逻辑应用程序，需要处理连续块中的数据与[**滑动窗口**触发器][schedule-sliding-window-doc]。 <p>- 使用[**"延迟**"操作][schedule-delay-doc]暂停逻辑应用指定持续时间。 <p>- 暂停逻辑应用，直到指定的日期和时间与[**延迟，直到**操作][schedule-delay-until-doc]。 | [![API][batch-icon]<br>图标**批处理**][batch-doc] | - 使用“批处理消息”触发器来批量处理消息。**** <p>- 使用“发送要批量处理的消息”操作调用具有现有批处理触发器的逻辑应用。**** |
| [![API][http-icon]<br>图标**HTTP**][http-doc] | 使用 HTTP 的触发器和操作调用 HTTP 或 HTTPS 终结点。 其他 HTTP 内置触发器和操作包括[HTTP + 斯瓦格][http-swagger-doc]和[HTTP = Webhook][http-webhook-doc]。 | [![API][http-request-icon]<br>图标**请求**][http-request-doc] | - 使用“请求”触发器，使逻辑应用可从其他应用程序或服务调用、针对事件网格资源事件触发，或者针对 Azure 安全中心警报触发。**** <p>- 使用“响应”操作将响应发送到应用或服务。**** |
| [![API][azure-api-management-icon]<br>图标**Azure API<br>管理**][azure-api-management-doc] | 调用可以使用 Azure API 管理进行管理和发布的自有 API 定义的触发器与操作。 | [![API][azure-app-services-icon]<br>图标**Azure 应用<br>服务**][azure-app-services-doc] | 调用 Azure 应用服务上托管的 Azure API 应用或 Web 应用。 包含 Swagger 后，这些应用定义的触发器和操作类似于其他任何第一类触发器和操作。|
| [![API][azure-logic-apps-icon]<br>图标**Azure 逻辑<br>应用**][nested-logic-app-doc] | 调用以 **"请求"** 触发器开头的其他逻辑应用。 |
|||||

### <a name="run-code-from-logic-apps"></a>从逻辑应用运行代码

逻辑应用提供内置操作，用于在逻辑应用的工作流中运行您自己的代码：

|   |   |   |   |
|---|---|---|---|
| [![API][azure-functions-icon]<br>图标**Azure 函数**][azure-functions-doc] | 从逻辑应用调用运行自定义代码片段（C# 或 Node.js）的 Azure 函数。 | [![API][inline-code-icon]<br>图标**内联代码**][azure-functions-doc] | 从逻辑应用添加和运行 JavaScript 代码段。 |
|||||

### <a name="control-workflow"></a>控制工作流

逻辑应用提供用于在逻辑应用工作流中构建和控制操作的内置操作：

|   |   |   |   |
|---|---|---|---|
| [![内置图标][condition-icon]<br>**条件**][condition-doc] | 评估条件，并根据条件是 true 还是 false 运行不同的操作。 | [![][for-each-icon]<br>**每个**的内置图标][for-each-doc] | 对数组中的每个项执行相同的操作。 |
| [![内置图标][scope-icon]<br>**范围**][scope-doc] | 将操作分组到范围，以便在该范围内的操作完成运行后，获取这些操作的自身状态。** | [![内置图标][switch-icon]<br>**开关**][switch-doc] | 将操作分组到案例，而案例分配有唯一的值（默认案例除外）。** 仅运行其分配值与表达式、对象或令牌的结果相匹配的案例。 如果不存在任何匹配项，则运行默认案例。 |
| [![内置图标][terminate-icon]<br>**终止**][terminate-doc] | 停止当前正在运行的逻辑应用工作流。 | [![内置图标][until-icon]<br>**，直到**][until-doc] | 重复操作，直到指定的条件为 true 或某个状态发生更改。 |
|||||

### <a name="manage-or-manipulate-data"></a>管理或处理数据

逻辑应用提供用于处理数据输出及其格式的内置操作：

|   |   |
|---|---|
| [![内置图标][data-operations-icon]<br>**数据操作**][data-operations-doc] | 对数据执行操作： <p>- **组合**：基于具有不同类型的多个输入创建单个输出。 <br>- **创建 CSV 表**：基于包含 JSON 对象的数组创建逗号分隔值 (CSV) 表。 <br>- **创建 HTML 表**：基于包含 JSON 对象的数组创建一个 HTML 表。 <br>- **筛选数组**：基于符合条件的另一个数组中的项创建一个数组。 <br>- **联接**：基于数组中的所有项创建一个字符串，并使用指定的分隔符分隔这些项。 <br>- **分析 JSON**：从 JSON 内容中的属性及其值创建用户友好的令牌，以便在工作流中使用这些属性。 <br>- **选择**：通过转换另一数组中的项或值并将这些项映射到指定的属性，创建包含 JSON 对象的数组。 |
| ![内置操作图标][date-time-icon]<br>**日期时间** | 对时间戳执行操作： <p>- **添加到时间**：将指定的单位数添加到时间戳。 <br>- **转换时区**：将时间戳从源时区转换为目标时区。 <br>- **当前时间**：返回字符串形式的当前时间戳。 <br>- **获取将来的时间**：返回当前时间戳加上指定的时间单位。 <br>- **获取过去的时间**：返回当前时间戳减去指定的时间单位。 <br>- **从时间减去**：从时间戳中减去一定数目的时间单位。 |
| [![内置图标][variables-icon]<br>**变量**][variables-doc] | 对变量执行操作： <p>- **追加到数组变量**：插入一个值，作为变量存储的数组中的最后一个项。 <br>- **追加到字符串变量**：插入一个值，作为变量存储的字符串中的最后一个字符。 <br>- **递减变量**：按常量值减小变量。 <br>- **递增变量**：按常量值增大变量。 <br>- **初始化变量**：创建一个变量并声明其数据类型和初始值。 <br>- **设置变量**：将不同的值分配给现有变量。 |
|  |  |

<a name="managed-api-connectors"></a>

## <a name="managed-connectors"></a>托管的连接器

逻辑应用提供这些流行的标准连接器，用于通过这些服务或系统自动执行任务、流程和工作流：

|   |   |   |   |
|---|---|---|---|
| [![API][azure-service-bus-icon]<br>图标**Azure 服务总线**][azure-service-bus-doc] | 使用逻辑应用中最常用的连接器管理异步消息、会话和主题订阅。 | [![API][sql-server-icon]<br>图标**SQL 服务器**][sql-server-doc] | 在内部连接到 SQL Server 或云中的 Azure SQL 数据库，以便可以管理记录、运行存储过程或执行查询。 |
| [![API][azure-blob-storage-icon]<br>图标**Azure Blob<br>存储**][azure-blob-storage-doc] | 连接到存储帐户，以便您可以创建和管理 Blob 内容。 | [![API][office-365-outlook-icon]<br>图标**Office<br>365 展望**][office-365-outlook-doc] | 连接到 Office 365 电子邮件帐户，以便您可以创建和管理电子邮件、任务、日历事件和会议、联系人、请求等。 |
| [![API][sftp-ssh-icon]<br>图标**SFTP-SSH**][sftp-ssh-doc] | 连接到 SFTP 服务器，您可以使用 SSH 从 Internet 访问这些服务器，以便可以使用文件和文件夹。 | [![API][sharepoint-online-icon]<br>图标**共享点<br>在线**][sharepoint-online-doc] | 联机连接到 SharePoint，以便可以管理文件、附件、文件夹等。 | 
| [![API][dynamics-365-icon]<br>图标**动态 365<br> **][dynamics-365-doc] | 连接到 Dynamics 365 帐户，以便您可以创建和管理记录、项目等。 | [![API][azure-queues-icon]<br>图标**Azure<br>队列**][azure-queues-doc] | 连接到 Azure 存储帐户，以便创建和管理队列和消息 |
| [![API][ftp-icon]<br>图标**FTP**][ftp-doc] | 连接到 FTP 服务器，您可以从 Internet 访问，以便您可以使用文件和文件夹。 | [![API][file-system-icon]<br>**图标<br>文件系统**][file-system-doc] | 连接到本地文件共享，以便您可以创建和管理文件。 |
| [![API][azure-event-hubs-icon]<br>图标**Azure 事件中心**][azure-event-hubs-doc] | 通过事件中心使用和发布事件。 例如，通过事件中心获取逻辑应用的输出，然后将输出发送到实时分析提供程序。 | [![API][azure-event-grid-icon]<br>图标**Azure 事件**<br>**网格**][azure-event-grid-doc] | 监视事件网格发布的事件（例如，当 Azure 资源或第三方资源发生更改时）。 |
| [![API][salesforce-icon]<br>图标**销售队伍**][salesforce-doc] | 连接到 Salesforce 帐户，以便您可以创建和管理记录、作业、对象等项目。 | [![API图标][twitter-icon]<br>**推特**][twitter-doc] | 连接到您的 Twitter 帐户，以便您可以管理推文、关注者、时间线等。 将推文保存到 SQL、Excel 或 SharePoint。 |
|||||

<a name="on-premises-connectors"></a>

## <a name="on-premises-connectors"></a>本地连接器

逻辑应用提供下面这些常用的标准连接器用于访问本地系统中的数据和资源。 在与本地系统建立连接之前，必须先[下载、安装并设置本地数据网关][gateway-doc]。 此网关提供安全信道，无需设置所需的网络基础结构。

|   |   |   |   |   |
|---|---|---|---|---|
| [![API][biztalk-server-icon]<br>图标**BizTalk** <br>**服务器**][biztalk-server-doc] | [![API][file-system-icon]<br>**图标<br>文件系统**][file-system-doc] | [![API][ibm-db2-icon]<br>图标**IBM DB2**][ibm-db2-doc] | [![API][ibm-informix-icon]<br>图标**IBM** <br> **Informix**][ibm-informix-doc] | [![API][mysql-icon]<br>图标**MySQL**][mysql-doc] |
| [![API][oracle-db-icon]<br>图标**Oracle DB**][oracle-db-doc] | [![API][postgre-sql-icon]<br>图标**PostgreSQL**][postgre-sql-doc] | [![API][sharepoint-server-icon]<br>图标**共享<br>点服务器**][sharepoint-server-doc] | [![API][sql-server-icon]<br>图标**SQL<br>服务器**][sql-server-doc] | [![API][teradata-icon]<br>图标**Teradata**][teradata-doc] |
|||||

<a name="integration-account-connectors"></a>

## <a name="integration-account-connectors"></a>集成帐户连接器

逻辑应用提供标准连接器，用于在创建和付费购买[集成帐户](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md)（通过 Azure 中的 Enterprise Integration Pack (EIP) 提供）时，在逻辑应用中生成企业到企业 (B2B) 解决方案。 使用此帐户可以创建和存储 B2B 项目，例如贸易合作伙伴、协议、映射、架构、证书，等等。 若要使用这些项目，请将逻辑应用与集成帐户相关联。 如果你当前使用的是 BizTalk Server，则可能已熟悉这些连接器的用法。

|   |   |   |   |
|---|---|---|---|
| [![API][as2-icon]<br>图标**AS2<br>解码**][as2-doc] | [![API][as2-icon]<br>图标**AS2<br>编码**][as2-doc] | [![API][edifact-icon]<br>图标**EDIFACT<br>解码**][edifact-decode-doc] | [![API][edifact-icon]<br>图标**EDIFACT<br>编码**][edifact-encode-doc] |
| [![API][flat-file-decode-icon]<br>图标**平面文件<br>解码**][flat-file-decode-doc] | [![API][flat-file-encode-icon]<br>图标**平面文件<br>编码**][flat-file-encode-doc] | [![API][integration-account-icon]<br>**图标<br>集成帐户**][integration-account-doc] | [![API][liquid-icon]<br>图标**液体**<br>**转换**][json-liquid-transform-doc] |
| [![API][x12-icon]<br>图标**X12<br>解码**][x12-decode-doc] | [![API][x12-icon]<br>图标**X12<br>编码**][x12-encode-doc] | [![API][xml-transform-icon]<br>图标**XML** <br>**转换**][xml-transform-doc] | [![API][xml-validate-icon]<br>图标**XML<br>验证**][xml-validate-doc] |  
|||||

<a name="enterprise-connectors"></a>

## <a name="enterprise-connectors"></a>企业连接器

逻辑应用提供以下用于访问 SAP 和 IBM MQ 等企业系统的企业连接器：

|   |   |   |
|---|---|---|
| [![API][ibm-3270-icon]<br>图标**IBM 3270**][ibm-3270-doc] | [![API][ibm-mq-icon]<br>图标**IBM MQ**][ibm-mq-doc] | [![API][sap-icon]<br>图标**SAP**][sap-connector-doc] |
||||

<a name="ise-connectors"></a>

## <a name="ise-connectors"></a>ISE 连接器

对于创建和运行隔离[集成服务环境 （ISE）](#integration-service-environment)的逻辑应用，逻辑应用设计器使用**CORE**标签标识在 ISE 中运行的内置触发器和操作。 在 ISE 中运行的托管连接器显示**ISE**标签，而在全局多租户逻辑应用服务中运行的连接器不显示任一标签。 此列表显示当前具有 ISE 版本的连接器：

|   |   |   |   |   |
|---|---|---|---|---|
[![API][as2-icon]<br>图标**AS2**][as2-doc] | [![API][azure-blob-storage-icon]<br>图标**Azure Blob<br>存储**][azure-blob-storage-doc] | [![API][azure-cosmos-db-icon]<br>图标**Azure<br>宇宙 DB**][azure-cosmos-db-doc] | [![API][azure-event-hubs-icon]<br>图标**Azure 事件<br>中心**][azure-event-hubs-doc] | [![API][azure-file-storage-icon]<br>图标**Azure 文件<br>存储**][azure-file-storage-doc] |
| [![API][azure-service-bus-icon]<br>图标**Azure 服务<br>总线**][azure-service-bus-doc] | [![API][azure-sql-data-warehouse-icon]<br>图标**Azure <br>SQL 数据仓库**][azure-sql-data-warehouse-doc] | [![API][azure-table-storage-icon]<br>图标**Azure 表<br>存储**][azure-table-storage-doc] | [![API][azure-queues-icon]<br>图标**Azure<br>队列**][azure-queues-doc] | [![API][edifact-icon]<br>图标**EDIFACT**][edifact-doc] |
| [![API][file-system-icon]<br>**图标<br>文件系统**][file-system-doc] | [![API][ftp-icon]<br>图标**FTP**][ftp-doc] | [![API][ibm-3270-icon]<br>图标**IBM 3270**][ibm-3270-doc] | [![API][ibm-db2-icon]<br>图标**IBM DB2**][ibm-db2-doc] | [![API][ibm-mq-icon]<br>图标**IBM MQ**][ibm-mq-doc] |
| [![API][sap-icon]<br>图标**SAP**][sap-connector-doc] | [![API][sftp-ssh-icon]<br>图标**SFTP-SSH**][sftp-ssh-doc] | [![API][smtp-icon]<br>图标**SMTP**][smtp-doc] | [![API][sql-server-icon]<br>图标**SQL<br>服务器**][sql-server-doc] | [![API][x12-icon]<br>图标**X12**][x12-doc] |
||||||

有关详细信息，请参阅以下主题：

* [从 Azure 逻辑应用访问 Azure 虚拟网络资源](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md)
* [逻辑应用定价模型](../logic-apps/logic-apps-pricing.md)
* [从 Azure 逻辑应用连接到 Azure 虚拟网络](../logic-apps/connect-virtual-network-vnet-isolated-environment.md)

<a name="triggers-actions"></a>

## <a name="triggers-and-action-types"></a>触发器和操作类型

连接器可以提供触发器和/或操作。**** 触发器是任何逻辑应用中的第一个步骤，它通常指定激发触发器并开始运行逻辑应用的事件。** 例如，FTP 连接器中的某个触发器会在“添加或修改某个文件时”启动逻辑应用。 某些触发器定期检查指定的事件或数据，并在检测到指定的事件或数据时激发。 其他触发器会保持等待状态，但一旦发生特定的事件或者有新的数据可用，就会立即激发。 触发器还会将任何所需数据传递给逻辑应用。 在整个工作流中，逻辑应用都可以读取和使用该数据。 例如，Twitter 连接器包含一个触发器“发布新推文时”，该触发器会将推文内容传入逻辑应用的工作流。

触发器激发后，Azure 逻辑应用将创建逻辑应用的实例，并开始运行逻辑应用工作流中的操作。** 操作是激发触发器后的步骤，将执行逻辑应用工作流中的任务。 例如，可以创建一个逻辑应用来获取 SQL 数据库中的客户数据，并在后续的操作中处理该数据。

下面是 Azure 逻辑应用提供的常规类型的触发器：

* *定期触发器*：此触发器按指定计划运行，并且不与特定服务或系统紧密关联。

* *轮询触发器*：此触发器根据指定的计划定期轮询特定服务或系统，检查新数据或是否发生了特定事件。 如果有可用的新数据或者发生了特定的事件，该触发器将创建并运行逻辑应用的新实例，该实例现在可以使用作为输入传递的数据。

* *推送触发器*：此触发器等待并侦听新数据或事件发生。 如果有可用的新数据或者发生了该事件，该触发器将创建并运行逻辑应用的新实例，该实例现在可以使用作为输入传递的数据。

<a name="connections"></a>

## <a name="connector-configuration"></a>连接器配置

每个连接器的触发器和操作提供其自身的属性，你可以配置这些属性。 许多连接器还要求先与目标服务或系统建立连接，并提供身份验证凭据或其他配置详细信息，然后，你才能在逻辑应用中使用某个触发器或操作。** 例如，必须授权与 Twitter 帐户建立连接才能访问数据，或者让系统代表你发贴。

对于使用 Azure 活动目录 （Azure AD） OAuth 的连接器，创建连接意味着登录到服务，如 Office 365、Salesforce 或 GitHub，其中访问令牌[已加密](../security/fundamentals/encryption-overview.md)并安全地存储在 Azure 密钥存储中。 其他连接器（例如 FTP 和 SQL）需要提供包含服务器地址、用户名和密码等配置详细信息的连接。 这些连接配置详细信息同样加密并安全存储。 了解有关[Azure 中的加密](../security/fundamentals/encryption-overview.md)的更多详细信息。

只要目标服务或系统允许，连接就可以访问该服务或系统。 对于使用 Azure AD OAuth 连接的服务（如 Office 365 和动态），Azure 逻辑应用将无限期地刷新访问令牌。 其他服务可能会限制在不刷新令牌的情况下，Azure 逻辑应用能够使用该令牌多长时间。 一般情况下，某些操作（例如更改密码）会使所有访问令牌失效。

<a name="custom"></a>

## <a name="custom-apis-and-connectors"></a>自定义 API 和连接器

若要调用运行自定义代码或者无法作为连接器使用的 API，可以通过[创建自定义 API 应用](../logic-apps/logic-apps-create-api-app.md)来扩展逻辑应用平台。 还可以针对任何基于 REST 或 SOAP 的 API [创建自定义连接器](../logic-apps/custom-connector-overview.md)，使这些 API 可供 Azure 订阅中的任何逻辑应用使用。** 若要使自定义 API 应用或连接器可供任何人在 Azure 中使用，可以[提交连接器进行 Microsoft 认证](../logic-apps/custom-connector-submit-certification.md)。

> [!NOTE]
> 在[集成服务环境 （ISE）](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md)中部署和运行的逻辑应用可以直接访问 Azure 虚拟网络中的资源。 如果您有需要本地数据网关的自定义连接器，并且在 ISE 之外创建了这些连接器，则 ISE 中的逻辑应用也可以使用这些连接器。
>
> 在 ISE 中创建的自定义连接器与本地数据网关不起作用。 但是，这些连接器可以直接访问连接到托管 ISE 的 Azure 虚拟网络的本地数据源。 因此，在与这些资源通信时，ISE 中的逻辑应用很可能不需要数据网关。
>
> 有关创建 ISE 的详细信息，请参阅[从 Azure 逻辑应用连接到 Azure 虚拟网络](../logic-apps/connect-virtual-network-vnet-isolated-environment.md)。

## <a name="next-steps"></a>后续步骤

* 查看[完整的连接器列表](https://docs.microsoft.com/connectors)
* [创建第一个逻辑应用](../logic-apps/quickstart-create-first-logic-app-workflow.md)
* [为逻辑应用创建自定义连接器](https://docs.microsoft.com/connectors/custom-connectors/)
* [为逻辑应用创建自定义 API](../logic-apps/logic-apps-create-api-app.md)

<!--Misc doc links-->
[gateway-doc]: ../logic-apps/logic-apps-gateway-connection.md "通过本地数据网关，从逻辑应用连接到本地数据源"

<!--Built-in doc links-->
[azure-api-management-doc]: ../api-management/get-started-create-service-instance.md "创建 Azure API 管理服务实例用于管理和发布 API"
[azure-app-services-doc]: ../logic-apps/logic-apps-custom-hosted-api.md "将逻辑应用与应用服务 API 应用集成"
[azure-functions-doc]: ../logic-apps/logic-apps-azure-functions.md "将逻辑应用与 Azure Functions 集成"
[batch-doc]: ../logic-apps/logic-apps-batch-process-send-receive-messages.md "以组或批的形式处理消息"
[condition-doc]: ../logic-apps/logic-apps-control-flow-conditional-statement.md "根据条件是真还是假评估条件并运行不同的操作"
[for-each-doc]: ../logic-apps/logic-apps-control-flow-loops.md#foreach-loop "对数组中的每个项执行相同的操作"
[http-doc]: ./connectors-native-http.md "从逻辑应用调用 HTTP 或 HTTPS 终结点"
[http-request-doc]: ./connectors-native-reqres.md "在逻辑应用中接收 HTTP 请求"
[http-response-doc]: ./connectors-native-reqres.md "响应来自逻辑应用的 HTTP 请求"
[http-swagger-doc]: ./connectors-native-http-swagger.md "从逻辑应用调用 REST 终结点"
[http-webhook-doc]: ./connectors-native-webhook.md "等待来自 HTTP 或 HTTPS 终结点的特定事件"
[nested-logic-app-doc]: ../logic-apps/logic-apps-http-endpoint.md "将逻辑应用与嵌套工作流集成"
[query-doc]: ../logic-apps/logic-apps-perform-data-operations.md#filter-array-action "通过查询操作选择和筛选数组"
[schedule-doc]: ../logic-apps/concepts-schedule-automated-recurring-tasks-workflows.md "基于计划运行逻辑应用"
[schedule-delay-doc]: ./connectors-native-delay.md "延迟运行下一个操作"
[schedule-delay-until-doc]: ./connectors-native-delay.md "延迟运行下一个操作"
[schedule-recurrence-doc]:  ./connectors-native-recurrence.md "定期计划运行逻辑应用"
[schedule-sliding-window-doc]: ./connectors-native-sliding-window.md "运行需要连续处理数据的逻辑应用"
[scope-doc]: ../logic-apps/logic-apps-control-flow-run-steps-group-scopes.md "将操作组织成组，以便在该组中的操作完成运行后获取这些操作的自身状态"
[switch-doc]: ../logic-apps/logic-apps-control-flow-switch-statement.md "将操作组织到分配给唯一值的案例中。仅运行其值与表达式、对象或令牌的结果匹配的案例。如果不存在匹配项，请运行默认大小写"
[terminate-doc]: ../logic-apps/logic-apps-workflow-actions-triggers.md#terminate-action "停止或取消逻辑应用的正在运行的工作流"
[until-doc]: ../logic-apps/logic-apps-control-flow-loops.md#until-loop "重复操作，直到指定条件为 true 或某些状态已更改"
[data-operations-doc]: ../logic-apps/logic-apps-perform-data-operations.md "执行数据操作，如筛选数组或创建 CSV 和 HTML 表"
[variables-doc]: ../logic-apps/logic-apps-create-variables-store-values.md "使用变量执行操作，例如初始化、设置、递增、递减和追加到字符串或数组变量"

<!--Managed connector doc links-->
[azure-blob-storage-doc]: ./connectors-create-api-azureblobstorage.md "使用 Azure Blob 存储连接器管理 Blob 容器中的文件"
[azure-cosmos-db-doc]: https://docs.microsoft.com/connectors/documentdb/ "连接到 Azure Cosmos DB，以便可以访问文档和存储过程"
[azure-event-grid-doc]: ../event-grid/monitor-virtual-machine-changes-event-grid-logic-app.md "监视事件网格发布的事件，例如，当 Azure 资源或第三方资源发生更改时"
[azure-event-hubs-doc]: ./connectors-create-api-azure-event-hubs.md "连接到 Azure 事件中心，以便在逻辑应用和事件中心之间接收和发送事件"
[azure-file-storage-doc]: https://docs.microsoft.com/connectors/azurefile/ "连接到 Azure 存储帐户，以便创建、更新、获取和删除文件"
[azure-queues-doc]: https://docs.microsoft.com/connectors/azurequeues/ "连接到 Azure 存储帐户，以便创建和管理队列和消息"
[azure-service-bus-doc]: ./connectors-create-api-servicebus.md "从服务总线队列和主题发送消息，并从服务总线队列和订阅接收消息"
[azure-sql-data-warehouse-doc]: https://docs.microsoft.com/connectors/sqldw/ "连接到 Azure SQL 数据仓库，以便查看数据"
[azure-table-storage-doc]: https://docs.microsoft.com/connectors/azuretables/ "连接到 Azure 存储帐户，以便创建、更新和查询表等"
[biztalk-server-doc]: https://docs.microsoft.com/connectors/biztalk/ "连接到 BizTalk 服务器，以便与 Azure 逻辑应用并行运行基于 BizTalk 的应用程序"
[box-doc]: ./connectors-create-api-box.md "连接到框。上传、获取、删除、列出文件等"
[dropbox-doc]: ./connectors-create-api-dropbox.md "连接到放置框。上传、获取、删除、列出文件等"
[dynamics-365-doc]: ./connectors-create-api-crmonline.md "联机连接到动态 CRM，以便可以使用 CRM 联机数据"
[facebook-doc]: ./connectors-create-api-facebook.md "连接到 Facebook。发布到时间线、获取页面源等"
[file-system-doc]: ../logic-apps/logic-apps-using-file-connector.md "连接到本地文件系统"
[ftp-doc]: ./connectors-create-api-ftp.md "连接到 FTP/FTPS 服务器以执行 FTP 任务，例如上传、获取、删除文件，等等"
[github-doc]: ./connectors-create-api-github.md "连接到 GitHub，对问题进行跟踪"
[google-calendar-doc]: ./connectors-create-api-googlecalendar.md "连接到 Google 日历，可以管理日历"
[google-drive-doc]: ./connectors-create-api-googledrive.md "连接到 GoogleDrive，以便您能够处理您的数据"
[google-sheets-doc]: ./connectors-create-api-googlesheet.md "连接到 Google 表格，以便您可以修改工作表"
[google-tasks-doc]: ./connectors-create-api-googletasks.md "连接到 Google 任务，以便您可以管理您的任务"
[ibm-3270-doc]: ./connectors-run-3270-apps-ibm-mainframe-create-api-3270.md "连接到 IBM 主机上的 3270 个应用"
[ibm-db2-doc]: ./connectors-create-api-db2.md "在云或本地连接到 IBM DB2。更新行、获取表等"
[ibm-informix-doc]: ./connectors-create-api-informix.md "在云或本地连接到 Informix。阅读行、列出表等"
[ibm-mq-doc]: ./connectors-create-api-mq.md "连接到本地或 Azure 中的 IBM MQ 以发送和接收消息"
[instagram-doc]: ./connectors-create-api-instagram.md "连接到 Instagram。触发事件或对事件采取行动"
[mailchimp-doc]: ./connectors-create-api-mailchimp.md "连接到您的邮件池帐户。管理和自动化邮件"
[mandrill-doc]: ./connectors-create-api-mandrill.md "连接到 Mandrill 进行通信"
[mysql-doc]: https://docs.microsoft.com/connectors/mysql/ "连接到本地 MySQL 数据库，以便读取和写入数据"
[office-365-outlook-doc]: ./connectors-create-api-office365-outlook.md "连接到 Office 365 帐户，以便您可以发送和接收电子邮件、管理日历和联系人等"
[office-365-users-doc]: ./connectors-create-api-office365-users.md
[onedrive-doc]: ./connectors-create-api-onedrive.md "连接到您的个人 Microsoft OneDrive，以便您可以上传、删除、列出文件等"
[onedrive-for-business-doc]: ./connectors-create-api-onedriveforbusiness.md "连接到您的业务 Microsoft OneDrive，以便您可以上传、删除、列出文件等"
[oracle-db-doc]: ./connectors-create-api-oracledatabase.md "连接到 Oracle 数据库，以便添加、插入、删除行等"
[outlook.com-doc]: ./connectors-create-api-outlook.md "连接到 Outlook 邮箱，以便管理电子邮件、日历、联系人等"
[postgre-sql-doc]: https://docs.microsoft.com/connectors/postgresql/ "连接到 PostgreSQL 数据库，以便从表中读取数据"
[project-online-doc]: ./connectors-create-api-projectonline.md "联机连接到 Microsoft 项目，以便管理项目、任务、资源等"
[rss-doc]: ./connectors-create-api-rss.md "发布和检索源项，将新项目发布到 RSS 源时触发操作"
[salesforce-doc]: ./connectors-create-api-salesforce.md "连接到您的 Salesforce 帐户。管理客户、潜在客户、商机等"
[sap-connector-doc]: ../logic-apps/logic-apps-using-sap-connector.md "连接到本地 SAP 系统"
[sendgrid-doc]: ./connectors-create-api-sendgrid.md "连接到发送网格。发送电子邮件和管理收件人列表"
[sftp-ssh-doc]: ./connectors-sftp-ssh.md "使用 SSH 连接到您的 SFTP 帐户。上传、获取、删除文件等"
[sharepoint-server-doc]: ./connectors-create-api-sharepointserver.md "连接到 SharePoint 本地服务器。管理文档、列表项等"
[sharepoint-online-doc]: ./connectors-create-api-sharepointonline.md "在线连接到共享点。管理文档、列表项等"
[slack-doc]: ./connectors-create-api-slack.md "连接到松弛并将消息发布到 Slack 通道"
[smtp-doc]: ./connectors-create-api-smtp.md "连接到 SMTP 服务器并发送带附件的电子邮件"
[sparkpost-doc]: ./connectors-create-api-sparkpost.md "连接到 SparkPost 进行通信"
[sql-server-doc]: ./connectors-create-api-sqlazure.md "连接到 Azure SQL 数据库或 SQL 服务器。在 SQL 数据库表中创建、更新、获取和删除条目"
[teradata-doc]: https://docs.microsoft.com/connectors/teradata/ "连接到 Teradata 数据库以从表中读取数据"
[trello-doc]: ./connectors-create-api-trello.md "连接到特雷洛。管理您的项目，并与任何人组织任何内容"
[twilio-doc]: ./connectors-create-api-twilio.md "连接到特维利奥。发送和获取消息、获取可用号码、管理传入电话号码等"
[twitter-doc]: ./connectors-create-api-twitter.md "连接到推特。获取时间线、发布推文等"
[yammer-doc]: ./connectors-create-api-yammer.md "连接到 Yammer。发布邮件、获取新消息等"
[youtube-doc]: ./connectors-create-api-youtube.md "连接到 YouTube。管理视频和频道"

<!--Enterprise Intregation Pack doc links-->
[as2-doc]: ../logic-apps/logic-apps-enterprise-integration-as2.md "编码和解码使用 AS2 协议的消息"
[edifact-doc]: ../logic-apps/logic-apps-enterprise-integration-edifact.md "编码和解码使用 EDIFACT 协议的消息"
[edifact-decode-doc]: ../logic-apps/logic-apps-enterprise-integration-EDIFACT-decode.md "解码使用 EDIFACT 协议的消息"
[edifact-encode-doc]: ../logic-apps/logic-apps-enterprise-integration-EDIFACT-encode.md "对使用 EDIFACT 协议的消息进行编码"
[flat-file-decode-doc]:../logic-apps/logic-apps-enterprise-integration-flatfile.md "了解企业集成平面文件"
[flat-file-encode-doc]:../logic-apps/logic-apps-enterprise-integration-flatfile.md "了解企业集成平面文件"
[integration-account-doc]: ../logic-apps/logic-apps-enterprise-integration-metadata.md "管理集成帐户项目元数据"
[json-liquid-transform-doc]: ../logic-apps/logic-apps-enterprise-integration-liquid-transform.md "使用液体模板转换 JSON"
[x12-doc]: ../logic-apps/logic-apps-enterprise-integration-x12.md "编码和解码使用 X12 协议的消息"
[x12-decode-doc]: ../logic-apps/logic-apps-enterprise-integration-X12-decode.md "解码使用 X12 协议的消息"
[x12-encode-doc]: ../logic-apps/logic-apps-enterprise-integration-X12-encode.md "对使用 X12 协议的消息进行编码"
[xml-transform-doc]: ../logic-apps/logic-apps-enterprise-integration-transform.md "转换 XML 消息"
[xml-validate-doc]: ../logic-apps/logic-apps-enterprise-integration-xml-validation.md "验证 XML 消息"

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
[azure-document-db-icon]: ./media/apis-list/azure-document-db.png
[azure-event-grid-icon]: ./media/apis-list/azure-event-grid.png
[azure-event-grid-publish-icon]: ./media/apis-list/azure-event-grid-publish.png
[azure-event-hubs-icon]: ./media/apis-list/azure-event-hubs.png
[azure-file-storage-icon]: ./media/apis-list/azure-file-storage.png
[azure-ml-icon]: ./media/apis-list/azure-ml.png
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
[box-icon]: ./media/apis-list/box.png
[campfire-icon]: ./media/apis-list/campfire.png
[common-data-service-icon]: ./media/apis-list/common-data-service.png
[dropbox-icon]: ./media/apis-list/dropbox.png
[dynamics-365-icon]: ./media/apis-list/dynamics-crm-online.png
[dynamics-365-financials-icon]: ./media/apis-list/dynamics-365-financials.png
[dynamics-365-operations-icon]: ./media/apis-list/dynamics-365-operations.png
[easy-redmine-icon]: ./media/apis-list/easyredmine.png
[facebook-icon]: ./media/apis-list/facebook.png
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
[mailchimp-icon]: ./media/apis-list/mailchimp.png
[mandrill-icon]: ./media/apis-list/mandrill.png
[mysql-icon]: ./media/apis-list/mysql.png
[office-365-outlook-icon]: ./media/apis-list/office-365.png
[office-365-users-icon]: ./media/apis-list/office-365-users.png
[onedrive-icon]: ./media/apis-list/onedrive.png
[onedrive-for-business-icon]: ./media/apis-list/onedrive-business.png
[oracle-db-icon]: ./media/apis-list/oracle-db.png
[outlook.com-icon]: ./media/apis-list/outlook.png
[pagerduty-icon]: ./media/apis-list/pagerduty.png
[pinterest-icon]: ./media/apis-list/pinterest.png
[postgre-sql-icon]: ./media/apis-list/postgre-sql.png
[project-online-icon]: ./media/apis-list/projecton-line.png
[redmine-icon]: ./media/apis-list/redmine.png
[rss-icon]: ./media/apis-list/rss.png
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
[trello-icon]: ./media/apis-list/trello.png
[twilio-icon]: ./media/apis-list/twilio.png
[twitter-icon]: ./media/apis-list/twitter.png
[vimeo-icon]: ./media/apis-list/vimeo.png
[visual-studio-team-services-icon]: ./media/apis-list/visual-studio-team-services.png
[wordpress-icon]: ./media/apis-list/wordpress.png
[yammer-icon]: ./media/apis-list/yammer.png
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
