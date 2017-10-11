---
title: "Azure 逻辑应用的连接器 | Microsoft Docs"
description: "从所有可用的 Microsoft 托管连接器中进行选择，以生成并创建逻辑应用"
services: logic-apps
documentationcenter: 
author: MandiOhlinger
manager: anneta
editor: 
tags: connectors
ms.assetid: f1f1fd50-b7f9-4d13-824a-39678619aa7a
ms.service: logic-apps
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 06/21/2017
ms.author: mandia; ladocs
ms.openlocfilehash: c14ac7592efabfec8668d7437463e2d8771ee072
ms.sourcegitcommit: 02e69c4a9d17645633357fe3d46677c2ff22c85a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/03/2017
---
# <a name="connectors-list"></a>连接器列表
> [!TIP]
> （本主题中的）[A-Z 完整列表](#az)列出了所有可以用在逻辑应用中的连接器。 [连接器详细信息](/connectors/)列出了在 Swagger 中定义的触发器和操作，同时还列出了每个连接器的限制。

在创建逻辑应用时，连接器是不可或缺的一部分。 使用这些连接器，你可以真正展开你的本地和云应用程序来执行不同操作使用你所创建的数据和已有的数据。 提供的连接器有以下类别： 

* **标准连接器**：在使用逻辑应用时自动提供。 示例包括服务总线、Power BI、Oracle 数据库、OneDrive 等。

* 集成帐户连接器：在购买集成帐户时提供。 使用这些连接器可以转换和验证 XML、处理 AS2/X12/EDIFACT 格式的企业到企业消息，以及对平面文件进行编码和解码。 如果使用 BizTalk Server，则可选择这些连接器将 BizTalk 工作流扩展到 Azure 中。  

    BizTalk Server 还有一个[逻辑应用适配器](https://msdn.microsoft.com/library/mt787163.aspx)，既可从逻辑应用接收数据，也可向逻辑应用发送数据。

* **企业连接器**：包括 MQ 和 SAP。 需支付额外费用。 

[逻辑应用定价](https://azure.microsoft.com/pricing/details/logic-apps/)和[定价模型](../logic-apps/logic-apps-pricing.md)提供了有关成本的更多详细信息。 

## <a name="popular-connectors"></a>常用连接器
成千上万的应用程序和执行操作成功使用这些连接器处理数据和信息。 下表列出了用户最常用和最喜欢的一些连接器：

| |  |  |  |
| --- | --- | --- | --- |
| [![API Icon][AzureBlobStorageicon]<br/>**Azure Blob<br/>存储**][AzureBlobStoragedoc] | 如果需要自动完成存储帐户的任务，则应使用此连接器。 支持 CRUD（创建、读取、更新、删除）操作。 | [![API 图标][Azure-Functionsicon]<br/>**Azure Functions**][azure-functionsdoc] | 创建运行 C# 或 node.js 自定义代码片段的函数，并在逻辑应用中使用这些函数。  |
| [![API 图标][Dynamics-365icon]<br/>**Dynamics 365<br/>CRM Online**][Dynamics-365doc] | 最受欢迎的连接器之一。 它包含触发器和操作、有助于自动完成潜在客户的工作流，等等。 | [![API 图标][Event-Hubs-icon]<br/>**事件中心**][event-hubs-doc] | 在事件中心使用和发布事件。 例如，可以通过事件中心获取逻辑应用的输出，然后将输出发送到实时分析提供程序。 |
| [![API Icon][FTPicon]<br/>**FTP**][FTPdoc] | 如果可以从 Internet 访问 FTP 服务器，则可自动完成用于处理文件和文件夹的工作流。 <br/><br/>也可通过 SFTP 连接器来使用 SFTP。 | [![API Icon][HTTPicon]<br/>**HTTP**][httpdoc] | 使用逻辑应用与任何基于 HTTP 的终结点通信。 |
| [![API Icon][Office-365-Outlookicon]<br/>**Office 365<br/>Outlook**][office365-outlookdoc] | 大量的触发器，以及数量更多的操作，便于在工作流中使用 Office 365 电子邮件和事件。 <br/><br/>此连接器包括“审批电子邮件”操作，用于审批休假请求、费用报表等。 <br/><br/>也可通过 Office 365 用户连接器使用 Office 365 用户。| [![API 图标][HTTP-Requesticon]<br/>**请求/响应**][HTTP-Requestdoc] | 此连接器提供 HTTPS URL。 当逻辑应用收到向此 URL 发出的请求时，逻辑应用就会启动。 |
| [![API Icon][Salesforceicon]<br/>**Salesforce**][salesforcedoc] | 轻松使用 Salesforce 帐户登录，以便访问各种对象，例如潜在客户等。 |  [![API Icon][Service-Busicon]<br/>**服务总线**][Service-Busdoc] | 逻辑应用中最常用的连接器，其包括的触发器和操作适用于异步消息传送，以及队列、订阅和主题的发布/订阅。 |
|  [![API 图标][SharePointicon]<br/>**SharePoint<br/>Online**][SharePointdoc] | 如果通过 SharePoint 进行操作，并且可以利用自动化，则建议使用此连接器。 可以与本地 SharePoint 以及 SharePoint Online 配合使用。 | [![API Icon][SQL-Servericon]<br/>**SQL Server**][SQL-Serverdoc] | 使用次数最多的连接器之一，可以连接到本地 SQL Server 以及 Azure SQL 数据库。 | 
| [![API Icon][Twittericon]<br/>**Twitter**][Twitterdoc] | 轻松使用 Twitter 帐户登录，并在有新推文发布时启动工作流。 然后，将这些推文保存到 SQL 数据库或 SharePoint 列表。 | | | 

## <a name="integration-account-connectors"></a>集成帐户连接器 

Enterprise Integration Pack (EIP) 包括 BizTalk Server 社区众所周知的连接器。 购买[集成帐户](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md)时，还会获得以下连接器： 

|  |  |  |  |
| --- | --- | --- | --- |
| [![API Icon][as2icon]<br/>**AS2</br> 解码**][as2decode] | [![API Icon][as2icon]<br/>**AS2</br> 编码**][as2encode] | [![API Icon][x12icon]<br/>**EDIFACT</br> 解码**][EDIFACTdecode] | [![API Icon][x12icon]<br/>**EDIFACT</br> 编码**][EDIFACTencode] |
[![API Icon][flatfileicon]<br/>**平面文件</br>编码**][flatfiledoc] | [![API Icon][flatfiledecodeicon]<br/>**平面文件</br>解码**][flatfiledecodedoc] | [![API 图标][integrationaccounticon]<br/>**集成<br/>帐户**][integrationaccountdoc] | [![API 图标][xmltransformicon]<br/>**转换<br/>XML**][xmltransformdoc] |
| [![API Icon][x12icon]<br/>**X12</br> 解码**][x12decode] | [![API Icon][x12icon]<br/>**X12</br> 解码**][x12encode] | [![API Icon][xmlvalidateicon]<br/>**XML <br/>验证**][xmlvalidatedoc] | |

## <a name="enterprise-connectors"></a>企业连接器

连接到逻辑应用中的企业应用程序。

|  |  |
| --- | --- |
|[![API 图标][MQicon]<br/>MQ][mqdoc]|[![API Icon][SAPicon]<br/>**SAP**][sapconnector]|


## <a name="az"></a>A 到 Z 的完整列表

[连接器详细信息](/connectors/)列出了在 Swagger 中定义的触发器和操作，同时还列出了每个连接器的限制。

| | | | | | | | | | | | | |
|---|---|---|---|---|---|---|---|---|---|---|---|---|
| [**1**](#1) | [**A**](#a) | [**B**](#b) | [**C**](#c) | [**D**](#d) | [**E**](#e) | [**F**](#f) | [**G**](#g) | [**H**](#h) | [**我**](#i) | [**J**](#j) | [**L**](#l) | [**M**](#m) |
| [**N**](#n) | [**O**](#o) | [**P**](#p) | [**R**](#r) | [**S**](#s) | [**T**](#t) | [**U**](#u) | [**V**](#v) | [**W**](#w) | [**X**](#x) | [**Y**](#y) | [**Z**](#z) | | 

| | |
|---|---|
|<a name="1"></a>10to8 约会日程安排<br/><br/><a name="a"></a>Act ！<br/>Adobe Creative Cloud<br/>appFigures<br/>[AS2][as2doc]<br/>Asana<br/>Azure Active Directory (AD)<br/>Azure API 管理<br/>Azure 应用服务<br/>Azure 应用程序<br/>Azure 自动化<br/>[Azure Blob 存储][azureblobstoragedoc]<br/>Azure Data Lake<br/>Azure DocumentDB (Cosmos DB)<br/>[Azure Functions][azure-functionsdoc]<br/>[Azure 逻辑应用][nested-logic-appdoc]<br/>AzureML<br/>Azure 队列<br/>Azure Resource Manager<br/>[Azure SQL 数据库][sql-serverdoc]<br/><br/><a name="b"></a>Basecamp 2<br/>Basecamp 3<br/>批处理<br/>Benchmark Email<br/>必应搜索<br/>Bitbucket<br/>Bitly<br/>BizTalk Server<br/>Blogger<br/>Box<br/>Buffer<br/><br/><a name="c"></a>Calendly<br/>Campfire<br/>Capsule CRM<br/>Chatter<br/>Cognito Forms<br/>认知服务计算机视觉 API<br/>认知服务人脸 API<br/>认知服务 LUIS<br/>认知服务文本分析<br/>Common Data Service<br/>内容转换<br/>Control-Terminate<br/>[自定义 API/Web 应用][api/web-appdoc]<br/><br/><a name="d"></a>数据操作<br/>[DB2][db2doc]<br/>Disqus<br/>DocuSign<br/>Do Until<br/>Dropbox<br/>[Dynamics 365 CRM Online][Dynamics-365doc]<br/>Dynamics 365 for Financials<br/>Dynamics 365 for Operations<br/>Dynamics NAV<br/><br/><a name="e"></a>轻松 Redmine<br/>EDIFACT<br/>[事件中心][event-hubs-doc]<br/>Eventbrite<br/><br/><a name="f"></a>Facebook<br/>[文件系统][filesystemdoc]<br/>[平面文件][flatfiledoc]<br/>FreshBooks<br/>Freshdesk<br/>Freshservice<br/>[FTP][ftpdoc]<br/><br/><a name="g"></a>GitHub<br/>Gmail<br/>Google Calendar<br/>Google Contacts<br/>Google Drive<br/>Google Sheets<br/>Google Tasks<br/>GoToMeeting<br/>GoToTraining<br/>GoToWebinar<br/><br/><a name="h"></a>收集<br/>HelloSign<br/>HipChat<br/>[HTTP][httpdoc]<br/>[HTTP + Swagger][http-swaggerdoc]<br/>[HTTP Webhook][webhookdoc]<br/><br/><a name="i"></a>[Informix][informixdoc]<br/>Infusionsoft<br/>Inoreader<br/>Insightly<br/>Instagram<br/>Instapaper<br/>集成帐户<br/>Intercom | <a name="j"></a>JotForm<br/>JIRA<br/><br/><a name="l"></a>LeanKit<br/>LiveChat<br/><br/><a name="m"></a>MailChimp<br/>Mandrill<br/>中型<br/>Microsoft Forms<br/>Microsoft Teams<br/>Microsoft Translator<br/>[MQ][mqdoc]<br/>MSN 天气<br/>Muhimbi PDF<br/>MySQL<br/><br/><a name="n"></a>Nexmo<br/><br/><a name="o"></a>[Office 365 Outlook][office365-outlookdoc]<br/>Office 365 用户<br/>Office 365 视频<br/>OneDrive<br/>OneDrive for Business<br/>OneNote（企业）<br/>[Oracle 数据库][oracle-db-doc]<br/>Outlook 客户管理器<br/>Outlook 任务<br/>Outlook.com<br/><br/><a name="p"></a>PagerDuty<br/>Parserr<br/>Paylocity<br/>Pinterest<br/>Pipedrive<br/>Pivotal Tracker<br/>Planner<br/>PostgreSQL<br/>Power BI<br/>Project Online<br/><br/><a name="r"></a>Redmine<br/>[请求/响应][http-requestdoc]<br/>RSS<br/><br/><a name="s"></a>[Salesforce][salesforcedoc]<br/>[SAP Application Server][sapconnector]<br/>[SAP Message Server][sapconnector]<br/>[计划][recurrencedoc]<br/>范围<br/>SendGrid<br/>将消息发送到批处理<br/>[服务总线][service-busdoc]<br/>SFTP<br/>[SharePoint Online][sharepointdoc]<br/>[SharePoint Server][sharepointserver]<br/>Slack<br/>Smartsheet<br/>SMTP<br/>SparkPost<br/>[SQL Server][sql-serverdoc]<br/>Stripe<br/>SurveyMonkey<br/>Switch Case<br/><br/><a name="t"></a>团队协作项目<br/>Teradata<br/>Todoist<br/>Toodledo<br/>[转换 XML][xmltransformdoc]<br/>Trello<br/>Twilio<br/>[Twitter][twitterdoc]<br/>Typeform<br/><br/><a name="u"></a>UserVoice<br/><br/><a name="v"></a>变量<br/>Vimeo<br/>Visual Studio Team Services<br/><br/><a name="w"></a>WebMerge<br/>WordPress<br/>Wunderlist<br/><br/><a name="x"></a>[X12][x12doc]<br/>[XML 验证][xmlvalidatedoc]<br/><br/><a name="y"></a>Yammer<br/>YouTube<br/><br/><a name="z"></a>Zendesk |

> [!TIP]
> 若要在注册 Azure 帐户之前开始使用 Azure 逻辑应用，请转到[试用逻辑应用](https://tryappservice.azure.com/?appservice=logic)。 可立即创建短期的初学者逻辑应用。 不需要使用信用卡，也不需要做出承诺。

## <a name="connectors-as-triggers-and-actions"></a>用作触发器和操作的连接器

**触发器**可启动或运行逻辑应用的实例。 某些连接器提供触发器，在发生特定事件时通知应用。 例如，FTP 连接器提供 `OnUpdatedFile` 触发器，在更新文件时启动逻辑应用。 

逻辑应用包括以下类型的触发器：  

* *轮询触发器*：这些触发器以指定的频率轮询服务，检查是否有新数据。 

    有新数据可用时，将运行逻辑应用的新实例并以该数据作为输入。 

* *推送触发器*：这些触发器侦听终结点上的数据或要发生的事件，并触发逻辑应用的新实例。

* *定期触发器*：此触发器按规定的计划实例化逻辑应用。

连接器还提供可在工作流中使用的**操作**。 例如，可以通过逻辑应用查找数据，再在逻辑应用中使用该数据。 更具体地说，可以在 SQL 数据库中查找客户数据，并使用该客户数据生成工作流。 

> [!TIP]
> [连接器概述](connectors-overview.md)提供了有关触发器和操作的更多详细信息。 


## <a name="message-manipulation-actions"></a>消息操纵操作

逻辑应用包括内置的操作，可以用来更改或操纵有效负载数据。 内置的“数据操作”连接器包括以下操作： 

| | |
|---|---|
| **编制** | 生成以后使用或在生成工作流时使用的值或对象。 例如，可以创作一个其值来自多个步骤的 JSON 对象，也可以计算一个在以后的逻辑应用运行中引用的常量。 |
| **创建 CSV 表**<br/>**创建 HTML 表** | 将数组结果集转换为 CSV 或 HTML 表。 例如，添加 CRM“列出记录”操作，以及为今天添加的记录添加筛选器。 然后，将结果作为电子邮件中的 HTML 表发送。 |
| **筛选数组**（查询） | 从结果集中筛选出感兴趣的条目。 例如，使用 `#Azure` 搜索所有推文，并对返回的推文进行“筛选”，仅返回 `Tweeted_by_followers > 50` 的结果。 |
| **Join** | 通过某个分隔符来联接数组。 例如，“检测关键短语”操作返回包含关键短语的数组。 可以使用 `,` 或类似的符号来“联接”它们。 因此，请采用 `"Some, Phrase"` 格式，而不是 `["Some", "Phrase"]` 格式。 |
| **分析 JSON** | 分析并访问设计器中的 JSON 对象提供的值。 例如，如果 Azure Function 返回 JSON 有效负载，则可对其进行分析，以便以后在另一步骤中访问 JSON 属性。 该操作还验证 JSON 是否与运行时的指定架构匹配。 | 
| **Select** | 选择某个数组的特定属性进行进一步的处理。 如果从 SQL“列出记录”时返回了 15 个列，则只选择其中一部分进行进一步的处理。 输出是只包含所选属性的数组。 |

## <a name="custom-connectors-and-azure-certification"></a>自定义连接器和 Azure 认证 

要调用的 API 运行自定义代码，或者无法作为连接器使用，可以[将基于 REST 的 API 应用作为自定义连接器创建](../logic-apps/logic-apps-create-api-app.md)，以便扩展逻辑应用平台。 

要将自定义 API 应用公开并使其可以在 Azure 中使用，则请将提名的应用提交到 [Microsoft Azure 认证计划](https://azure.microsoft.com/marketplace/programs/certified/logic-apps/)。

## <a name="get-help"></a>获取帮助

若要提问、解答问题以及了解其他 Azure 逻辑应用用户的活动，请访问 [Azure 逻辑应用论坛](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps)。

为了帮助我们改进 Azure 逻辑应用和连接器，敬请在[逻辑应用用户反馈站点](http://aka.ms/logicapps-wish)上投票或发表看法。

我们是否缺少连接器主题，或者你认为哪些细节很重要？ 如果是，请帮帮忙，为我们的现有主题添加相关内容，或者写下你自己的主题。 我们的文档是开源的，托管在 GitHub 上。 [GitHub 存储库](https://github.com/Microsoft/azure-docs)入门。 

## <a name="next-steps"></a>后续步骤
* [创建第一个逻辑应用](../logic-apps/logic-apps-create-a-logic-app.md)
* [为逻辑应用创建自定义 API](../logic-apps/logic-apps-create-api-app.md)
* [监视逻辑应用](../logic-apps/logic-apps-monitor-your-logic-apps.md)

<!--Connectors Documentation-->

[api/web-appdoc]: ../logic-apps/logic-apps-custom-hosted-api.md "将逻辑 apps 与 App Service API Apps 集成"
[azureblobstoragedoc]: ./connectors-create-api-azureblobstorage.md "管理与 Azure blob 存储连接器 blob 容器中的文件"
[azure-functionsdoc]: ../logic-apps/logic-apps-azure-functions.md "将逻辑 apps 与 Azure 函数集成"
[db2doc]: ./connectors-create-api-db2.md "连接到在云中或本地的 IBM DB2。更新行、获取表，等等"
[Dynamics-365doc]: ./connectors-create-api-crmonline.md "连接到 Dynamics CRM Online，因此你可以使用 CRM Online 数据"
[event-hubs-doc]: ./connectors-create-api-azure-event-hubs.md "连接到 Azure 事件中心。在逻辑应用与事件中心之间接收和发送事件"
[filesystemdoc]: ../logic-apps/logic-apps-using-file-connector.md "连接到本地文件系统"
连接到 FTP/FTPS 服务器以执行 FTP 任务，例如上传、获取、删除文件，等等
[httpdoc]: ./connectors-native-http.md "执行与 HTTP 连接器的 HTTP 调用"
[http-requestdoc]: ./connectors-native-reqres.md "添加用于 HTTP 请求和响应你的 logic apps 操作"
[http-swaggerdoc]: ./connectors-native-http-swagger.md "请使用 HTTP + Swagger 的 HTTP 调用连接器"
[informixdoc]: ./connectors-create-api-informix.md "连接到 Informix 在云中还是在本地。读取行、列出表，等等"
[nested-logic-appdoc]: ../logic-apps/logic-apps-http-endpoint.md "将逻辑应用与嵌套工作流集成"
[office365-outlookdoc]: ./connectors-create-api-office365-outlook.md "连接到 Office 365 帐户。发送和接收电子邮件、管理日历和联系人，等等"
[oracle-db-doc]: ./connectors-create-api-oracledatabase.md "连接到 Oracle 数据库来添加、 插入、 删除行和详细信息"
[mqdoc]: ./connectors-create-api-mq.md "连接到 MQ 本地或 Azure，并发送和接收消息"
[recurrencedoc]:  ./connectors-native-recurrence.md "触发对于 logic apps 的重复执行操作"
[salesforcedoc]: ./connectors-create-api-salesforce.md "连接到你的 Salesforce 帐户。管理帐户、潜在客户、商机等"
[sapconnector]: ../logic-apps/logic-apps-using-sap-connector.md "连接到本地 SAP 系统"
[service-busdoc]: ./connectors-create-api-servicebus.md "从 Service Bus 队列和主题发送消息和从 Service Bus 队列和订阅接收消息"
[sharepointdoc]: ./connectors-create-api-sharepointonline.md "连接到 SharePoint Online。管理文档、列出项，等等"
[sharepointserver]: ./connectors-create-api-sharepointserver.md "连接到 SharePoint 服务器上的本地。管理文档、列出项，等等"
[sql-serverdoc]: ./connectors-create-api-sqlazure.md "连接到 Azure SQL 数据库或 SQL server。在 SQL 数据库表中创建、更新、获取和删除条目。"
[twitterdoc]: ./connectors-create-api-twitter.md "连接到 Twitter。获取时间线、发布推文，等等"
[webhookdoc]: ./connectors-native-webhook.md "将 Webhook 操作和触发器添加到你的 logic apps"

[as2doc]: ../logic-apps/logic-apps-enterprise-integration-as2.md "了解有关企业集成 AS2。"
[x12doc]: ../logic-apps/logic-apps-enterprise-integration-x12.md "了解有关企业集成 X12"
[flatfiledoc]:../logic-apps/logic-apps-enterprise-integration-flatfile.md "了解企业集成平面文件。"
[flatfiledecodedoc]:../logic-apps/logic-apps-enterprise-integration-flatfile.md "了解企业集成平面文件。"
[xmlvalidatedoc]: ../logic-apps/logic-apps-enterprise-integration-xml-validation.md "了解有关企业集成 XML 验证。"
[xmltransformdoc]: ../logic-apps/logic-apps-enterprise-integration-transform.md "了解有关企业集成转换。"
[as2decode]: ../logic-apps/logic-apps-enterprise-integration-as2-decode.md "了解有关 AS2 解码的企业集成"
[as2encode]:../logic-apps/logic-apps-enterprise-integration-as2-encode.md "了解有关企业集成 AS2 编码"
[X12decode]: ../logic-apps/logic-apps-enterprise-integration-X12-decode.md "了解有关企业集成 X12 解码"
[X12encode]: ../logic-apps/logic-apps-enterprise-integration-X12-encode.md "了解有关企业集成 X12 编码"
[EDIFACTdecode]: ../logic-apps/logic-apps-enterprise-integration-EDIFACT-decode.md "了解有关 EDIFACT 解码的企业集成"
[EDIFACTencode]: ../logic-apps/logic-apps-enterprise-integration-EDIFACT-encode.md "了解有关企业集成 EDIFACT 编码"
[integrationaccountdoc]: ../logic-apps/logic-apps-enterprise-integration-metadata.md "查找架构、 地图、 合作伙伴和集成帐户中的详细信息"


[boxDoc]: ./connectors-create-api-box.md "连接到框中。上传、获取、删除、列出文件，等等"
[delaydoc]: ./connectors-native-delay.md "执行延迟的操作"
[dropboxdoc]: ./connectors-create-api-dropbox.md "连接到 Dropbox。上传、获取、删除、列出文件，等等"
[facebookdoc]: ./connectors-create-api-facebook.md "连接到 Facebook。发布到时间线、获取页面源，等等"
[githubdoc]: ./connectors-create-api-github.md "连接到 GitHub 和跟踪问题"
[google-drivedoc]: ./connectors-create-api-googledrive.md "连接到 GoogleDrive，这样你可以使用你的数据"
[google-sheetsdoc]: ./connectors-create-api-googlesheet.md "连接到 Google 工作表，因此您可以修改可以将表"
[google-tasksdoc]: ./connectors-create-api-googletasks.md "连接到 Google 任务，以便您可以管理你的任务"
[google-calendardoc]: ./connectors-create-api-googlecalendar.md "连接到 Google 日历并可以管理日历。"
[http-responsedoc]: ./connectors-native-reqres.md "添加用于 HTTP 请求和响应你的 logic apps 操作"
[instagramdoc]: ./connectors-create-api-instagram.md "连接到 Instagram。触发事件或针对事件进行操作"
[mailchimpdoc]: ./connectors-create-api-mailchimp.md "连接到你的 MailChimp 帐户。管理邮件和自动执行邮件操作"
[mandrilldoc]: ./connectors-create-api-mandrill.md "连接到 Mandrill 的通信"
[microsoft-translatordoc]: ./connectors-create-api-microsofttranslator.md "连接到 Microsoft Translator。翻译文字、检测语言，等等" 
[office365-usersdoc]: ./connectors-create-api-office365-users.md 
[office365-videodoc]: ./connectors-create-api-office365-video.md "获取 Office 365 视频的视频信息、 视频列表和通道和播放 Url"
[onedrivedoc]: ./connectors-create-api-onedrive.md "连接到个人 Microsoft OneDrive。上传、删除、列出文件，等等"
[onedrive-for-businessdoc]: ./connectors-create-api-onedriveforbusiness.md "连接到你的业务 Microsoft OneDrive。上传、删除、列出文件，等等"
[outlook.comdoc]: ./connectors-create-api-outlook.md "连接到 Outlook 邮箱。管理电子邮件、日历、联系人等"
[project-onlinedoc]: ./connectors-create-api-projectonline.md "连接到 Microsoft Project Online。管理项目、任务、资源等"
[querydoc]: ./connectors-native-query.md "选择和筛选的查询操作的阵列"
[rssdoc]: ./connectors-create-api-rss.md "发布和检索源的项，触发器操作的新项时发布到 RSS 源。"
[sendgriddoc]: ./connectors-create-api-sendgrid.md "连接到 SendGrid。发送电子邮件和管理收件人列表"
[sftpdoc]: ./connectors-create-api-sftp.md "连接到 SFTP 帐户。上传、获取、删除文件，等等"
[slackdoc]: ./connectors-create-api-slack.md "连接到 Slack，并将消息发布到 Slack 通道"
[smtpdoc]: ./connectors-create-api-smtp.md "连接到 SMTP 服务器和发送带附件的电子邮件"
[sparkpostdoc]: ./connectors-create-api-sparkpost.md "连接到 SparkPost 进行通信"
[trellodoc]: ./connectors-create-api-trello.md "连接到 Trello。管理项目，与任何人一起组织任何事情"
[twiliodoc]: ./connectors-create-api-twilio.md "连接到 Twilio。发送和获取消息、获取可用号码，管理来电号码，等等"
[wunderlistdoc]: ./connectors-create-api-wunderlist.md "连接到 Wunderlist。管理任务和待办事项列表，让生活有条不紊，等等"
[yammerdoc]: ./connectors-create-api-yammer.md "连接到 Yammer。发布消息、获取新消息，等等"
[youtubedoc]: ./connectors-create-api-youtube.md "连接到 YouTube。管理视频和频道"


<!--Icon references-->
[appFiguresicon]: ./media/apis-list/appfigures.png
[Asanaicon]: ./media/apis-list/asana.png
[Azure-Automation-icon]: ./media/apis-list/azure-automation.png
[AzureBlobStorageicon]: ./media/apis-list/azureblob.png
[Azure-Data-Lake-icon]: ./media/apis-list/azure-data-lake.png
[Azure-DocumentDBicon]: ./media/apis-list/azure-documentdb.png
[Azure-MLicon]: ./media/apis-list/azureml.png
[Azure-Resource-Manager-icon]: ./media/apis-list/azure-resource-manager.png
[Azure-Queues-icon]: ./media/apis-list/azure-queues.png
[Basecamp-3icon]: ./media/apis-list/basecamp.png
[Bitbucket-icon]: ./media/apis-list/bitbucket.png
[Bitlyicon]: ./media/apis-list/bitly.png
[BizTalk-Servericon]: ./media/apis-list/biztalk.png
[Bloggericon]: ./media/apis-list/blogger.png
[Boxicon]: ./media/apis-list/box.png
[Campfireicon]: ./media/apis-list/campfire.png
[Cognitive-Services-Text-Analyticsicon]: ./media/apis-list/cognitiveservicestextanalytics.png
[DB2icon]: ./media/apis-list/db2.png
[Dropboxicon]: ./media/apis-list/dropbox.png
[Dynamics-365icon]: ./media/apis-list/dynamicscrmonline.png
[Dynamics-365-for-Financialsicon]: ./media/apis-list/madeira.png
[Dynamics-365-for-Operationsicon]: ./media/apis-list/dynamicsax.png
[Easy-Redmineicon]: ./media/apis-list/easyredmine.png
[Event-Hubs-icon]: ./media/apis-list/eventhubs.png
[Facebookicon]: ./media/apis-list/facebook.png
[FTPicon]: ./media/apis-list/ftp.png
[GitHubicon]: ./media/apis-list/github.png
[Google-Calendaricon]: ./media/apis-list/googlecalendar.png
[Google-Driveicon]: ./media/apis-list/googledrive.png
[Google-Sheetsicon]: ./media/apis-list/googlesheet.png
[Google-Tasksicon]: ./media/apis-list/googletasks.png
[HideKeyicon]: ./media/apis-list/hidekey.png
[HipChaticon]: ./media/apis-list/hipchat.png
[Informixicon]: ./media/apis-list/informix.png
[Insightlyicon]: ./media/apis-list/insightly.png
[Instagramicon]: ./media/apis-list/instagram.png
[Instapapericon]: ./media/apis-list/instapaper.png
[JIRAicon]: ./media/apis-list/jira.png
[MailChimpicon]: ./media/apis-list/mailchimp.png
[Mandrillicon]: ./media/apis-list/mandrill.png
[Microsoft-Translatoricon]: ./media/apis-list/microsofttranslator.png
[MQicon]: ./media/apis-list/mq.png
[Office-365-Outlookicon]: ./media/apis-list/office365.png
[Office-365-Usersicon]: ./media/apis-list/office365users.png
[Office-365-Videoicon]: ./media/apis-list/office365video.png
[OneDriveicon]: ./media/apis-list/onedrive.png
[OneDrive-for-Businessicon]: ./media/apis-list/onedriveforbusiness.png
[Oracle-DB-icon]: ./media/apis-list/oracle-db.png
[Outlook.comicon]: ./media/apis-list/outlook.png
[PagerDutyicon]: ./media/apis-list/pagerduty.png
[Pinteresticon]: ./media/apis-list/pinterest.png
[Project-Onlineicon]: ./media/apis-list/projectonline.png
[Redmineicon]: ./media/apis-list/redmine.png
[RSSicon]: ./media/apis-list/rss.png
[Common-Data-Serviceicon]: ./media/apis-list/runtimeservice.png
[Salesforceicon]: ./media/apis-list/salesforce.png
[SAPicon]: ./media/apis-list/sap.png
[SendGridicon]: ./media/apis-list/sendgrid.png
[Service-Busicon]: ./media/apis-list/servicebus.png
[SFTPicon]: ./media/apis-list/sftp.png
[SharePointicon]: ./media/apis-list/sharepointonline.png
[Slackicon]: ./media/apis-list/slack.png
[Smartsheeticon]: ./media/apis-list/smartsheet.png
[SMTPicon]: ./media/apis-list/smtp.png
[SparkPosticon]: ./media/apis-list/sparkpost.png
[SQL-Servericon]: ./media/apis-list/sql.png
[Todoisticon]: ./media/apis-list/todoist.png
[Trelloicon]: ./media/apis-list/trello.png
[Twilioicon]: ./media/apis-list/twilio.png
[Twittericon]: ./media/apis-list/twitter.png
[Vimeoicon]: ./media/apis-list/vimeo.png
[Visual-Studio-Team-Servicesicon]: ./media/apis-list/visualstudioteamservices.png
[WordPressicon]: ./media/apis-list/wordpress.png
[Wunderlisticon]: ./media/apis-list/wunderlist.png
[Yammericon]: ./media/apis-list/yammer.png
[YouTubeicon]: ./media/apis-list/youtube.png

<!-- Primitive Icons -->
[API/Web-Appicon]: ./media/apis-list/api.png
[Azure-Functionsicon]: ./media/apis-list/function.png
[Delayicon]: ./media/apis-list/delay.png
[FileSystemIcon]: ./media/apis-list/filesystem.png
[HTTPicon]: ./media/apis-list/http.png
[HTTP-Requesticon]: ./media/apis-list/request.png
[HTTP-Responseicon]: ./media/apis-list/response.png
[HTTP-Swaggericon]: ./media/apis-list/http_swagger.png
[Nested-Logic-Appicon]: ./media/apis-list/workflow.png
[Recurrenceicon]: ./media/apis-list/recurrence.png
[Queryicon]: ./media/apis-list/query.png
[Webhookicon]: ./media/apis-list/webhook.png

<!-- EIP Icons -->
[as2icon]: ./media/apis-list/as2.png
[x12icon]: ./media/apis-list/x12new.png
[flatfileicon]: ./media/apis-list/flatfileencoding.png
[flatfiledecodeicon]: ./media/apis-list/flatfiledecoding.png
[xmlvalidateicon]: ./media/apis-list/xmlvalidation.png
[xmltransformicon]: ./media/apis-list/xsltransform.png
[integrationaccounticon]: ./media/apis-list/integrationaccount.png
