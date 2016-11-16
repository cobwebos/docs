---
title: "可用的连接器和 API 应用的列表 | Microsoft Docs"
description: "了解 Azure 应用服务中的连接器和 API 应用"
services: logic-apps
documentationcenter: 
author: MandiOhlinger
manager: erikre
editor: cgronlun
ms.assetid: 984a425d-ba64-48cc-90dc-bb624411e0f0
ms.service: logic-apps
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 09/01/2016
ms.author: mandia
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 6028fc53661978f63bbac89be6214ffebdc22405


---
# <a name="list-of-connectors-and-api-apps-to-use-in-your-logic-apps"></a>可在逻辑应用中使用的连接器和 API 应用的列表
> [!NOTE]
> 此文章版本适用于 2014-12-01-预览架构版的逻辑应用。 有关逻辑应用正式发布 (GA) 版本，请参阅 [新连接器列表](../connectors/apis-list.md)。
> 
> 

了解 Microsoft 创建的所有可用的连接器和 API 应用，以便在逻辑应用中使用。

有关定价信息和每个服务层包含的内容列表，请参阅 [Azure 应用服务定价](https://azure.microsoft.com/pricing/details/app-service/)。

> [!NOTE]
> 若要在注册 Azure 帐户之前就开始使用逻辑应用，请转到 [试用逻辑应用](https://tryappservice.azure.com/?appservice=logic)。 可以立即在应用服务中创建一个短期的初学者逻辑应用。 你不需要使用信用卡，也不需要做出承诺。
> 
> 

## <a name="core-connectors"></a>核心连接器
下表列出了 Microsoft 创建的可用作核心连接器的所有可用连接器和 API 应用：

| Name | 说明 |
| --- | --- |
| [必应在线翻译](https://azure.microsoft.com/marketplace/partners/bing/microsofttranslator/) |使用必应将文本翻译为另一种语言。 |
| [HTTP](app-service-logic-connector-http.md) |HTTP 侦听程序打开可用作 HTTP 服务器的终结点，并侦听传入的 HTTP 或 HTTPS 请求。 HTTP 操作不需要 API 应用，并在逻辑应用中以本机方式受支持。 |
| [Slack](app-service-logic-connector-slack.md) |连接到 Slack，并将消息发布到 Slack 通道。 |

## <a name="enterprise-integration-connectors"></a>企业集成连接器
下表列出了 Microsoft 创建的可用作企业集成连接器的所有可用连接器和 API 应用：

| Name | 说明 |
| --- | --- |
| [BizTalk 规则](app-service-logic-use-biztalk-rules.md) |使用“BizTalk 规则”可以定义和控制组织中的业务逻辑。 无需重新编译也无需重新部署关联的应用程序，即可更新业务策略。 |
| [BizTalk XPath 提取程序](app-service-logic-xpath-extract.md) |基于所选的 XPath，从 XML 内容中查找并提取数据。 |
| [DB2 连接器](app-service-logic-connector-db2.md) |连接到运行 Windows 操作系统的本地和 Azure 虚拟机上的 IBM DB2 数据库。 可以将 Web API 和 OData API 操作映射到 Informix 结构化查询语言命令。 <br/><br/>无触发器。 操作包括表 select、insert、update、delete 和自定义语句<br/><br/>此连接器还包括 Microsoft DRDA 客户端，用于通过 TCP/IP 网络连接到 Informix 服务器。 |
| [文件](app-service-logic-connector-file.md) |使用此连接器，可以连接到本地文件系统或网络，完成各种文件任务，包括上载、删除、列出文件等。 |
| [Informix](app-service-logic-connector-informix.md) |连接到运行 Windows 操作系统的本地和 Azure 虚拟机上的 IBM Informix 数据库。 可以将 Web API 和 OData API 操作映射到 Informix 结构化查询语言命令。<br/><br/>无触发器。 操作包括表 select、insert、update、delete 和自定义语句。<br/><br/>在本地使用时，可以使用 VPN 或 Azure ExpressRoute。 此连接器还包括 Microsoft DRDA 客户端，用于通过 TCP/IP 网络连接到 Informix 服务器。 |
| [Microsoft SQL Server](app-service-logic-connector-sql.md) |连接到本地 SQL Server 或 Azure SQL 数据库。 可以在 SQL 数据库表中创建、更新、获取和删除条目。 |
| MQ |连接到运行 Windows 操作系统的本地和 Azure 虚拟机上的 IBM WebSphere MQ Server 版本 8。 在本地使用时，可以使用 VPN 或 Azure ExpressRoute。 此连接器还包括 Microsoft MQ 客户端。<br/><br/>无触发器。 无操作。<br/><br/>**注意** 目前不能用于逻辑应用。 |

## <a name="connectors-as-triggers"></a>作为触发器的连接器
多种连接器为逻辑应用提供触发器。 这些触发器有两种类型：

1. 轮询触发器：这些触发器以指定的频率轮询服务，以检查是否有新数据。 有新数据可用时，将运行逻辑应用的新实例并以该数据作为输入。 为了防止同一数据被使用多次，触发器可能会清除已读取并已传递给逻辑应用的数据。 此类连接器的示例包括文件、SQL 和 Azure 存储。
2. 推送触发器：这些触发器侦听终结点上的数据或要发生的事件。 然后，会触发逻辑应用的一个新实例。 此类连接器的示例包括 HTTP 侦听器和 Twitter。

## <a name="connectors-as-actions"></a>作为操作的连接器
连接器还可以用作逻辑应用中的操作。 操作可用于在逻辑应用中查找随后可用于执行的数据。 例如，在处理订单时，可能需要从 SQL 数据库中查找有关客户的更多信息。 或者，可能需要在目标中写入、更新或删除数据。 可以使用连接器提供的操作来实现此目的。 这些操作将映射到 API 应用中的操作（如其 Swagger 元数据所定义）。

## <a name="create-your-own-connectors-and-api-apps"></a>创建自己的连接器和 API 应用
[Connectors and API Apps Reference](http://aka.ms/appservicesconnectorreference)（连接器和 API 应用参考）  
[Azure 应用服务 API 应用触发器](../app-service-api/app-service-api-dotnet-triggers.md)  
[逻辑应用参考](https://msdn.microsoft.com/library/azure/dn948510.aspx)

## <a name="more-on-connectors-and-api-apps"></a>有关连接器和 API 应用的更多信息
[什么是连接器和 BizTalk API 应用](app-service-logic-what-are-biztalk-api-apps.md)  
[在 Azure 应用服务中使用混合连接管理器](app-service-logic-hybrid-connection-manager.md)  
[管理和监视内置 API 应用和连接器](app-service-logic-monitor-your-connectors.md)




<!--HONumber=Nov16_HO2-->


