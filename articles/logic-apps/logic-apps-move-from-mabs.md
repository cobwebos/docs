---
title: 将应用从 BizTalk 服务移动到 Azure 逻辑应用 | Microsoft Docs
description: 将 Azure BizTalk 服务 (MABS) 移动或迁移到 Azure 逻辑应用
services: logic-apps
documentationcenter: ''
author: jonfancey
manager: jeconnoc
editor: ''
ms.assetid: ''
ms.service: logic-apps
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/30/2017
ms.author: jonfan; LADocs
ms.openlocfilehash: 846386172c0221c217430e62c8560484f799fa7f
ms.sourcegitcommit: 6f6d073930203ec977f5c283358a19a2f39872af
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/11/2018
ms.locfileid: "35299539"
---
# <a name="move-from-biztalk-services-to-azure-logic-apps"></a>从 BizTalk 服务移动到 Azure 逻辑应用

Microsoft Azure BizTalk 服务 (MABS) 即将停用。 若要将 MABS 集成解决方案移动到 [Azure 逻辑应用](../logic-apps/logic-apps-overview.md)，请使用本文中的指南。 

## <a name="introduction"></a>介绍

BizTalk 服务由两个子服务组成：

* Microsoft BizTalk 服务混合连接
* 基于 EAI 和 EDI 网桥的集成

[Azure 应用服务混合连接](../app-service/app-service-hybrid-connections.md)替代了 BizTalk 服务混合连接。 Azure 混合连接可以通过 Azure 门户利用 Azure 应用服务获得。 此服务提供了一个混合连接管理器，以用于管理现有 BizTalk 服务混合连接，以及你在门户中创建的新混合连接。 

[逻辑应用](../logic-apps/logic-apps-overview.md)替代了 EAI 和 EDI 基于桥的集成，提供与 BizTalk 服务相同的所有功能以及其他功能。 此服务提供基于云规模消耗的工作流和业务流程功能，使得你可以使用浏览器或 Visual Studio 快速轻松地生成复杂的集成解决方案。

此表将 BizTalk 服务功能映射到逻辑应用。

| BizTalk 服务   | 逻辑应用            | 目的                      |
| ------------------ | --------------------- | ---------------------------- |
| 连接器          | 连接器             | 发送和接收数据   |
| 网桥             | 逻辑应用             | 管道处理器           |
| 验证阶段     | XML 验证操作 | 针对架构验证 XML 文档 | 
| 扩充阶段       | 数据令牌           | 将属性升级为消息或升级属性供路由决策 |
| 转换阶段    | 转换操作      | 将 XML 消息从一种格式转换为另一种格式 |
| 解码阶段       | 平面文件解码操作 | 将平面文件转换为 XML |
| 编码阶段       | 平面文件编码操作 | 将 XML 转换为平面文件 |
| 消息检查器  | Azure Functions 或 API 应用 | 在集成中运行自定义代码 |
| 路由操作       | 条件或切换 | 将消息路由到指定的连接器之一 |
|||| 

## <a name="biztalk-services-artifacts"></a>BizTalk 服务项目

BizTalk 服务有几种类型的项目。 

## <a name="connectors"></a>连接器

BizTalk 服务连接器帮助网桥发送和接收数据，包括启用了基于 HTTP 的请求/响应交互的双向网桥。 逻辑应用使用相同的术语，并且有通过连接到各种技术和服务来提供相同用途的 180 + 连接器。 例如，连接器可用于云 SaaS 和 PaaS 服务（例如 OneDrive、Office365、Dynamics CRM 及其他服务）并可通过本地数据网关用于本地系统，本地数据网关替代了 BizTalk 服务的 BizTalk 适配器服务。 BizTalk 服务中的源仅限于 FTP、SFTP 和服务总线队列或主题订阅。

![](media/logic-apps-move-from-mabs/sources.png)

默认情况下，每个网桥都有一个 HTTP 终结点，这是通过网桥的“运行时地址”和“相对地址”属性配置的。 若要通过逻辑应用实现相同结果，请使用[请求和响应](../connectors/connectors-native-reqres.md)操作。

## <a name="xml-processing-and-bridges"></a>XML 处理和网桥

