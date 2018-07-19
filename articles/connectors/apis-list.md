---
title: Azure 逻辑应用的连接器 | Microsoft Docs
description: 使用 Azure 逻辑应用的内置托管 API、本地集成帐户和企业连接器将工作流自动化
services: logic-apps
ms.service: logic-apps
author: ecfan
ms.author: estfan
manager: jeconnoc
ms.topic: article
ms.date: 06/29/2018
ms.reviewer: klam, LADocs
ms.suite: integration
ms.openlocfilehash: 2bb3e2ce29037372395aa0b30e9f76f3e712667d
ms.sourcegitcommit: d7725f1f20c534c102021aa4feaea7fc0d257609
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/29/2018
ms.locfileid: "37096605"
---
# <a name="connectors-for-azure-logic-apps"></a>Azure 逻辑应用的连接器

使用 Azure 逻辑应用创建自动化工作流时，连接器的作用不可或缺。 使用逻辑应用中连接器，可以扩展本地和云应用的功能，以便对创建的数据和现有的数据执行任务。 逻辑应用提供 200 多个连接器，但本文只介绍一些流行的和较常用的连接器，数千个应用和数百万次执行已成功使用这些连接器来处理数据与信息。
连接器以内置操作或托管连接器的形式提供。 

* [**内置操作**](#built-ins)：这些内置操作和触发器可帮助创建按自定义计划运行的逻辑应用、与其他终结点通信、接收和响应请求，以及调用 Azure 函数、Azure API 应用（Web 应用）、通过 Azure API 管理进行管理和发布的自有 API，和可以接收请求的嵌套逻辑应用。 还可以使用内置操作来帮助组织和控制逻辑应用工作流及处理数据。

* **托管的连接器**：这些连接器提供触发器和操作用于访问其他服务与系统。 某些连接器要求首先创建由 Azure 逻辑应用管理的连接。 托管的连接器组织成以下组：

  |   |   |
  |---|---|
  | [**托管的 API 连接器**](#managed-api-connectors) | 创建使用 Azure Blob 存储、Office 365、Dynamics、Power BI、OneDrive、Salesforce、SharePoint Online 等服务的逻辑应用。 | 
  | [**本地连接器**](#on-premises-connectors) | 安装并设置[本地数据网关][gateway-doc]后，这些连接器可以帮助逻辑应用访问 SQL Server、SharePoint Server、Oracle DB、文件共享等本地系统。 | 
  | [**集成帐户连接器**](#integration-account-connectors) | 创建和付费购买集成帐户时可以使用这些连接器，它们会转换和验证 XML、编码和解码平面文件，以及使用 AS2、EDIFACT 和 X12 协议处理企业到企业 (B2B) 消息。 | 
  | [**企业连接器**](#enterprise-connectors) | 提供对 SAP 和 IBM MQ 等企业系统的访问，但会产生额外的费用。 |
  ||| 

  例如，如果你使用 Microsoft BizTalk 服务器，则逻辑应用可以使用 [BizTalk 服务器连接器](#on-premises-connectors)连接到 BizTalk 服务器并与其通信。 
  然后，可以使用[集成帐户连接器](#integration-account-connectors)在逻辑应用中扩展或执行类似于 BizTalk 的操作。 

有关每个连接器的触发器和操作（在 Swagger 说明中定义）的技术信息及任何限制，请参阅[连接器详细信息](/connectors/)。 有关费用信息，请参阅[逻辑应用定价详细信息](https://azure.microsoft.com/pricing/details/logic-apps/)和[逻辑应用定价模型](../logic-apps/logic-apps-pricing.md)。 

<a name="built-ins"></a>

## <a name="built-ins"></a>内置操作

逻辑应用提供内置触发器和操作，因此你可以创建基于计划的工作流、帮助逻辑应用与其他应用和服务通信、通过逻辑应用控制工作流，以及管理或处理数据。 

|   |   |   |   | 
|---|---|---|---| 
| [![API 图标][schedule-icon]<br/>**计划**][recurrence-doc] | - 使用“重复周期”触发器，按指定的计划以简单到复杂的重复方案运行逻辑应用。 <p>- 使用“延迟”操作，将逻辑应用暂停指定的持续时间。 <p>- 使用“延迟截止时间”操作，将逻辑应用暂停到指定的日期和时间。 | [![API 图标][http-icon]<br/>**HTTP**][http-doc] | 使用适用于 HTTP、HTTP + Swagger 和 HTTP + Webhook 的触发器和操作，通过 HTTP 与任何终结点通信。 | 
| [![API 图标][http-request-icon]<br/>**请求**][http-request-doc] | - 使用“请求”触发器，使逻辑应用可从其他应用程序或服务调用、针对事件网格资源事件触发，或者针对 Azure 安全中心警报触发。 <p>- 使用“响应”操作将响应发送到应用或服务。 | [![API 图标][batch-icon]<br/>**Batch**][batch-doc] | - 使用“批处理消息”触发器来批量处理消息。 <p>- 使用“发送要批量处理的消息”操作调用具有现有批处理触发器的逻辑应用。 | 
| [![API 图标][azure-functions-icon]<br/>**Azure Functions**][azure-functions-doc] | 从逻辑应用调用运行自定义代码片段（C# 或 Node.js）的 Azure 函数。 | [![API 图标][azure-api-management-icon]</br>**Azure API 管理**][azure-api-management-doc] | 调用可以使用 Azure API 管理进行管理和发布的自有 API 定义的触发器与操作。 | 
| [![API icon][azure-app-services-icon]<br/>**Azure 应用服务**][azure-app-services-doc] | 调用 Azure 应用服务上托管的 Azure API 应用或 Web 应用。 包含 Swagger 后，这些应用定义的触发器和操作类似于其他任何第一类触发器和操作。 | [![API 图标][azure-logic-apps-icon]<br/>**Azure<br/>逻辑应用**][nested-logic-app-doc] | 调用使用“请求”触发器启动的其他逻辑应用。 | 
||||| 

### <a name="control-workflow"></a>控制工作流

下面是用于在逻辑应用工作流中构建和控制操作的内置操作：

|   |   |   |   | 
|---|---|---|---| 
| [![内置操作图标][condition-icon]<br/>**条件**][condition-doc] | 评估条件，并根据条件是 true 还是 false 运行不同的操作。 | [![内置操作图标][for-each-icon]</br>**For each**][for-each-doc] | 对数组中的每个项执行相同的操作。 | 
| [![内置操作图标][scope-icon]<br/>**范围**][scope-doc] | 将操作分组到范围，以便在该范围内的操作完成运行后，获取这些操作的自身状态。 | [![内置操作图标][switch-icon]</br>**开关**][switch-doc] | 将操作分组到案例，而案例分配有唯一的值（默认案例除外）。 仅运行其分配值与表达式、对象或令牌的结果相匹配的案例。 如果不存在任何匹配项，则运行默认案例。 | 
| [![内置操作图标][terminate-icon]<br/>**终止**][terminate-doc] | 停止当前正在运行的逻辑应用工作流。 | [![内置操作图标][until-icon]<br/>**直到**][until-doc] | 重复操作，直到指定的条件为 true 或某个状态发生更改。 | 
||||| 

### <a name="manage-or-manipulate-data"></a>管理或处理数据

下面是用于处理数据输出及其格式的内置操作：  

|   |   | 
|---|---| 
| ![内置操作图标][data-operations-icon]<br/>**数据操作** | 对数据执行操作： <p>- **组合**：基于具有不同类型的多个输入创建单个输出。 <br>- **创建 CSV 表**：基于包含 JSON 对象的数组创建逗号分隔值 (CSV) 表。 <br>- **创建 HTML 表**：基于包含 JSON 对象的数组创建一个 HTML 表。 <br>- **筛选数组**：基于符合条件的另一个数组中的项创建一个数组。 <br>- **联接**：基于数组中的所有项创建一个字符串，并使用指定的分隔符分隔这些项。 <br>- **分析 JSON**：基于 JSON 内容中的属性及其值创建用户友好的令牌，以便可以在工作流中使用这些属性。 <br>- **选择**：通过转换另一数组中的项或值并将这些项映射到指定的属性，创建包含 JSON 对象的数组。 | 
| ![内置操作图标][date-time-icon]<br/>**日期时间** | 对时间戳执行操作： <p>- **添加到时间**：将指定的单位数添加到时间戳。 <br>- **转换时区**：将时间戳从源时区转换为目标时区。 <br>- **当前时间**：返回字符串形式的当前时间戳。 <br>- **获取将来的时间**：返回当前时间戳加上指定的时间单位。 <br>- **获取过去的时间**：返回当前时间戳减去指定的时间单位。 <br>- **从时间减去**：从时间戳中减去一定数目的时间单位。 |
| [![内置操作图标][variables-icon]<br/>**变量**][variables-doc] | 对变量执行操作： <p>- **追加到数组变量**：插入一个值，作为变量存储的数组中的最后一个项。 <br>- **追加到字符串变量**：插入一个值，作为变量存储的字符串中的最后一个字符。 <br>- **递减变量**：按常量值减小变量。 <br>- **递增变量**：按常量值增大变量。 <br>- **初始化变量**：创建一个变量并声明其数据类型和初始值。 <br>- **设置变量**：将不同的值分配给现有变量。 |
|  |  | 

<a name="managed-api-connectors"></a>

## <a name="managed-api-connectors"></a>托管的 API 连接器

下面是用于自动化这些服务或系统的任务、流程和工作流的较流行连接器：

|   |   |   |   | 
|---|---|---|---| 
| [![API 图标][azure-service-bus-icon]<br/>**Azure 服务总线**][azure-service-bus-doc] | 使用逻辑应用中最常用的连接器管理异步消息、会话和主题订阅。 | [![API 图标][sql-server-icon]<br/>**SQL Server**][sql-server-doc] | 连接到本地 SQL Server 或云中的 Azure SQL 数据库，以便可以管理记录、运行存储过程或执行查询。 | 
| [![API 图标][office-365-outlook-icon]<br/>**Office 365<br/>Outlook**][office-365-outlook-doc] | 连接到 Office 365 电子邮件帐户，以便可以创建和管理电子邮件、任务、日历事件和会议、联系人、请求，等等。 | [![API 图标][azure-blob-storage-icon]<br/>**Azure Blob<br/>存储**][azure-blob-storage-doc] | 连接到存储帐户，以便可以创建和管理 Blob 内容。 | 
| [![API 图标][sftp-icon]<br/>**SFTP**][sftp-doc] | 连接到可从 internet 访问的 SFTP 服务器，以便能够处理文件和文件夹。 | [![API 图标][sharepoint-online-icon]<br/>**SharePoint<br/>Online**][sharepoint-online-doc] | 连接到 SharePoint Online，以便能够管理文件、附件、文件夹，等等。 | 
| [![API 图标][dynamics-365-icon]<br/>**Dynamics 365<br/>CRM Online**][dynamics-365-doc] | 连接到 Dynamics 365 帐户，以便能够创建和管理记录、项，等等。 | [![API 图标][ftp-icon]<br/>**FTP**][ftp-doc] | 连接到可从 internet 访问的 FTP 服务器，以便能够处理文件和文件夹。 | 
| [![API 图标][salesforce-icon]<br/>**Salesforce**][salesforce-doc] | 连接到 Salesforce 帐户，以便能够创建和管理记录、作业、对象等项。 | [![API 图标][twitter-icon]<br/>**Twitter**][twitter-doc] | 连接到 Twitter 帐户，以便能够管理推文、关注者、时间表、等等。 将推文保存到 SQL、Excel 或 SharePoint。 | 
| [![API 图标][azure-event-hubs-icon]<br/>**Azure 事件中心**][azure-event-hubs-doc] | 通过事件中心使用和发布事件。 例如，通过事件中心获取逻辑应用的输出，然后将输出发送到实时分析提供程序。 | [![API 图标][azure-event-grid-icon]<br/>**Azure 事件**</br>**网格**][azure-event-grid-doc] | 监视事件网格发布的事件（例如，当 Azure 资源或第三方资源发生更改时）。 | 
||||| 

<a name="on-premises-connectors"></a>

## <a name="on-premises-connectors"></a>本地连接器 

下面这些常用连接器提供对本地系统中数据和资源的访问。 在与本地系统建立连接之前，必须先[下载、安装并设置本地数据网关][gateway-doc]。 此网关提供安全信道，无需设置所需的网络基础结构。 

|   |   |   |   |   | 
|---|---|---|---|---| 
| ![API 图标][biztalk-server-icon]<br/>**BizTalk**</br> **服务器** | [![API 图标][file-system-icon]<br/>**文件</br>系统**][file-system-doc] | [![API 图标][ibm-db2-icon]<br/>**IBM DB2**][ibm-db2-doc] | [![API 图标][ibm-informix-icon]<br/>**IBM**</br> **Informix**][ibm-informix-doc] | ![API 图标][mysql-icon]<br/>**MySQL** | 
| [![API 图标][oracle-db-icon]<br/>**Oracle DB**][oracle-db-doc] | ![API 图标][postgre-sql-icon]<br/>**PostgreSQL** | [![API 图标][sharepoint-server-icon]<br/>**SharePoint</br> Server**][sharepoint-server-doc] | [![API 图标][sql-server-icon]<br/>**SQL</br> Server**][sql-server-doc] | ![API 图标][teradata-icon]<br/>**Teradata** | 
||||| 

<a name="integration-account-connectors"></a>

## <a name="integration-account-connectors"></a>集成帐户连接器 

创建和付费购买[集成帐户](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md)（通过 Azure 中的 Enterprise Integration Pack (EIP) 提供）时，可以使用以下连接器在逻辑应用中生成企业到企业 (B2B) 解决方案。 使用此帐户可以创建和存储 B2B 项目，例如贸易合作伙伴、协议、映射、架构、证书，等等。 若要使用这些项目，请将逻辑应用与集成帐户相关联。 如果你当前使用的是 BizTalk 服务器，则可能已熟悉这些连接器的用法。

|   |   |   |   | 
|---|---|---|---| 
| [![API 图标][as2-icon]<br/>**AS2</br> 解码**][as2-decode-doc] | [![API 图标][as2-icon]<br/>**AS2</br> 编码**][as2-encode-doc] | [![API 图标][edifact-icon]<br/>**EDIFACT</br> 解码**][edifact-decode-doc] | [![API 图标][edifact-icon]<br/>**EDIFACT</br> 编码**][edifact-encode-doc] | 
| [![API 图标][flat-file-decode-icon]<br/>**平面文件</br>解码**][flat-file-decode-doc] | [![API 图标][flat-file-encode-icon]<br/>**平面文件</br>编码**][flat-file-encode-doc] | [![API 图标][integration-account-icon]<br/>**集成<br/>帐户**][integration-account-doc] | [![API 图标][liquid-icon]<br/>**Liquid**</br>**转换**][json-liquid-transform-doc] | 
| [![API 图标][x12-icon]<br/>**X12</br> 解码**][x12-decode-doc] | [![API 图标][x12-icon]<br/>**X12</br> 编码**][x12-encode-doc] | [![API 图标][xml-transform-icon]<br/>**XML**</br>**转换**][xml-transform-doc] | [![API 图标][xml-validate-icon]<br/>**XML <br/>验证**][xml-validate-doc] |  
||||| 

<a name="enterprise-connectors"></a>

## <a name="enterprise-connectors"></a>企业连接器

逻辑应用可以访问 SAP 和 IBM MQ 等企业系统：

|   |   | 
|---|---| 
| [![API 图标][ibm-mq-icon]<br/>**IBM MQ**][ibm-mq-doc] | [![API 图标][sap-icon]<br/>**SAP**][sap-connector-doc] |
||| 

## <a name="more-about-triggers-and-actions"></a>有关触发器和操作的详细信息

某些连接器提供触发器，在发生特定事件时通知逻辑应用。 因此，在发生这些事件时，触发器将创建并运行逻辑应用的实例。 例如，FTP 连接器提供“添加或修改文件时”触发器，用于在更新文件时启动逻辑应用。 

逻辑应用提供以下类型的触发器：  

* 轮询触发器：这些触发器以指定的频率轮询服务和检查新数据。 

  有新数据可用时，将创建逻辑应用的新实例，并使用作为输入传递的数据运行该实例。 

* 推送触发器：这些触发器侦听终结点上的新数据或要发生的事件，然后创建并运行逻辑应用的新实例。

* *重复周期触发器*：此触发器根据指定的计划创建并运行逻辑应用的实例。

连接器还提供可在逻辑应用工作流中执行任务的操作。 例如，逻辑应用可以读取数据，并在逻辑应用的后续步骤中使用此数据。 更具体地说，逻辑应用可以查找 SQL 数据库中的客户数据，然后在逻辑应用的工作流中处理此数据。 

有关触发器和操作的详细信息，请参阅[连接器概述](connectors-overview.md)。 

## <a name="custom-apis-and-connectors"></a>自定义 API 和连接器 

若要调用运行自定义代码或者无法作为连接器使用的 API，可以通过[创建自定义 API 应用](../logic-apps/logic-apps-create-api-app.md)来扩展逻辑应用平台。 还可以针对任何基于 REST 或 SOAP 的 API [创建自定义连接器](../logic-apps/custom-connector-overview.md)，使这些 API 可供 Azure 订阅中的任何逻辑应用使用。
若要使自定义 API 应用或连接器可供任何人在 Azure 中使用，可以[提交连接器进行 Microsoft 认证](../logic-apps/custom-connector-submit-certification.md)。

## <a name="get-support"></a>获取支持

* 有关问题，请访问 [Azure 逻辑应用论坛](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps)。

* 若要提交或投票有关 Azure 逻辑应用和连接器的想法，请访问[逻辑应用用户反馈站点](http://aka.ms/logicapps-wish)。

* 文档中是否缺少你认为很重要的文章或详细信息？ 如果是，你可以在现有文章中补充内容或编写自己的文章。 我们的文档是开源的，托管在 GitHub 上。 使用 Azure 文档的 [GitHub 存储库](https://github.com/Microsoft/azure-docs)入门。 

## <a name="next-steps"></a>后续步骤

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
[switch-doc]: ../logic-apps/logic-apps-control-flow-switch-statement.md "将操作组织成分配有唯一值的案例。仅运行其值与表达式、对象或令牌的结果相匹配的案例。如果不存在任何匹配项，则运行默认案例"
[terminate-doc]: ../logic-apps/logic-apps-workflow-actions-triggers.md#terminate-action "停止或取消逻辑应用的正在运行的工作流"
[until-doc]: ../logic-apps/logic-apps-control-flow-loops.md#until-loop "重复操作，直到指定的条件为 true 或某个状态发生更改"
[variables-doc]: ../logic-apps/logic-apps-create-variables-store-values.md "使用变量执行操作，例如初始化、设置、递增、递减和追加到字符串或数组变量"

<!--Managed API doc links-->
[azure-blob-storage-doc]: ./connectors-create-api-azureblobstorage.md "使用 Azure Blob 存储连接器管理 Blob 容器中的文件"
[azure-event-grid-doc]: ../event-grid/monitor-virtual-machine-changes-event-grid-logic-app.md "监视事件网格发布的事件（例如，当 Azure 资源或第三方资源发生更改时）"
[azure-event-hubs-doc]: ./connectors-create-api-azure-event-hubs.md "连接到 Azure 事件中心。在逻辑应用与事件中心之间接收和发送事件"
[box-doc]: ./connectors-create-api-box.md "连接到 Box。上传、获取、删除、列出文件，等等"
[dropbox-doc]: ./connectors-create-api-dropbox.md "连接到 Dropbox。上传、获取、删除、列出文件，等等"
[dynamics-365-doc]: ./connectors-create-api-crmonline.md "连接到 Dynamics CRM Online，以便使用 CRM Online 数据"
[facebook-doc]: ./connectors-create-api-facebook.md "连接到 Facebook。发布到时间线、获取页面源，等等"
[file-system-doc]: ../logic-apps/logic-apps-using-file-connector.md "连接到本地文件系统"
[ftp-doc]: ./connectors-create-api-ftp.md "连接到 FTP/FTPS 服务器以执行 FTP 任务，例如上传、获取、删除文件，等等"
[github-doc]: ./connectors-create-api-github.md "连接到 GitHub，对问题进行跟踪"
[google-calendar-doc]: ./connectors-create-api-googlecalendar.md "连接到 Google Calendar 即可管理日历。"
[google-drive-doc]: ./connectors-create-api-googledrive.md "连接到 GoogleDrive，以便使用数据"
[google-sheets-doc]: ./connectors-create-api-googlesheet.md "连接到 Google Sheets 以便可以修改表单"
[google-tasks-doc]: ./connectors-create-api-googletasks.md "连接到 Google Tasks，以便管理任务"
[ibm-db2-doc]: ./connectors-create-api-db2.md "连接到云中或本地的 IBM DB2。更新行、获取表，等等"
[ibm-informix-doc]: ./connectors-create-api-informix.md "连接到云中或本地的 Informix。读取行、列出表，等等"
[ibm-mq-doc]: ./connectors-create-api-mq.md "连接到本地或 Azure 中的 IBM MQ 以发送和接收消息"
[instagram-doc]: ./connectors-create-api-instagram.md "连接到 Instagram。触发事件或针对事件进行操作"
[mailchimp-doc]: ./connectors-create-api-mailchimp.md "连接到 MailChimp 帐户。管理邮件和自动执行邮件操作"
[mandrill-doc]: ./connectors-create-api-mandrill.md "连接到 Mandrill 进行通信"
[microsoft-translator-doc]: ./connectors-create-api-microsofttranslator.md "连接到 Microsoft Translator。翻译文字、检测语言，等等" 
[office-365-outlook-doc]: ./connectors-create-api-office365-outlook.md "连接到 Office 365 帐户。发送和接收电子邮件、管理日历和联系人，等等"
[office-365-users-doc]: ./connectors-create-api-office365-users.md 
[office-365-video-doc]: ./connectors-create-api-office365-video.md "获取视频信息、视频列表和频道，以及 Office 365 视频的播放 URL"
[onedrive-doc]: ./connectors-create-api-onedrive.md "连接到个人 Microsoft OneDrive。上传、删除、列出文件，等等"
[onedrive-for-business-doc]: ./connectors-create-api-onedriveforbusiness.md "连接到企业 Microsoft OneDrive。上传、删除、列出文件，等等"
[oracle-db-doc]: ./connectors-create-api-oracledatabase.md "连接到 Oracle 数据库以添加、插入和删除行以及执行其他操作"
[outlook.com-doc]: ./connectors-create-api-outlook.md "连接到 Outlook 邮箱。管理电子邮件、日历、联系人等"
[project-online-doc]: ./connectors-create-api-projectonline.md "连接到 Microsoft Project Online。管理项目、任务、资源等"
[rss-doc]: ./connectors-create-api-rss.md "发布和检索源项，在新项发布到 RSS 源时触发操作。"
[salesforce-doc]: ./connectors-create-api-salesforce.md "连接到 Salesforce 帐户。管理帐户、潜在客户、商机等"
[sap-connector-doc]: ../logic-apps/logic-apps-using-sap-connector.md "连接到本地 SAP 系统"
[sendgrid-doc]: ./connectors-create-api-sendgrid.md "连接到 SendGrid。发送电子邮件和管理收件人列表"
[sftp-doc]: ./connectors-create-api-sftp.md "连接到 SFTP 帐户。上传、获取、删除文件，等等"
[sharepoint-server-doc]: ./connectors-create-api-sharepointserver.md "连接到 SharePoint 本地服务器。管理文档、列出项，等等"
[sharepoint-online-doc]: ./connectors-create-api-sharepointonline.md "连接到 SharePoint Online。管理文档、列出项，等等"
[slack-doc]: ./connectors-create-api-slack.md "连接到 Slack，并将消息发布到 Slack 通道"
[smtp-doc]: ./connectors-create-api-smtp.md "连接到 SMTP 服务器并发送带附件的电子邮件"
[sparkpost-doc]: ./connectors-create-api-sparkpost.md "连接到 SparkPost 进行通信"
[sql-server-doc]: ./connectors-create-api-sqlazure.md "连接到 Azure SQL 数据库或 SQL Server。在 SQL 数据库表中创建、更新、获取和删除条目。"
[trello-doc]: ./connectors-create-api-trello.md "连接到 Trello。管理项目，与任何人一起组织任何事情"
[twilio-doc]: ./connectors-create-api-twilio.md "连接到 Twilio。发送和获取消息、获取可用号码，管理来电号码，等等"
[twitter-doc]: ./connectors-create-api-twitter.md "连接到 Twitter。获取时间线、发布推文，等等"
[wunderlist-doc]: ./connectors-create-api-wunderlist.md "连接到 Wunderlist。管理任务和待办事项列表，让生活有条不紊，等等"
[yammer-doc]: ./connectors-create-api-yammer.md "连接到 Yammer。发布消息、获取新消息，等等"
[youtube-doc]: ./connectors-create-api-youtube.md "连接到 YouTube。管理视频和频道"

<!--Enterprise Intregation Pack doc links-->
[as2-doc]: ../logic-apps/logic-apps-enterprise-integration-as2.md "了解企业集成 AS2。"
[as2-decode-doc]: ../logic-apps/logic-apps-enterprise-integration-as2-decode.md "了解企业集成 AS2 解码"
[as2-encode-doc]:../logic-apps/logic-apps-enterprise-integration-as2-encode.md "了解企业集成 AS2 编码"
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