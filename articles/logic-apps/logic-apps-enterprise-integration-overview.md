---
title: B2B 企业集成
description: 了解如何使用 Azure 逻辑应用和 Enterprise Integration Pack 为企业集成构建自动化 B2B 工作流
services: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: jonfan, logicappspm
ms.topic: overview
ms.date: 08/01/2019
ms.openlocfilehash: 141f1a7fc8f966132cb3570fd9d9cfa911127eed
ms.sourcegitcommit: 76b48a22257a2244024f05eb9fe8aa6182daf7e2
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/03/2019
ms.locfileid: "74792450"
---
# <a name="b2b-enterprise-integration-solutions-with-azure-logic-apps-and-enterprise-integration-pack"></a>使用 Azure 逻辑应用和 Enterprise Integration Pack 的 B2B 企业集成解决方案

若要构建企业到企业 (B2B) 解决方案并在组织之间无缝通信，可以结合 [Azure 逻辑应用](../logic-apps/logic-apps-overview.md)使用 Enterprise Integration Pack (EIP) 来构建自动化的可缩放企业集成工作流。 尽管组织使用不同的协议和格式，但它们仍可以通过电子方式交换消息。 EIP 会将不同的格式转换为组织系统可以处理的格式，并支持 [AS2](../logic-apps/logic-apps-enterprise-integration-as2.md)、[X12](logic-apps-enterprise-integration-x12.md) 和 [EDIFACT](../logic-apps/logic-apps-enterprise-integration-edifact.md) 等行业标准协议。 还可以使用加密和数字签名保护消息。 EIP 支持以下[企业集成连接器](../connectors/apis-list.md#integration-account-connectors)和行业标准：

* 电子数据交换 (EDI)
* 企业应用程序集成 (EAI)

如果你熟悉 Microsoft BizTalk Server 或 Azure BizTalk 服务，则也会熟悉 EIP 的概念，因此可以轻松地使用其功能。 不过，一个重要差别在于，EIP 在体系结构上基于“集成帐户”，可以简化 B2B 通信中使用的项目的存储和管理。 这些帐户是基于云的容器，用于存储所有项目，例如合作伙伴、协议、架构、映射和证书。 

## <a name="why-use-the-enterprise-integration-pack"></a>为何使用 Enterprise Integration Pack？

* 使用 EIP 可将所有项目存储在一个位置，即集成帐户。

* 可以使用 Azure 逻辑应用和连接器来构建 B2B 工作流并将其与第三方软件即服务 (SaaS) 应用、本地应用以及自定义应用集成。

* 可以使用 Azure 函数为逻辑应用创建自定义代码。

## <a name="how-do-i-get-started"></a>如何入门？

在使用 EIP 开始构建 B2B 逻辑应用工作流之前，需要准备好以下各项：

* Azure 订阅。 如果没有 Azure 订阅，请[注册一个免费 Azure 帐户](https://azure.microsoft.com/free/)。

* 包含要使用的项目的[集成帐户](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md)

* 若要创建映射和架构，可以使用[适用于 Visual Studio 2015 的 Microsoft Azure 逻辑应用企业集成工具 2.0](https://aka.ms/vsmapsandschemas) 和 Visual Studio 2015。

创建集成帐户并添加项目后，可以通过在 Azure 门户中创建一个逻辑应用，开始使用这些项目构建 B2B 工作流。 如果你不熟悉逻辑应用，请尝试[创建一个简单的逻辑应用](../logic-apps/quickstart-create-first-logic-app-workflow.md)。 若要使用这些项目，请确保先将集成帐户链接到逻辑应用。 之后，逻辑应用便可以访问集成帐户。 还可以使用 Visual Studio 或 [PowerShell](https://docs.microsoft.com/powershell/module/az.logicapp) 来创建、管理和部署逻辑应用。

下面是开始构建 B2B 逻辑应用的概要步骤：

![创建 B2B 逻辑应用的先决条件](./media/logic-apps-enterprise-integration-overview/overview.png)  

## <a name="try-now"></a>立即试用

[部署一个用于发送和接收 AS2 消息的完全正常运行的示例逻辑应用](https://github.com/Azure/azure-quickstart-templates/tree/master/201-logic-app-as2-send-receive)

## <a name="next-steps"></a>后续步骤

* [创建贸易合作伙伴](logic-apps-enterprise-integration-partners.md)
* [创建协议](../logic-apps/logic-apps-enterprise-integration-agreements.md)
* [添加架构](logic-apps-enterprise-integration-schemas.md)
* [添加映射](../logic-apps/logic-apps-enterprise-integration-maps.md)
* [从 BizTalk 服务迁移](../logic-apps/logic-apps-move-from-mabs.md)
