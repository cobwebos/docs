---
title: "将应用从 BizTalk 服务移动到 Azure 逻辑应用 | Microsoft Docs"
description: "将 Azure BizTalk 服务 MABS 移动或迁移到逻辑应用"
services: logic-apps
documentationcenter: 
author: jonfancey
manager: anneta
editor: 
ms.assetid: 
ms.service: logic-apps
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/30/2017
ms.author: ladocs; jonfan; mandia
ms.openlocfilehash: 01c5376ac5ba9125eede9deb5ee0a7a006a91bb4
ms.sourcegitcommit: be9a42d7b321304d9a33786ed8e2b9b972a5977e
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/19/2018
---
# <a name="move-from-biztalk-services-to-logic-apps"></a>从 BizTalk 服务移动到逻辑应用

Microsoft Azure BizTalk 服务 (MABS) 即将停用。 使用本主题将 MABS 集成解决方案移动到 Azure 逻辑应用。 

## <a name="overview"></a>概述

BizTalk 服务由两个子服务组成：

1.  Microsoft BizTalk 服务混合连接
2.  基于 EAI 和 EDI 网桥的集成

如果打算移动混合连接，那么请参阅 [Azure 应用服务混合连接](../app-service/app-service-hybrid-connections.md)，了解此服务的更改和功能。 Azure 混合连接将替换 BizTalk 服务混合连接。 Azure 混合连接可从 Azure 应用服务获得，也可从 Azure 门户中获得。 Azure 混合连接还将提供新的混合连接管理器，管理现有 BizTalk 服务混合连接，以及门户中创建的新混合连接。 Azure 应用服务混合连接已正式发布 (GA)。

为了实现基于 EAI 和 EDI 网桥的集成，将替换为逻辑应用。 逻辑应用提供与 BizTalk 服务相同的所有功能以及更多内容。 逻辑应用提供基于云规模消耗的工作流和业务流程功能，用户可以使用浏览器或 Visual Studio 中的工具，快速轻松地生成复杂的集成解决方案。

下表提供了 BizTalk 服务功能到逻辑应用的映射。

| BizTalk 服务   | 逻辑应用            | 目的                  |
| ------------------ | --------------------- | ---------------------------- |
| 连接器          | 连接器             | 发送和接收数据   |
| 网桥             | 逻辑应用             | 管道处理器           |
| 验证阶段     | XML 验证操作      | 针对架构验证 XML 文档             |
| 扩充阶段       | 数据令牌      | 将属性升级为消息或升级属性供路由决策             |
| 转换阶段    | 转换操作      | 将 XML 消息从一种格式转换为另一种格式             |
| 解码阶段       | 平面文件解码操作      | 将平面文件转换为 XML             |
| 编码阶段       |  平面文件编码操作      | 将 XML 转换为平面文件             |
| 消息检查器       |  Azure Functions 或 API 应用      | 在集成中运行自定义代码             |
| 路由操作      |  条件或切换      | 将消息路由到指定的连接器之一             |

BizTalk 服务中有多种不同类型的项目。

## <a name="connectors"></a>连接器
BizTalk 服务中的连接器允许网桥发送和接收数据，包括启用了基于 HTTP 的请求/响应交互的双向网桥。 在逻辑应用中，使用了同样的术语。 逻辑应用中的连接器可以达到同一目的，并且还包括超过 140 种可以连接到广泛的技术和服务的连接器，这两者均在本地使用本地数据网关（替换 BizTalk 服务使用的 BizTalk 适配器服务）以及云 SaaS 和 PaaS 服务（如 OneDrive、Office365、Dynamics CRM 等等）。

BizTalk 服务中的源仅限于 FTP、SFTP 和服务总线队列或主题订阅。

![](media/logic-apps-move-from-mabs/sources.png)

默认情况下，每个网桥都有一个 HTTP 终结点，这是通过网桥的运行时地址和相对地址属性进行配置的。 若要在逻辑应用中实现相同功能，请使用[请求和响应](../connectors/connectors-native-reqres.md)操作。

