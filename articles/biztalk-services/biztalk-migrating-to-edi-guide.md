---
title: 将 BizTalk 服务器 EDI 解决方案迁移到 BizTalk 服务 | Microsoft Docs
description: 了解如何将 Microsoft BizTalk 服务器 EDI 解决方案迁移到 Microsoft Azure BizTalk 服务 (MABS)
services: biztalk-services
ms.service: biztalk-services
author: jonfancey
ms.author: jonfan
manager: jeconnoc
ms.topic: article
ms.date: 07/31/2018
ms.reviewer: jonfan, LADocs
ms.suite: integration
ms.openlocfilehash: 4ce65f1b5dd22da031ebf6730b5efad2d04f91a0
ms.sourcegitcommit: f86e5d5b6cb5157f7bde6f4308a332bfff73ca0f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/31/2018
ms.locfileid: "39365581"
---
# <a name="migrate-biztalk-server-edi-solutions-to-biztalk-services-technical-guide"></a>将 BizTalk 服务器 EDI 解决方案迁移到 BizTalk 服务：技术指南

> [!INCLUDE [BizTalk Services is being retired, and replaced with Azure Logic Apps](../../includes/biztalk-services-retirement.md)]

作者：Tim Wieman 和 Nitin Mehrotra

审阅者：Karthik Bharthy

编写使用：Microsoft Azure BizTalk 服务 – 2014 年 2 月版本。

## <a name="introduction"></a>介绍
电子数据交换 (EDI) 是企业以电子方式交换数据的最普遍的方式之一，也称为企业对企业或 B2B 交易。 自 BizTalk 服务器初版起，数十年来 BizTalk 服务器始终拥有 EDI 支持。 借助 BizTalk 服务，Microsoft 继续在 Microsoft Azure Platform 上支持 EDI 解决方案。 大部分 B2B 事务都在组织外部，因此更容易在云平台上实现。 Microsoft Azure 通过 BizTalk 服务提供此功能。

虽然部分客户将 BizTalk 服务视为新 EDI 解决方案的“新领域”平台，但许多客户拥有当前的 BizTalk 服务器 EDI 解决方案，他们可能希望将其迁移到 Azure。 由于 BizTalk 服务 EDI 是基于与 BizTalk Server EDI 架构（贸易合作伙伴、实体、协议）相同的关键实体构建的，因此可将 BizTalk Server EDI 项目迁移到 BizTalk 服务。