在 BizTalk 服务中，网桥类似于处理管道。 网桥可以获得从连接器接收的数据，对数据执行某些操作，然后将结果发送到另一个系统。 逻辑应用通过支持与 BizTalk 服务相同的基于管道的集成并提供其他集成模式来执行相同的操作。 在 BizTalk 服务中，[XML 请求-答复网桥](https://msdn.microsoft.com/library/azure/hh689781.aspx)称为 VETER 管道，它包括执行以下任务的各个阶段：

* (V) 验证
* (E) 扩充
* (T) 转换
* (E) 扩充
* (R) 路由

下图显示了处理在请求和答复之间的拆分情况，这允许分别对请求和答复路径进行控制，例如，通过为每个路径使用不同的映射：

![](media/logic-apps-move-from-mabs/xml-request-reply.png)

另外，XML 单向网桥还在处理开头和末尾添加了解码和编码阶段。 直通网桥包含单个扩充阶段。

### <a name="message-processing-decoding-and-encoding"></a>消息处理、解码和编码

在 BizTalk 服务中，可以接收不同类型的 XML 消息，并确定所接收消息的匹配架构。 此工作是在接收处理管道的“消息类型”阶段执行的。 然后，解码阶段通过提供的架构，使用检测到的消息类型对消息进行解码。 如果架构为平面文件架构，则此阶段会将传入的平面文件转换为 XML。 

逻辑应用提供类似的功能。 你使用不同的连接器触发器通过不同的协议（文件系统、FTP、HTTP，等等）接收平面文件，并使用[平面文件解码](../logic-apps/logic-apps-enterprise-integration-flatfile.md)操作将传入的数据转换为 XML。 无需进行任何更改即可将现有的平面文件架构直接移动到逻辑应用，然后再将架构上传到集成帐户。

### <a name="validation"></a>验证

传入数据转换为 XML后（或者如果接收到的消息格式为 XML），将运行验证以确定消息是否符合 XSD 架构。 若要在逻辑应用中执行此任务，请使用 [XML 验证](../logic-apps/logic-apps-enterprise-integration-xml-validation.md)操作。 无需进行任何更改即可使用 BizTalk 服务中的相同架构。

### <a name="transform-messages"></a>转换消息

在 BizTalk 服务中，转换阶段将一种基于 XML 的消息格式转换为另一种格式。 此工作是通过使用基于 TRFM 的映射程序来应用映射完成的。 在逻辑应用中，过程与此类似。 转换操作会从集成帐户执行映射。 主要区别是，逻辑应用中的映射是 XSLT 格式。 XSLT 能够重复使用现有 XSLT，包括为 BizTalk 服务器创建的包含 functoid 的映射。 

### <a name="routing-rules"></a>路由规则

BizTalk 服务会做出路由决策，决定由哪个终结点或连接器来发送传入的消息或数据。 可以使用路由筛选器选项从预配置的终结点中进行选择：

![](media/logic-apps-move-from-mabs/route-filter.png)

在 BizTalk 服务中，如果只有两个选项，则使用“条件”是用于转换 BizTalk 服务中的路由筛选器的最佳方式。 如果不止两个选项，则使用“切换”。

逻辑应用通过[条件语句](../logic-apps/logic-apps-control-flow-conditional-statement.md)和 [switch 语句](../logic-apps/logic-apps-control-flow-switch-statement.md)提供更复杂的逻辑功能以及高级控制流和路由。

### <a name="enrich"></a>扩充

在 BizTalk 服务处理中，扩充阶段会向与接收的数据关联的消息上下文添加属性。 例如，通过查找数据库提升要用于路由的属性，或者通过使用 XPath 表达式来提取值。 逻辑应用将提供前面操作的所有上下文数据输出的访问权限，使复制相同的行为变得十分简单。 例如，使用 `Get Row` SQL 连接操作，可从 SQL Server 数据库返回数据，并在决策操作中使用数据进行路由。 同样，触发器触发的传入服务总线队列消息的属性是可寻址的，使用 xpath 工作流定义语言表达式的 XPath 也是如此。

### <a name="run-custom-code"></a>运行自定义代码

BizTalk 服务允许你运行在你自己的程序集中上传的[自定义代码](https://msdn.microsoft.com/library/azure/dn232389.aspx)。 此功能是通过 [IMessageInspector](https://msdn.microsoft.com/library/microsoft.biztalk.services.imessageinspector) 接口实现的。 网桥中的每个阶段都包括两个属性（On Enter Inspector 和 On Exit Inspector），它们提供你创建的实现了此接口的 .NET 类型。 通过自定义代码，可以对数据执行更复杂的处理，还可以重复使用程序集中执行常见业务逻辑的现有代码。 

逻辑应用提供两种主要方式来执行自定义代码：Azure Functions 和 API 应用。 可以创建 Azure Functions，也可以从逻辑应用中调用。 请参阅[通过 Azure Functions 为逻辑应用添加和运行自定义代码](../logic-apps/logic-apps-azure-functions.md)。 使用 API 应用（Azure 应用服务的一部分）创建自己的触发器和操作。 了解有关[创建用于逻辑应用的自定义 API](../logic-apps/logic-apps-create-api-app.md) 的详细信息。 

如果程序集中有从 BizTalk 服务中调用的自定义代码，可以将此代码移到 Azure Functions，也可以使用 API 应用创建自定义 API；具体取决于要实现什么。 例如，如果代码包装了逻辑应用没有其连接器的另一个服务，则请创建 API 应用，并在逻辑应用中使用你的 API 应用提供的操作。 如果有帮助程序函数或库，则 Azure Functions 很有可能是最合适的。

### <a name="edi-processing-and-trading-partner-management"></a>EDI 处理和参与方管理

BizTalk 服务和逻辑应用包括 EDI 和 B2B 处理，并支持 AS2（适用性语句 2）、X12 和 EDIFACT。 在 BizTalk 服务中，需要在专用的跟踪和管理门户中创建 EDI 网桥以及创建或管理参与方和协议。
在逻辑应用中，此功能是通过 [Enterprise Integration Pack (EIP)](../logic-apps/logic-apps-enterprise-integration-overview.md) 实现的。 EIP 提供了用于 EDI 和 B2B 处理的[集成帐户](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md)和 B2B 操作。 还可以使用集成帐户来创建和管理[参与方](../logic-apps/logic-apps-enterprise-integration-partners.md)和[协议](../logic-apps/logic-apps-enterprise-integration-agreements.md)。 创建集成帐户后，可以将一个或多个逻辑应用链接到该帐户。 然后，可以使用 B2B 操作从逻辑应用访问参与方信息。 提供了以下操作：

* AS2 编码
* AS2 解码
* X12 编码
* X12 解码
* EDIFACT 编码
* EDIFACT 解码

与 BizTalk 服务不同，这些操作与传输协议无关。 因此，创建逻辑应用时，可以更加灵活地选择要使用哪个连接器来发送和接收数据。 例如，可以将电子邮件附件接收为 X12 文件，然后在逻辑应用中处理这些文件。 

## <a name="manage-and-monitor"></a>管理和监视

在 BizTalk 服务中，专用门户提供了跟踪功能来监视和解决问题。 逻辑应用通过 [Azure 门户](../logic-apps/logic-apps-monitor-your-logic-apps.md)提供更丰富的跟踪和监视功能，包括用于在行动中对事物保持关注的一款移动应用。

## <a name="high-availability"></a>高可用性

若要在 BizTalk 服务中实现高可用性 (HA)，可以通过在特定区域中使用多个实例来共享处理负载。 逻辑应用提供区域中 HA 且不另外收费。 

在 BizTalk 服务中，B2B 处理的区域外灾难恢复需要执行备份和还原过程。 为实现业务连续性，逻辑应用提供了跨区域主动/被动 [DR 功能](../logic-apps/logic-apps-enterprise-integration-b2b-business-continuity.md)，这允许跨不同区域中的集成帐户来同步 B2B 数据。

## <a name="next-steps"></a>后续步骤

* [什么是逻辑应用？](../logic-apps/logic-apps-overview.md)
* [创建你的第一个逻辑应用](../logic-apps/quickstart-create-first-logic-app-workflow.md)，或者通过[预置模板](../logic-apps/logic-apps-create-logic-apps-from-templates.md)快速入门  
* [查看可以在逻辑应用中使用的所有可用连接器](../connectors/apis-list.md)