## <a name="xml-processing-and-bridges"></a>XML 处理和网桥
BizTalk 服务中的网桥类似于处理管道。 网桥可以获得从连接器接收的数据，并对数据执行某些操作，然后将其发送到另一个系统。 逻辑应用与 BizTalk 服务相同，支持基于相同管道的交互模式，还提供大量其他集成模式。 在 BizTalk 服务中，[XML 请求-答复网桥](https://msdn.microsoft.com/library/azure/hh689781.aspx)被称为 VETER 管道，在各个阶段可以执行以下操作：

* (V) 验证
* (E) 扩充
* (T) 转换
* (E) 扩充
* (R) 路由

如以下映像所示，处理在请求和答复之间进行了拆分，并允许单独对请求和答复路径进行控制（例如，各自使用不同的映射）：

![](media/logic-apps-move-from-mabs/xml-request-reply.png)

此外，XML 单向网桥会在处理开始和结尾时添加解码和编码阶段，同时，传递网桥包含一个扩充阶段。

### <a name="message-processing-and-decodingencoding"></a>消息处理和解码/编码
在 BizTalk 服务中，将接收不同类型的 XML 消息，并为接收的消息确定匹配的架构。 这是在接收处理管道的消息类型阶段执行的。 然后，解码阶段通过提供的架构，使用检测到的消息类型对其进行解码。 如果架构为平面文件架构，则会将传入的平面文件转换为 XML。 

逻辑应用提供类似的功能。 用户使用不同的连接器触发器（文件系统、FTP、HTTP 等）通过多种不同的协议接收平面文件，并使用[平面文件解码](../logic-apps/logic-apps-enterprise-integration-flatfile.md)操作将传入数据转换为 XML。 无需任何更改即可将现有的平面文件架构直接移动到逻辑应用，然后再将架构上传到集成帐户。

### <a name="validation"></a>验证
传入数据转换为 XML后（或者如果接收到的消息格式为 XML），将运行验证以确定消息是否符合 XSD 架构。 若要在逻辑应用中执行此操作，请使用 [XML 验证](../logic-apps/logic-apps-enterprise-integration-xml-validation.md)操作。 同样，无需任何更改即可使用 BizTalk 服务中的相同架构。

### <a name="transform-messages"></a>转换消息
在 BizTalk 服务中，转换阶段将一种基于 XML 的消息格式转换为另一种格式。 此操作通过使用基于 TRFM 的映射程序应用映射完成。 在逻辑应用中，过程与此类似。 转换操作会从集成帐户执行映射。 主要区别是，逻辑应用中的映射是 XSLT 格式。 XSLT 能够重复使用现有 XSLT，包括为 BizTalk 服务器创建的包含 functoid 的映射。 

### <a name="routing-rules"></a>路由规则
BizTalk 服务会做出路由决策，决定哪个终结点/连接器发送传入的邮件/数据。 使用路由筛选器选项能够实现从大量预配置终结点中选出一个终结点：

![](media/logic-apps-move-from-mabs/route-filter.png)

逻辑应用通过[条件](../logic-apps/logic-apps-use-logic-app-features.md)和[切换](../logic-apps/logic-apps-switch-case.md)提供更复杂的逻辑功能，能够实现高级的控制流和路由。 如果只有两个选项，则在 BizTalk 服务中转换路由筛选器的最佳方式是使用“条件”。 如果不止两个选项，则使用“切换”。

### <a name="enrich"></a>扩充
BizTalk 服务处理中的扩充阶段会向与接收的数据关联的消息上下文添加属性。 例如，提升从数据库查找路由（下文将讨论）所用的属性，或者使用 XPath 表达式提取值。 逻辑应用将提供前面操作的所有上下文数据输出的访问权限，使复制相同的行为变得十分简单。 例如，使用 `Get Row` SQL 连接操作，可从 SQL Server 数据库返回数据，并在决策操作中使用数据进行路由。 同样，触发器触发的传入服务总线队列消息的属性是可寻址的，使用 xpath 工作流定义语言表达式的 XPath 也是如此。

### <a name="use-custom-code"></a>使用自定义代码
BizTalk 服务能够[运行上传到程序集中的自定义代码](https://msdn.microsoft.com/library/azure/dn232389.aspx)。 这是通过 [IMessageInspector](https://msdn.microsoft.com/library/microsoft.biztalk.services.imessageinspector.aspx) 接口实现的。 网桥中的每个阶段都包括两个属性（On Enter Inspector 和 On Exit Inspector），它们提供创建的 .Net 类型以实现此接口。 通过自定义代码可以对数据执行更复杂的处理，还可以重复使用程序集中执行常见业务逻辑的现有代码。 

逻辑应用提供两种主要方式来执行自定义代码：Azure Functions 和 API 应用。 可以创建 Azure Functions，也可以从逻辑应用中调用。 请参阅[通过 Azure Functions 为逻辑应用添加和运行自定义代码](../logic-apps/logic-apps-azure-functions.md)。 使用 API 应用（Azure 应用服务的一部分）创建自己的触发器和操作。 了解有关[创建用于逻辑应用的自定义 API](../logic-apps/logic-apps-create-api-app.md) 的详细信息。 

如果程序集中有从 BizTalk 服务中调用的自定义代码，可以将此代码移到 Azure Functions，也可以使用 API 应用创建自定义 API；具体取决于要实现什么。 例如，如果代码包装了逻辑应用没有连接器的另一个服务，则请创建 API 应用，并使用 API 应用在逻辑应用中提供的操作。 如果有帮助程序函数或库，则 Azure Functions 很有可能是最合适的。

### <a name="edi-processing-and-trading-partner-management"></a>EDI 处理和参与方管理
BizTalk 服务包括 EDI 和 B2B 处理，支持 AS2（适用性语句 2）、X12 和 EDIFACT。 逻辑应用也是如此。 在 BizTalk 服务中，需要在专用的跟踪和管理门户中创建 EDI 网桥以及创建/管理参与方和协议。

在逻辑应用中，此功能是 [Enterprise Integration Pack](../logic-apps/logic-apps-enterprise-integration-overview.md) 随附的。 其中包括用于 EDI 和 B2B 处理的集成帐户和 B2B 操作。 [集成帐户](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md)用于创建和管理[参与方](../logic-apps/logic-apps-enterprise-integration-partners.md)和[协议](../logic-apps/logic-apps-enterprise-integration-agreements.md)。 创建集成帐户后，可以将一个或多个逻辑应用关联到该帐户。 关联后，可以使用 B2B 操作在逻辑应用内访问参与方的信息。 提供了以下操作：

* AS2 编码
* AS2 解码
* X12 编码
* X12 解码
* EDIFACT 编码
* EDIFACT 解码

与 BizTalk 服务不同，这些操作与传输协议无关。 因此，创建逻辑应用时，可以更加灵活地选择使用哪个连接器发送和接收数据。 例如，它可能以电子邮件附件形式接收 X12 文件，然后在逻辑应用中处理这些文件。 

## <a name="manage-and-monitor"></a>管理和监视
除了参与方管理，BizTalk 服务的专用门户还提供了跟踪功能以监视和解决问题。 

逻辑应用在 [Azure 门户](../logic-apps/logic-apps-monitor-your-logic-apps.md)中，以及通过 [Operations Management Suite B2B 解决方案](../logic-apps/logic-apps-monitor-b2b-message.md)提供更丰富的跟踪和监视功能；包括用于在行动中对事物保持关注的一款移动应用。

## <a name="high-availability"></a>高可用性
若要在 BizTalk 服务中实现高可用性 (HA)，需要在给定区域使用多个实例共享处理负载。 在逻辑应用中，区域内 HA 是内置的，不会另外收费。 对于 BizTalk 服务中用于 B2B 处理的区域外灾难恢复，则需要备份和还原过程。 在逻辑应用中，提供了跨区域主动/被动 [DR 功能](../logic-apps/logic-apps-enterprise-integration-b2b-business-continuity.md)；这样可以跨不同区域的集成帐户同步 B2B 数据，以实现业务连续性。

## <a name="next"></a>下一步
* [什么是逻辑应用](logic-apps-overview.md)
* [创建你的第一个逻辑应用](quickstart-create-first-logic-app-workflow.md)，或者通过[预置模板](logic-apps-create-logic-apps-from-templates.md)快速入门  
* [查看所有可用的连接器](../connectors/apis-list.md)（可在逻辑应用中使用）
