---
title: Azure 逻辑应用的连接器
description: 使用 Azure 逻辑应用的连接器（包括内置、托管、本地集成帐户和企业连接器）自动执行工作流
services: logic-apps
ms.service: logic-apps
author: ecfan
ms.author: estfan
ms.reviewer: klam, LADocs
ms.suite: integration
ms.topic: article
ms.date: 05/08/2019
ms.openlocfilehash: 3d828e6e365864c2c23e1d9f178a556ff3c07515
ms.sourcegitcommit: 77bfc067c8cdc856f0ee4bfde9f84437c73a6141
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/16/2019
ms.locfileid: "72430182"
---
# <a name="connectors-for-azure-logic-apps"></a>Azure 逻辑应用的连接器

使用连接器可以从 Azure 逻辑应用快速访问其他应用、服务、系统、协议和平台的事件、数据和操作。 使用逻辑应用中连接器，可以扩展云和本地应用的功能，以便对创建的数据和现有的数据执行任务。

虽然逻辑应用提供了[数百个连接器](https://docs.microsoft.com/connectors)，但本文介绍了广泛且更常用的连接器，这些连接器已成功由成千上万的应用程序和用于处理数据和信息的数百万次使用。 若要查找连接器的完整列表以及每个连接器的参考信息（如触发器、操作和限制），请查看[连接器概述](https://docs.microsoft.com/connectors)下的连接器引用页面。 此外，详细了解[触发器和操作](#triggers-actions)、[逻辑应用定价模型](../logic-apps/logic-apps-pricing.md)和[逻辑应用定价详细信息](https://azure.microsoft.com/pricing/details/logic-apps/)。 

> [!NOTE]
> 若要与没有连接器的服务或 API 集成，可以直接通过协议（如 HTTP）或创建[自定义连接器](#custom)来调用服务。

连接器可以作为内置触发器和操作，也可以作为托管连接器使用：

<a name="built-in"></a>

* [**内置**](#built-ins)：这些内置触发器和操作都是 "本机" Azure 逻辑应用，可帮助你创建按自定义计划运行的逻辑应用，与其他终结点通信，接收和响应请求，以及调用 azure 函数、Azure API 应用（Web）应用）、你自己的 Api （通过 Azure API 管理进行管理和发布）以及可以接收请求的嵌套逻辑应用。 还可以使用内置操作来帮助组织和控制逻辑应用工作流及处理数据。

  > [!NOTE]
  > [集成服务环境（ISE）](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md)中的逻辑应用可以直接访问 Azure 虚拟网络中的资源。
  > 使用 ISE 时，将在逻辑应用所在的 ISE 中运行显示 "**核心**" 标签的内置触发器和操作。 在 ISE 中运行的逻辑应用、内置触发器和内置操作使用不同于基于消耗的定价计划的定价计划。
  >
  > 有关创建 ISEs 的详细信息，请参阅[从 Azure 逻辑应用连接到 azure 虚拟网络](../logic-apps/connect-virtual-network-vnet-isolated-environment.md)。 
  > 有关定价的详细信息，请参阅[逻辑应用定价模型](../logic-apps/logic-apps-pricing.md)。

<a name="managed-connectors"></a>

* **托管连接器**：由 Microsoft 部署和管理，这些连接器提供用于访问云服务和/或本地系统的触发器和操作，包括 Office 365、Azure Blob 存储、SQL Server、Dynamics、Salesforce、SharePoint等。 某些连接器专门支持企业到企业（B2B）通信方案，并且需要一个链接到逻辑应用的[集成帐户](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md)。 使用特定连接器之前，你可能必须首先创建由 Azure 逻辑应用管理的连接。 

  例如，如果你使用的是 Microsoft BizTalk Server，则逻辑应用可以使用[BizTalk Server 本地连接器](#on-premises-connectors)连接到 BizTalk Server 并与之通信。 
  然后，可以使用[集成帐户连接器](#integration-account-connectors)在逻辑应用中扩展或执行类似于 BizTalk 的操作。

  连接器分类为标准或企业。 
  [企业连接器](#enterprise-connectors)提供对企业系统（例如 SAP、ibm MQ 和 ibm 3270）的访问权限，以增加成本。 若要确定连接器是标准连接器还是企业版，请参阅[连接器概述](https://docs.microsoft.com/connectors)下每个连接器的参考页中的技术详细信息。 

  你还可以使用这些类别来识别连接器，尽管某些连接器可以跨多个类别。 
  例如，SAP 是企业连接器和本地连接器：

  |   |   |
  |---|---|
  | [**托管的 API 连接器**](#managed-api-connectors) | 创建使用 Azure Blob 存储、Office 365、Dynamics、Power BI、OneDrive、Salesforce、SharePoint Online 等服务的逻辑应用。 |
  | [**本地连接器**](#on-premises-connectors) | 安装并设置[本地数据网关][gateway-doc]之后，这些连接器可帮助逻辑应用访问本地系统，例如 SQL Server、SharePoint Server、Oracle DB、文件共享等。 |
  | [**集成帐户连接器**](#integration-account-connectors) | 创建和付费购买集成帐户时可以使用这些连接器，它们会转换和验证 XML、编码和解码平面文件，以及使用 AS2、EDIFACT 和 X12 协议处理企业到企业 (B2B) 消息。 |
  |||

  > [!NOTE]
  > [集成服务环境（ISE）](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md)中的逻辑应用可以直接访问 Azure 虚拟网络中的资源。 使用 ISE 时，显示**ise**标签的标准和企业连接器与逻辑应用在同一 ISE 中运行。 不显示 ISE 标签的连接器在全局逻辑应用服务中运行。
  >
  > 对于连接到 Azure 虚拟网络的本地系统，请将 ISE 注入该网络，以便逻辑应用可以使用具有**ISE**标签、HTTP 操作或[自定义连接器](#custom)的连接器直接访问这些系统。 在 ISE 中运行的逻辑应用和连接器使用不同于基于消耗的定价计划的定价计划。 
  >
  > 有关创建 ISEs 的详细信息，请参阅[从 Azure 逻辑应用连接到 azure 虚拟网络](../logic-apps/connect-virtual-network-vnet-isolated-environment.md)。
  > 有关定价的详细信息，请参阅[逻辑应用定价模型](../logic-apps/logic-apps-pricing.md)。

  有关连接器的完整列表以及每个连接器的参考信息，如操作和任何触发器（由 OpenAPI （以前的 Swagger）说明定义）加上任何限制，可以在[连接器概述](/connectors/)下找到完整列表。 有关定价信息，请参阅[逻辑应用定价模型](../logic-apps/logic-apps-pricing.md)和[逻辑应用定价详细信息](https://azure.microsoft.com/pricing/details/logic-apps/)。 

<a name="built-ins"></a>

## <a name="built-ins"></a>内置操作

逻辑应用提供内置触发器和操作，因此你可以创建基于计划的工作流、帮助逻辑应用与其他应用和服务通信、通过逻辑应用控制工作流，以及管理或处理数据。

|   |   |   |   | 
|---|---|---|---| 
| [@no__t 1API 图标 @ no__t-2**计划**][recurrence-doc] | - 使用“重复周期”触发器，按指定的计划以简单到复杂的重复方案运行逻辑应用。 <p>- 使用“延迟”操作，将逻辑应用暂停指定的持续时间。 <p>- 使用“延迟截止时间”操作，将逻辑应用暂停到指定的日期和时间。 | [@no__t 1API 图标 @ no__t-2**HTTP**][http-doc] | 使用适用于 HTTP、HTTP + Swagger 和 HTTP + Webhook 的触发器和操作，通过 HTTP 与任何终结点通信。 | 
| [@no__t 1API 图标 @ no__t-2**请求**][http-request-doc] | - 使用“请求”触发器，使逻辑应用可从其他应用程序或服务调用、针对事件网格资源事件触发，或者针对 Azure 安全中心警报触发。 <p>- 使用“响应”操作将响应发送到应用或服务。 | [![API icon @ no__t**批处理**][batch-doc] | - 使用“批处理消息”触发器来批量处理消息。 <p>- 使用“发送要批量处理的消息”操作调用具有现有批处理触发器的逻辑应用。 | 
| [![API icon @ no__t-2**Azure Functions**][azure-functions-doc] | 从逻辑应用调用运行自定义代码片段（C# 或 Node.js）的 Azure 函数。 | [@no__t 1API 图标 @ no__t-2**AZURE API 管理**][azure-api-management-doc] | 调用可以使用 Azure API 管理进行管理和发布的自有 API 定义的触发器与操作。 | 
| [![API 图标 @ no__t**Azure 应用服务**][azure-app-services-doc] | 调用 Azure 应用服务上托管的 Azure API 应用或 Web 应用。 包含 Swagger 后，这些应用定义的触发器和操作类似于其他任何第一类触发器和操作。 | [@no__t 1API 图标 @ no__t-2**Azure @ no__t-4Logic Apps**][nested-logic-app-doc] | 调用使用“请求”触发器启动的其他逻辑应用。 | 
||||| 

### <a name="control-workflow"></a>控制工作流

逻辑应用提供内置操作来构造和控制逻辑应用工作流中的操作：

|   |   |   |   | 
|---|---|---|---| 
| [![Built Icon @ no__t-2**条件**][condition-doc] | 评估条件，并根据条件是 true 还是 false 运行不同的操作。 | [**每个**![Built 图标 @ no__t-2][for-each-doc] | 对数组中的每个项执行相同的操作。 | 
| [![Built 图标 @ no__t-2**范围**][scope-doc] | 将操作分组到范围，以便在该范围内的操作完成运行后，获取这些操作的自身状态。 | [![Built 图标 @ no__t-2**开关**][switch-doc] | 将操作分组到案例，而案例分配有唯一的值（默认案例除外）。 仅运行其分配值与表达式、对象或令牌的结果相匹配的案例。 如果不存在任何匹配项，则运行默认案例。 | 
| [![Built 图标 @ no__t-2**终止**][terminate-doc] | 停止当前正在运行的逻辑应用工作流。 | [![Built Icon @ no__t-2**Until**][until-doc] | 重复操作，直到指定的条件为 true 或某个状态发生更改。 | 
||||| 

### <a name="manage-or-manipulate-data"></a>管理或处理数据

逻辑应用提供了用于处理数据输出及其格式的内置操作：  

|   |   | 
|---|---| 
| [![Built 图标 @ no__t**数据操作**][data-operations-doc] | 对数据执行操作： <p>- **组合**：基于具有不同类型的多个输入创建单个输出。 <br>- **创建 CSV 表**：基于包含 JSON 对象的数组创建逗号分隔值 (CSV) 表。 <br>- **创建 HTML 表**：基于包含 JSON 对象的数组创建一个 HTML 表。 <br>- **筛选数组**：基于符合条件的另一个数组中的项创建一个数组。 <br>- **联接**：基于数组中的所有项创建一个字符串，并使用指定的分隔符分隔这些项。 <br>- **分析 JSON**：基于 JSON 内容中的属性及其值创建用户友好的令牌，以便可以在工作流中使用这些属性。 <br>- **选择**：通过转换另一数组中的项或值并将这些项映射到指定的属性，创建包含 JSON 对象的数组。 | 
| ![内置操作图标][date-time-icon]<br/>**日期时间** | 对时间戳执行操作： <p>- **添加到时间**：将指定的单位数添加到时间戳。 <br>- **转换时区**：将时间戳从源时区转换为目标时区。 <br>- **当前时间**：返回字符串形式的当前时间戳。 <br>- **获取将来的时间**：返回当前时间戳加上指定的时间单位。 <br>- **获取过去的时间**：返回当前时间戳减去指定的时间单位。 <br>- **从时间减去**：从时间戳中减去一定数目的时间单位。 |
| [![Built Icon @ no__t-2**变量**][variables-doc] | 对变量执行操作： <p>- **追加到数组变量**：插入一个值，作为变量存储的数组中的最后一个项。 <br>- **追加到字符串变量**：插入一个值，作为变量存储的字符串中的最后一个字符。 <br>- **递减变量**：按常量值减小变量。 <br>- **递增变量**：按常量值增大变量。 <br>- **初始化变量**：创建一个变量并声明其数据类型和初始值。 <br>- **设置变量**：将不同的值分配给现有变量。 |
|  |  | 

<a name="managed-api-connectors"></a>

## <a name="managed-api-connectors"></a>托管的 API 连接器

逻辑应用提供了这些常用的标准连接器，可通过这些服务或系统自动执行任务、流程和工作流。

|   |   |   |   | 
|---|---|---|---| 
| [@no__t 1API 图标 @ no__t-2**Azure 服务总线**][azure-service-bus-doc] | 使用逻辑应用中最常用的连接器管理异步消息、会话和主题订阅。 | [![API icon @ no__t-2**SQL Server**][sql-server-doc] | 连接到本地 SQL Server 或云中的 Azure SQL 数据库，以便可以管理记录、运行存储过程或执行查询。 | 
| [@no__t 1API icon @ no__t-2**Office 365 @ no__t-4Outlook**][office-365-outlook-doc] | 连接到 Office 365 电子邮件帐户，以便可以创建和管理电子邮件、任务、日历事件和会议、联系人、请求，等等。 | [@no__t 1API icon @ no__t-2**Azure Blob @ no__t-4Storage**][azure-blob-storage-doc] | 连接到存储帐户，以便可以创建和管理 Blob 内容。 | 
| [@no__t 1API 图标 @ no__t-2**SFTP**][sftp-doc] | 连接到可从 internet 访问的 SFTP 服务器，以便能够处理文件和文件夹。 | [@no__t 1API icon @ no__t-2**SharePoint @ no__t-4Online**][sharepoint-online-doc] | 连接到 SharePoint Online，以便能够管理文件、附件、文件夹，等等。 | 
| [![API icon @ no__t-2**Dynamics 365 @ no__t-4CRM Online**][dynamics-365-doc] | 连接到 Dynamics 365 帐户，以便能够创建和管理记录、项，等等。 | [@no__t 1API 图标 @ no__t-2**FTP**][ftp-doc] | 连接到可从 internet 访问的 FTP 服务器，以便能够处理文件和文件夹。 | 
| [@no__t 1API 图标 @ no__t-2**Salesforce**][salesforce-doc] | 连接到 Salesforce 帐户，以便能够创建和管理记录、作业、对象等项。 | [![API icon @ no__t-2**Twitter**][twitter-doc] | 连接到 Twitter 帐户，以便能够管理推文、关注者、时间表、等等。 将推文保存到 SQL、Excel 或 SharePoint。 | 
| [@no__t 1API 图标 @ no__t-2**Azure 事件中心**][azure-event-hubs-doc] | 通过事件中心使用和发布事件。 例如，通过事件中心获取逻辑应用的输出，然后将输出发送到实时分析提供程序。 | [@no__t 1API 图标 @ no__t-2**Azure 事件**</br>**网格**][azure-event-grid-doc] | 监视事件网格发布的事件（例如，当 Azure 资源或第三方资源发生更改时）。 | 
|||||

<a name="on-premises-connectors"></a>

## <a name="on-premises-connectors"></a>本地连接器 

下面是逻辑应用提供的一些常用标准连接器，用于访问本地系统中的数据和资源。 必须先[下载、安装和设置本地数据网关][gateway-doc]，然后才能创建与本地系统的连接。 此网关提供安全信道，无需设置所需的网络基础结构。 

|   |   |   |   |   | 
|---|---|---|---|---| 
| ![API 图标][biztalk-server-icon]<br/>**BizTalk**</br> **服务器** | [@no__t 1API icon @ no__t-2**File @ no__t-4 System**][file-system-doc] | [![API icon @ no__t**IBM DB2**][ibm-db2-doc] | [![API icon @ no__t-2**IBM**</br> **Informix**][ibm-informix-doc] | ![API 图标][mysql-icon]<br/>**MySQL** | 
| [![API icon @ no__t-2**Oracle DB**][oracle-db-doc] | ![API 图标][postgre-sql-icon]<br/>**PostgreSQL** | [@no__t 1API 图标 @ no__t-2**SharePoint @ No__t 服务器**][sharepoint-server-doc] | [@no__t 1API 图标 @ no__t-2**SQL @ No__t 服务器**][sql-server-doc] | ![API 图标][teradata-icon]<br/>**Teradata** | 
|||||

<a name="integration-account-connectors"></a>

## <a name="integration-account-connectors"></a>集成帐户连接器

创建和支付[集成帐户](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md)时，逻辑应用提供了标准连接器用于构建企业到企业（B2B）解决方案，可通过 Azure 中的 ENTERPRISE INTEGRATION PACK （EIP）获得。 使用此帐户可以创建和存储 B2B 项目，例如贸易合作伙伴、协议、映射、架构、证书，等等。 若要使用这些项目，请将逻辑应用与集成帐户相关联。 如果你当前使用的是 BizTalk Server，则可能已熟悉这些连接器的用法。

|   |   |   |   | 
|---|---|---|---| 
| [@no__t 1API icon @ no__t-2**AS2 @ no__t 解码**][as2-doc] | [@no__t 1API icon @ no__t-2**AS2 @ no__t 编码**][as2-doc] | [@no__t 1API icon @ no__t-2**EDIFACT @ no__t 解码**][edifact-decode-doc] | [@no__t 1API icon @ no__t-2**EDIFACT @ no__t 编码**][edifact-encode-doc] | 
| [@no__t 1API 图标 @ no__t-2**平面文件 @ no__t 解码**][flat-file-decode-doc] | [@no__t 1API 图标 @ no__t-2**平面文件 @ no__t 编码**][flat-file-encode-doc] | [@no__t 1API icon @ no__t-2**Integration @ no__t-4account**][integration-account-doc] | [@no__t 1API 图标 @ no__t-2**液体**</br>**转换**][json-liquid-transform-doc] | 
| [@no__t 1API icon @ no__t-2**X12 @ no__t 解码**][x12-decode-doc] | [@no__t 1API 图标 @ no__t-2**X12 @ no__t 编码**][x12-encode-doc] | [@no__t 1API icon @ no__t-2**XML**</br>**转换**][xml-transform-doc] | [![API icon @ no__t-2**XML <br/>validation**][xml-validate-doc] |  
||||| 

<a name="enterprise-connectors"></a>

## <a name="enterprise-connectors"></a>企业连接器

逻辑应用提供了这些企业连接器用于访问企业系统，例如 SAP 和 IBM MQ：

|   |   |   | 
|---|---|---| 
| [@no__t 1API 图标 @ no__t-2**IBM 3270**][ibm-3270-doc] | [@no__t 1API icon @ no__t-2**IBM MQ**][ibm-mq-doc] | [@no__t 1API 图标 @ no__t-2**SAP**][sap-connector-doc] |
|||| 

<a name="triggers-actions"></a>

## <a name="triggers-and-actions---more-info"></a>触发器和操作-详细信息

连接器可以提供*触发器*和/或*操作*。 *触发器*是任何逻辑应用中的第一步，通常指定触发触发器并开始运行逻辑应用的事件。 例如，FTP 连接器具有一个触发器，该触发器在添加或修改文件时启动逻辑应用。 某些触发器会定期检查指定的事件或数据，并在检测到指定的事件或数据时触发。 当发生特定事件或新数据可用时，其他触发器等待但立即触发。 触发器还会将任何所需的数据传递到逻辑应用。 逻辑应用可在整个工作流中读取和使用这些数据。
例如，Twitter 连接器具有一个触发器，即 "发布新推文时"，它会将推文的内容传递到逻辑应用的工作流。 

触发触发器后，Azure 逻辑应用会创建逻辑应用的实例，并开始在逻辑应用的工作流中运行这些*操作*。 操作是指在逻辑应用的工作流中执行触发器并执行任务的步骤。 例如，可以创建一个逻辑应用，用于从 SQL 数据库获取客户数据，并在以后的操作中处理该数据。 

下面是 Azure 逻辑应用提供的常见触发器类型：

* *定期触发器*：此触发器按指定的计划运行，并与特定服务或系统紧密关联。

* *轮询触发器*：此触发器根据指定的计划定期轮询特定服务或系统、检查是否有新数据或是否发生了特定事件。 如果有新数据可用或发生特定事件，则触发器将创建并运行逻辑应用的新实例，该实例现在可以使用作为输入传递的数据。

* *推送触发器*：此触发器等待并侦听新数据或事件发生。 当新数据可用或事件发生时，触发器将创建并运行逻辑应用的新实例，该实例现在可以使用作为输入传递的数据。

<a name="custom"></a>

## <a name="connector-configuration"></a>连接器配置

每个连接器的触发器和操作都提供自己的属性供您进行配置。 许多连接器还要求首先创建与目标服务或系统的*连接*，并提供身份验证凭据或其他配置详细信息，然后才能在逻辑应用中使用触发器或操作。 例如，你必须授权到 Twitter 帐户的连接来访问数据或代表你发布。 

对于使用 Azure Active Directory （Azure AD） OAuth 的连接器，创建连接意味着登录到服务（例如 Office 365、Salesforce 或 GitHub），在此服务中，将对访问令牌进行[加密](../security/fundamentals/encryption-overview.md)并将其安全地存储在 Azure 密钥存储中。 其他连接器（如 FTP 和 SQL）需要具有配置详细信息的连接，如服务器地址、用户名和密码。 这些连接配置详细信息同样加密并安全存储。 详细了解[Azure 中的加密](../security/fundamentals/encryption-overview.md)。

只要该服务或系统允许，连接就可以访问目标服务或系统。 对于使用 Azure AD OAuth 连接的服务（如 Office 365 和 Dynamics），Azure 逻辑应用会无限期地刷新访问令牌。 其他服务可能会限制 Azure 逻辑应用无需刷新即可使用令牌的时间。 通常，某些操作会使所有访问令牌（例如更改密码）失效。

<a name="custom"></a>

## <a name="custom-apis-and-connectors"></a>自定义 API 和连接器

若要调用运行自定义代码或者无法作为连接器使用的 API，可以通过[创建自定义 API 应用](../logic-apps/logic-apps-create-api-app.md)来扩展逻辑应用平台。 还可以针对任何基于 REST 或 SOAP 的 API [创建自定义连接器](../logic-apps/custom-connector-overview.md)，使这些 API 可供 Azure 订阅中的任何逻辑应用使用。
若要使自定义 API 应用或连接器可供任何人在 Azure 中使用，可以[提交连接器进行 Microsoft 认证](../logic-apps/custom-connector-submit-certification.md)。

> [!NOTE]
> [集成服务环境（ISE）](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md)中的逻辑应用可以直接访问 Azure 虚拟网络中的资源。
> 如果你有需要本地数据网关的自定义连接器，并且你在 ISE 外部创建了这些连接器，则 ISE 中的逻辑应用还可以使用这些连接器。
>
> 在 ISE 中创建的自定义连接器不适用于本地数据网关。 但是，这些连接器可以直接访问连接到托管 ISE 的 Azure 虚拟网络的本地数据源。 因此，在与这些资源通信时，ISE 中的逻辑应用最有可能不需要数据网关。
>
> 有关创建 ISEs 的详细信息，请参阅[从 Azure 逻辑应用连接到 azure 虚拟网络](../logic-apps/connect-virtual-network-vnet-isolated-environment.md)。

## <a name="next-steps"></a>后续步骤

* 查找[连接器的完整列表](https://docs.microsoft.com/connectors)
* [创建第一个逻辑应用](../logic-apps/quickstart-create-first-logic-app-workflow.md)
* [为逻辑应用创建自定义连接器](https://docs.microsoft.com/connectors/custom-connectors/)
* [为逻辑应用创建自定义 API](../logic-apps/logic-apps-create-api-app.md)

<!--Misc doc links-->
[gateway-doc]: ../logic-apps/logic-apps-gateway-connection.md "通过本地数据网关，从逻辑应用连接到本地数据源"

<!--Built-ins doc links-->
[azure-functions-doc]: ../logic-apps/logic-apps-azure-functions.md "将逻辑应用与 Azure Functions 集成"
[azure-api-management-doc]: ../api-management/get-started-create-service-instance.md "创建 Azure API 管理服务实例用于管理和发布 API"
[azure-app-services-doc]: ../logic-apps/logic-apps-custom-hosted-api.md "将逻辑应用与应用服务 API 应用集成"
[azure-service-bus-doc]: ./connectors-create-api-servicebus.md "从服务总线队列和主题发送消息，并从服务总线队列和订阅接收消息"
[batch-doc]: ../logic-apps/logic-apps-batch-process-send-receive-messages.md "以组或批的形式处理消息"
[condition-doc]: ../logic-apps/logic-apps-control-flow-conditional-statement.md "评估条件并根据条件是 true 还是 false 运行不同的操作"
[delay-doc]: ./connectors-native-delay.md "执行延迟的操作"
[for-each-doc]: ../logic-apps/logic-apps-control-flow-loops.md#foreach-loop "对数组中的每个项执行相同的操作"
[http-doc]: ./connectors-native-http.md "通过 HTTP 连接器进行 HTTP 调用"
[http-request-doc]: ./connectors-native-reqres.md "向逻辑应用添加 HTTP 请求和响应的操作"
[http-response-doc]: ./connectors-native-reqres.md "向逻辑应用添加 HTTP 请求和响应的操作"
[http-swagger-doc]: ./connectors-native-http-swagger.md "通过 HTTP + Swagger 连接器进行 HTTP 调用"
[http-webook-doc]: ./connectors-native-webhook.md "将 HTTP Webhook 操作和触发器添加到逻辑应用"
[nested-logic-app-doc]: ../logic-apps/logic-apps-http-endpoint.md "将逻辑应用与嵌套工作流集成"
[query-doc]: ./connectors-native-query.md "通过查询操作选择和筛选数组"
[recurrence-doc]:  ./connectors-native-recurrence.md "针对逻辑应用触发重复执行的操作"
[scope-doc]: ../logic-apps/logic-apps-control-flow-run-steps-group-scopes.md "将操作组织成组，以便在该组中的操作完成运行后获取这些操作的自身状态" 
[switch-doc]: ../logic-apps/logic-apps-control-flow-switch-statement.md "将操作组织到为其分配唯一值的事例中。仅运行其值与表达式、对象或标记中的结果相匹配的事例。如果不存在匹配项，则运行默认的 case"
[terminate-doc]: ../logic-apps/logic-apps-workflow-actions-triggers.md#terminate-action "停止或取消逻辑应用的正在运行的工作流"
[until-doc]: ../logic-apps/logic-apps-control-flow-loops.md#until-loop "重复操作，直到指定的条件为 true 或某个状态发生更改"
[data-operations-doc]: ../logic-apps/logic-apps-perform-data-operations.md "执行数据操作，如筛选数组或创建 CSV 和 HTML 表"
[variables-doc]: ../logic-apps/logic-apps-create-variables-store-values.md "使用变量执行操作，例如初始化、设置、递增、递减和追加到字符串或数组变量"

<!--Managed API doc links-->
[azure-blob-storage-doc]: ./connectors-create-api-azureblobstorage.md "使用 Azure Blob 存储连接器管理 Blob 容器中的文件"
[azure-event-grid-doc]: ../event-grid/monitor-virtual-machine-changes-event-grid-logic-app.md "监视事件网格发布的事件（例如，当 Azure 资源或第三方资源发生更改时）"
[azure-event-hubs-doc]: ./connectors-create-api-azure-event-hubs.md "连接到 Azure 事件中心。在逻辑应用和事件中心之间接收和发送事件"
[box-doc]: ./connectors-create-api-box.md "连接到 Box。上传、获取、删除、列出文件，等等"
[dropbox-doc]: ./connectors-create-api-dropbox.md "连接到 Dropbox。上传、获取、删除、列出文件，等等"
[dynamics-365-doc]: ./connectors-create-api-crmonline.md "连接到 Dynamics CRM Online，以便使用 CRM Online 数据"
[facebook-doc]: ./connectors-create-api-facebook.md "连接到 Facebook。发布到时间线、获取页面源等"
[file-system-doc]: ../logic-apps/logic-apps-using-file-connector.md "连接到本地文件系统"
[ftp-doc]: ./connectors-create-api-ftp.md "连接到 FTP/FTPS 服务器以执行 FTP 任务，例如上传、获取、删除文件，等等"
[github-doc]: ./connectors-create-api-github.md "连接到 GitHub，对问题进行跟踪"
[google-calendar-doc]: ./connectors-create-api-googlecalendar.md "连接到 Google Calendar 即可管理日历。"
[google-drive-doc]: ./connectors-create-api-googledrive.md "连接到 GoogleDrive，以便使用数据"
[google-sheets-doc]: ./connectors-create-api-googlesheet.md "连接到 Google Sheets 以便可以修改表单"
[google-tasks-doc]: ./connectors-create-api-googletasks.md "连接到 Google Tasks，以便管理任务"
[ibm-3270-doc]: ./connectors-run-3270-apps-ibm-mainframe-create-api-3270.md "在 IBM 大型机上连接到3270应用"
[ibm-db2-doc]: ./connectors-create-api-db2.md "连接到云中或本地的 IBM DB2。更新行、获取表等"
[ibm-informix-doc]: ./connectors-create-api-informix.md "在云中或本地连接到 Informix。读取行、列出表等"
[ibm-mq-doc]: ./connectors-create-api-mq.md "连接到本地或 Azure 中的 IBM MQ 以发送和接收消息"
[instagram-doc]: ./connectors-create-api-instagram.md "连接到 Instagram。触发事件或对事件执行操作"
[mailchimp-doc]: ./connectors-create-api-mailchimp.md "连接到你的 MailChimp 帐户。管理和自动执行邮件"
[mandrill-doc]: ./connectors-create-api-mandrill.md "连接到 Mandrill 进行通信"
[microsoft-translator-doc]: ./connectors-create-api-microsofttranslator.md "连接到 Microsoft Translator。翻译文本、检测语言和更多" 
[office-365-outlook-doc]: ./connectors-create-api-office365-outlook.md "连接到 Office 365 帐户。发送和接收电子邮件、管理日历和联系人等"
[office-365-users-doc]: ./connectors-create-api-office365-users.md 
[office-365-video-doc]: ./connectors-create-api-office365-video.md "获取视频信息、视频列表和频道，以及 Office 365 视频的播放 URL"
[onedrive-doc]: ./connectors-create-api-onedrive.md "连接到个人 Microsoft OneDrive。上传、删除、列出文件，等等"
[onedrive-for-business-doc]: ./connectors-create-api-onedriveforbusiness.md "连接到企业 Microsoft OneDrive。上传、删除、列出文件，等等"
[oracle-db-doc]: ./connectors-create-api-oracledatabase.md "连接到 Oracle 数据库以添加、插入和删除行以及执行其他操作"
[outlook.com-doc]: ./connectors-create-api-outlook.md "连接到 Outlook 邮箱。管理你的电子邮件、日历、联系人等"
[project-online-doc]: ./connectors-create-api-projectonline.md "连接到 Microsoft Project Online。管理项目、任务、资源等"
[rss-doc]: ./connectors-create-api-rss.md "发布和检索源项，在新项发布到 RSS 源时触发操作。"
[salesforce-doc]: ./connectors-create-api-salesforce.md "连接到 Salesforce 帐户。管理帐户、潜在客户、机会等"
[sap-connector-doc]: ../logic-apps/logic-apps-using-sap-connector.md "连接到本地 SAP 系统"
[sendgrid-doc]: ./connectors-create-api-sendgrid.md "连接到 SendGrid。发送电子邮件和管理收件人列表"
[sftp-doc]: ./connectors-create-api-sftp.md "连接到 SFTP 帐户。上传、获取、删除文件等"
[sharepoint-server-doc]: ./connectors-create-api-sharepointserver.md "连接到 SharePoint 本地服务器。管理文档、列表项及更多内容"
[sharepoint-online-doc]: ./connectors-create-api-sharepointonline.md "连接到 SharePoint Online。管理文档、列表项及更多内容"
[slack-doc]: ./connectors-create-api-slack.md "连接到 Slack，并将消息发布到 Slack 通道"
[smtp-doc]: ./connectors-create-api-smtp.md "连接到 SMTP 服务器并发送带附件的电子邮件"
[sparkpost-doc]: ./connectors-create-api-sparkpost.md "连接到 SparkPost 进行通信"
[sql-server-doc]: ./connectors-create-api-sqlazure.md "连接到 Azure SQL 数据库或 SQL Server。在 SQL 数据库表中创建、更新、获取和删除条目。"
[trello-doc]: ./connectors-create-api-trello.md "连接到 Trello。管理你的项目并组织任何人的任何内容"
[twilio-doc]: ./connectors-create-api-twilio.md "连接到 Twilio。发送和获取消息、获取可用号码、管理传入的电话号码等"
[twitter-doc]: ./connectors-create-api-twitter.md "连接到 Twitter。获取时间线、post 推文等"
[wunderlist-doc]: ./connectors-create-api-wunderlist.md "连接到奇妙清单。管理你的任务和待办事项列表，保持你的生活同步，等等"
[yammer-doc]: ./connectors-create-api-yammer.md "连接到 Yammer。发布消息、获取新消息等"
[youtube-doc]: ./connectors-create-api-youtube.md "连接到 YouTube。管理视频和频道"

<!--Enterprise Intregation Pack doc links-->
[as2-doc]: ../logic-apps/logic-apps-enterprise-integration-as2.md "了解企业集成 AS2。"
[edifact-decode-doc]: ../logic-apps/logic-apps-enterprise-integration-EDIFACT-decode.md "了解企业集成 EDIFACT 解码"
[edifact-encode-doc]: ../logic-apps/logic-apps-enterprise-integration-EDIFACT-encode.md "了解企业集成 EDIFACT 编码"
[flat-file-decode-doc]:../logic-apps/logic-apps-enterprise-integration-flatfile.md "了解企业集成平面文件。"
[flat-file-encode-doc]:../logic-apps/logic-apps-enterprise-integration-flatfile.md "了解企业集成平面文件。"
[integration-account-doc]: ../logic-apps/logic-apps-enterprise-integration-metadata.md "在集成帐户中查找架构、映射、合作伙伴等内容"
[json-liquid-transform-doc]: ../logic-apps/logic-apps-enterprise-integration-liquid-transform.md "了解使用 Liquid 进行的 JSON 转换"
[x12-doc]: ../logic-apps/logic-apps-enterprise-integration-x12.md "了解企业集成 X12"
[x12-decode-doc]: ../logic-apps/logic-apps-enterprise-integration-X12-decode.md "了解企业集成 X12 解码"
[x12-encode-doc]: ../logic-apps/logic-apps-enterprise-integration-X12-encode.md "了解企业集成 X12 编码"
[xml-transform-doc]: ../logic-apps/logic-apps-enterprise-integration-transform.md "了解企业集成转换。"
[xml-validate-doc]: ../logic-apps/logic-apps-enterprise-integration-xml-validation.md "了解企业集成 XML 验证。"

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
[schedule-icon]: ./media/apis-list/recurrence.png
[scope-icon]: ./media/apis-list/scope.png
[switch-icon]: ./media/apis-list/switch.png
[terminate-icon]: ./media/apis-list/terminate.png
[until-icon]: ./media/apis-list/until.png
[variables-icon]: ./media/apis-list/variables.png

<!--Managed API icons-->
[appfigures-icon]: ./media/apis-list/appfigures.png
[asana-icon]: ./media/apis-list/asana.png
[azure-automation-icon]: ./media/apis-list/azure-automation.png
[azure-blob-storage-icon]: ./media/apis-list/azure-blob-storage.png
[azure-cognitive-services-text-analytics-icon]: ./media/apis-list/azure-cognitive-services-text-analytics.png
[azure-data-lake-icon]: ./media/apis-list/azure-data-lake.png
[azure-document-db-icon]: ./media/apis-list/azure-document-db.png
[azure-event-grid-icon]: ./media/apis-list/azure-event-grid.png
[azure-event-grid-publish-icon]: ./media/apis-list/azure-event-grid-publish.png
[azure-event-hubs-icon]: ./media/apis-list/azure-event-hubs.png
[azure-ml-icon]: ./media/apis-list/azure-ml.png
[azure-queues-icon]: ./media/apis-list/azure-queues.png
[azure-resource-manager-icon]: ./media/apis-list/azure-resource-manager.png
[azure-service-bus-icon]: ./media/apis-list/azure-service-bus.png
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
[microsoft-translator-icon]: ./media/apis-list/microsoft-translator.png
[mysql-icon]: ./media/apis-list/mysql.png
[office-365-outlook-icon]: ./media/apis-list/office-365.png
[office-365-users-icon]: ./media/apis-list/office-365-users.png
[office-365-video-icon]: ./media/apis-list/office-365-video.png
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
[sftp-icon]: ./media/apis-list/sftp.png
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
[wunderlist-icon]: ./media/apis-list/wunderlist.png
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