本文档讨论了将 BizTalk Server EDI 项目迁移到 BizTalk 服务所涉及的一些差异。 本文档假定了一种 BizTalk Server EDI 处理和贸易合作伙伴协议的应用知识。 有关 BizTalk Server EDI 的详细信息，请参阅[使用 BizTalk Server 进行贸易合作伙伴管理](https://msdn.microsoft.com/library/bb259970.aspx)。

## <a name="which-version-of-biztalk-server-edi-artifacts-can-be-migrated-to-biztalk-services"></a>哪个版本的 BizTalk Server EDI 项目可迁移到 BizTalk 服务？
BizTalk Server EDI 模块针对 BizTalk Server 2010 实现了显著增强，重新建模时纳入了合作伙伴、配置文件和协议。 BizTalk 服务利用同一模型整理贸易合作伙伴以及这些伙伴中的业务部门。 因此，可更为直接地将 EDI 项目从 BizTalk Server 2010 及更高版本迁移到 BizTalk 服务。 要迁移与 BizTalk Server 2010 之前的版本关联的 EDI 项目，必须先升级到 BizTalk Server 2010，然后将 EDI 项目迁移到 BizTalk 服务。

## <a name="scenariosmessage-flow"></a>方案/消息流
与 BizTalk Server 一样，BizTalk 服务中的 EDI 处理同样基于贸易合作伙伴管理 (TPM) 解决方案。 TPM 解决方案包含以下关键组件：

* 贸易合作伙伴，其表示 B2B 交易中的组织。
* 配置文件，其表示贸易合作伙伴内的部门。
* 贸易合作伙伴协议（或协议），其表示两个合作伙伴/配置文件之间的业务协议。

下图展示了 BizTalk Server EDI 解决方案与 BizTalk 服务 EDI 解决方案之间的异同点：

![][EDImessageflow]

BizTalk Server 和 BizTalk 服务中的 EDI 解决方案流之间的主要异同点在于：

* 正如 BizTalk Server 使用 EDIReceive 管道接收 EDI 消息、使用 EDISend 管道发送 EDI 消息一样，BizTalk 服务使用 EDI 接收网桥接收 EDI 消息、使用 EDI 发送网桥发送 EDI 消息。 在 BizTalk Server 中，管道通过使用发送或接收端口与协议进行关联。 在 BizTalk 服务中，协议本身表示发送或接收网桥。
* 在 BizTalk Server 中，EDIReceive 管道处理 EDI 消息后，消息将转储到 SQL Server 数据库。 然后，EdiSend 管道从 SQL Server 数据库中提取消息并对其进行处理，再将其发送给贸易合作伙伴。
  
    在 BizTalk 服务中，EDI 接收网桥处理 EDI 消息之后，会将消息路由到外部进程。 外部进程可在 Microsoft Azure 上运行或本地运行。 外部进程应将消息路由到 EDI 发送网桥；发送网桥不会自行拉取消息。 处理消息后，EDI 发送网桥将消息路由到贸易合作伙伴。

BizTalk 服务提供了一种易于使用的配置体验，可在不配置任何 Microsoft Azure 计算实例（Web 或辅助角色）、Microsoft Azure SQL 数据库或 Microsoft Azure 存储帐户的情况下在贸易合作伙伴之间快速创建和部署 B2B 协议。 更复杂的方案需要在工作流或贸易伙伴协议“边缘”的其他服务处理中进行尝试，即在贸易合作伙伴协议 EDI 网桥处理之前或之后。 详细来讲，在 BizTalk 服务的 EDI 消息处理期间会发生以下序列的事件。

1. 从贸易合作伙伴 Fabrikam 接收到 EDI 消息。  为了从贸易合作伙伴接收 EDI 消息，BizTalk 服务支持传输协议，如 FTP、SFTP、AS2 和 HTTP/S。
2. 贸易合作伙伴协议接收端处理将 EDI 消息拆分成 XML 格式。  可将已拆分的 EDI 消息（XML 格式）路由到服务总线终结点，例如服务总线中继终结点、服务总线主题、服务总线队列或 BizTalk 服务网桥。
3. 然后，可从终结点接收已拆分的 XML 消息，进行进一步的自定义处理。  这些终结点可由本地组件或 Microsoft Azure 计算实例处理，以便在 Windows Workflow (WF) 或 Windows Communication Foundation (WCF) 服务等中进一步处理消息。
4. 然后，贸易合作伙伴协议的“发送端处理”将 XML 消息组合成 EDI 格式，并将其发送给贸易合作伙伴 Contoso。  为了向贸易合作伙伴发送 EDI 消息，BizTalk 服务支持接收 EDI 消息时所用的相同协议。

本文档进一步提供了概念性指导，介绍了如何将一些不同的 BizTalk Server EDI 项目迁移到 BizTalk 服务。

## <a name="sendreceive-ports-to-trading-partners"></a>到贸易合作伙伴的发送/接收端口
在 BizTalk Server 中，设置接收位置和接收端口以便从贸易合作伙伴接收 EDI/XML 消息，设置发送端口以便向贸易合作伙伴发送 EDI/XML 消息。 然后，使用 BizTalk Server 管理控制台将这些端口绑定到贸易合作伙伴协议。 在 BizTalk 服务中，BizTalk 服务门户用于从贸易合作伙伴接收消息和向贸易合作伙伴发送消息的位置配置为贸易合作伙伴协议本身的一部分（作为“传输设置”的一部分）。  因此在 BizTalk 服务中，本身并不具有“发送端口”和“接收位置”的概念。 有关详细信息，请参阅[创建协议](https://msdn.microsoft.com/library/windowsazure/hh689908.aspx)。

## <a name="pipelines-bridges"></a>管道（网桥）
在 BizTalk Server EDI 中，管道是消息处理实体，它还可包括应用程序所需的用于特定处理功能的自定义逻辑。 对于 BizTalk 服务，其等效于 EDI 网桥。 然而在 BizTalk 服务中，EDI 网桥目前处于“关闭”状态。  也就是说，无法将自己的自定义活动添加到 EDI 网桥。 任何自定义处理都必须在应用程序中的 EDI 网桥外执行，在消息进入配置为交易合作伙伴协议的一部分的网桥之前或之后完成。 EAI 网桥可执行自定义处理。 如要进行自定义处理，可在 EDI 网桥处理消息之前或之后使用 EAI 网桥。 有关详细信息，请参阅[如何在桥中包含自定义代码](https://msdn.microsoft.com/library/azure/dn232389.aspx)。

可在贸易合作伙伴协议收到消息之前，或在协议处理消息并将其路由到服务总线终结点后，使用自定义代码和/或服务总线消息队列及主题插入发布/订阅流。

若要了解消息流模式，请参阅本文中的“方案/消息流”。

## <a name="agreements"></a>协议
如果熟悉用于 EDI 处理的 BizTalk Server 2010 交易合作伙伴协议，就会发现 BizTalk 服务贸易合作伙伴协议与之很相似。 大部分协议设置均相同且使用相同术语。 在某些情况下，与 BizTalk Server 中的相同设置相比，协议设置要简单得多。 Microsoft Azure BizTalk 服务支持 X12、EDIFACT 和 AS2 传输。

Microsoft Azure BizTalk 服务还提供 **TPM 数据迁移**工具，用于将贸易合作伙伴和协议从 BizTalk Server 贸易合作伙伴模块迁移到 BizTalk 服务门户。 TPM 数据迁移工具作为工具包的一部分提供，可以从 [MABS SDK](http://go.microsoft.com/fwlink/p/?LinkId=235057) 下载。 该包还包括一个自述文件，其中提供了工具使用方式的说明和工具的基本疑难解答信息。

## <a name="schemas"></a>架构
BizTalk 服务提供可用于 BizTalk 服务解决方案的 EDI 架构。  此外，BizTalk Server EDI 架构还可与 BizTalk 服务配合使用，因为 EDI 架构的根节点在 BizTalk Server 和 BizTalk 服务中相同。 因此，可直接获取 BizTalk Server EDI 架构，并在利用 BizTalk 服务开发的 EDI 解决方案中使用它们。 还可从 [MABS SDK](http://go.microsoft.com/fwlink/p/?LinkId=235057) 下载该架构。

## <a name="maps-transforms"></a>映射（转换）
BizTalk Server 中的映射在 BizTalk 服务中称为转换。 将映射从 BizTalk Server 迁移到 BizTalk 服务的任务可能更加复杂（取决于映射复杂性）。 用于 BizTalk 服务的映射工具不同于 BizTalk 映射器。 即使映射器看起来大部分相同，但底层映射格式不同。 用户可用的 functoid（在 BizTalk 服务中称为**映射操作**）也不同。  实际上，无法在 BizTalk 服务中直接使用 BizTalk 映射。 此外，BizTalk Server 中可用的所有 functoid 并非都可用作 BizTalk 服务中的映射操作。

### <a name="new-transform-operations"></a>新转换操作
可用转换映射操作的列表可能看起来与 BizTalk Server 映射器有很大差异，但 BizTalk 服务转换具有新方式来完成相同任务。 例如，BizTalk 服务转换可使用**列表操作**。 此操作在 BizTalk 映射器中不可用。  **列表操作**可在“列表”上进行创建和操作，其中列表是一组项目（也称为“行”），每个项目均可具有多个成员（也称为“列”）。  可对列表进行排序，根据条件选择项目等。

BizTalk 服务转换中的另一个新功能示例是**循环操作**。  很难在 BizTalk Server 映射器中创建嵌套循环。  因此，为 BizTalk 服务转换添加了循环映射操作。

再例如 **If-Then-Else** 表达式映射操作。  可在 BizTalk 映射器中执行 if-then-else 操作，但需要多个 functoid 才可完成看似简单的任务。

### <a name="migrating-biztalk-server-maps"></a>迁移 BizTalk Server 映射
Microsoft Azure BizTalk 服务提供了将 BizTalk Server 映射迁移到 BizTalk 服务转换的工具。 **BTMMigrationTool** 随附**工具**包提供，可通过 [BizTalk 服务 SDK 下载](http://go.microsoft.com/fwlink/p/?LinkId=235057)获取。 有关该工具的详细信息，请参阅[将 BizTalk 映射转换为 BizTalk 服务转换](https://msdn.microsoft.com/library/windowsazure/hh949812.aspx)。

还可查看 Sandro Pereira (BizTalk MVP) 提供的示例，了解如何 [migrate BizTalk Server maps to BizTalk Services transforms](http://social.technet.microsoft.com/wiki/contents/articles/23220.migrating-biztalk-server-maps-to-windows-azure-biztalk-services-wabs-maps.aspx)（将 BizTalk Server 映射迁移到 BizTalk 服务转换）。

## <a name="orchestrations"></a>业务流程
如需将 BizTalk Server 业务流程处理迁移到 Microsoft Azure，则需要重写该业务流程，因为 Microsoft Azure 不支持运行 BizTalk Server 业务流程。  可在 Windows Workflow Foundation 4.0 (WF4) 服务中重写业务流程功能。  这会是一次完全重写，因为当前没有从 BizTalk Server 业务流程到 WF4 的迁移。 以下是 Windows 工作流的部分资源：

* [如何将 WCF 工作流服务与服务总线队列和主题集成](https://blogs.msdn.microsoft.com/paolos/2013/04/09/how-to-integrate-a-wcf-workflow-service-with-service-bus-queues-and-topics/)（Paolo Salvatori 编写）。 
* [使用 Windows Workflow Foundation 和 Azure 构建应用会议](http://go.microsoft.com/fwlink/p/?LinkId=237314)（来自 Build 2011 大会）。
* [Windows Workflow Foundation 开发人员中心](http://go.microsoft.com/fwlink/p/?LinkId=237315)（参见 MSDN）。
* [Windows Workflow Foundation 4 (WF4) 文档](https://msdn.microsoft.com/library/dd489441.aspx)（参见 MSDN）。

## <a name="other-considerations"></a>其他注意事项
以下是使用 BizTalk 服务时必须注意的一些事项。

### <a name="fallback-agreements"></a>后备协议
BizTalk Server EDI 处理具有“后备协议”概念。  到目前为止，BizTalk 服务**尚无**后备协议概念。  若要了解如何在 BizTalk Server 中使用后备协议，请参阅 BizTalk 文档主题[协议在 EDI 处理中的角色](http://go.microsoft.com/fwlink/p/?LinkId=237317)和[配置全局或后备协议属性](https://msdn.microsoft.com/library/bb245981.aspx)。

### <a name="routing-to-multiple-destinations"></a>路由到多个目标
BizTalk 服务桥在当前状态下不支持使用发布订阅模型将邮件路由到多个目标。 相反，可将消息从 BizTalk 服务桥路由到服务总线主题，该主题中随后可包含多个订阅以接收多个终结点处的消息。

## <a name="see-also"></a>另请参阅
[Azure 中的 LOB 解决方案](https://azure.microsoft.com/solutions/lob-applications)

[EDImessageflow]: ./media/biztalk-migrating-to-edi-guide/IC719455.png
