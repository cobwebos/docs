---
title: 概述-适用于基于云的应用程序和解决方案的 Azure 无服务器
description: 了解如何使用 Azure 逻辑应用和 Azure Functions 来创建基于云的应用和解决方案，而无需担心基础结构
services: logic-apps
ms.suite: integration
author: jeffhollan
ms.author: jehollan
ms.reviewer: klam, estfan, logicappspm
ms.topic: article
ms.date: 03/30/2017
ms.openlocfilehash: 0f20bb5fb249ad6bac862afe2b0e8eee4b32e2a9
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/02/2020
ms.locfileid: "75666544"
---
# <a name="azure-serverless-overview-for-building-cloud-based-apps-and-solutions-with-azure-logic-apps-and-azure-functions"></a>Azure 无服务器：通过 Azure 逻辑应用和 Azure Functions 构建基于云的应用和解决方案概述

[无服务器](https://azure.microsoft.com/solutions/serverless/)应用具有各种优势，例如开发速度快、减少了代码、简单、规模。 本文介绍无服务器解决方案的各种属性以及 Azure 无服务器产品/服务。

## <a name="what-is-serverless"></a>什么是无服务器？

无服务器的意思并不是说没有服务器，而是说开发人员不需要考虑服务器。 在传统应用程序开发中，很大一部分工作是解决与缩放、托管以及监视解决方案相关的问题以满足应用程序的需求。 使用无服务器产品/服务时，解决方案中已考虑了这些问题。 此外，无服务器应用是按基于消耗的计划计费的。 如果永远不使用应用，则不会产生费用。 这些功能有助于开发人员集中精力处理解决方案的业务逻辑。

用于无服务器的核心 Azure 服务是[Azure 逻辑应用](https://azure.microsoft.com/services/logic-apps/)和[Azure Functions](https://azure.microsoft.com/services/functions/)。 两种解决方案都遵守上述原则，有助于开发人员通过最少的代码构建可靠的云应用。

## <a name="what-is-azure-logic-apps"></a>什么是 Azure 逻辑应用？

[Azure 逻辑应用](logic-apps-overview.md)提供了用于在云中简化并实现可缩放的集成和工作流的方式。 该服务提供了可视化设计器，用于为流程建模并将流程作为一系列步骤（称为工作流）自动执行。 在云服务和本地系统之间有许多[连接器](../connectors/apis-list.md)，可以快速地将无服务器应用连接到其他 API。 每个逻辑应用均以触发器（例如“将帐户添加到 Dynamics CRM 时”）开头。 在触发器触发后，工作流可以运行操作、转换和条件逻辑的组合。 在流程中安排不同的 Azure Functions 时，逻辑应用是一个很好的选择，尤其是当流程需要与外部系统或 API 进行交互时。

要开始使用逻辑应用，请先[创建第一个逻辑应用](quickstart-create-first-logic-app-workflow.md)。 如需有关逻辑应用的更多技术信息，请参阅[开发人员参考](logic-apps-workflow-definition-language.md)。

## <a name="what-is-azure-functions"></a>Azure Functions 是什么？

Azure Functions 是用于在云中轻松运行代码片段或“函数”的一项服务。 可以只编写解决当前问题所需的代码，不需担心整个应用或所需的基础结构。 Functions 可使开发更有效率，并可以使用自己所选的开发语言，例如 C#、F#、Node.js、Python 或 PHP。 只需为代码运行的时间付费，并且 Azure 会根据需要进行缩放。

若要开始使用 Azure Functions，请从[创建第一个 Azure 函数](../azure-functions/functions-create-first-azure-function.md)着手。 有关 Functions 的更多技术信息，请参阅[开发人员参考](../azure-functions/functions-reference.md)。

## <a name="how-can-i-build-and-deploy-serverless-apps-in-azure"></a>如何在 Azure 中构建和部署无服务器应用？

Azure 提供丰富的工具，用于开发、部署和管理无服务器应用。 可以直接在 Azure 门户中构建应用，只需使用 [Visual Studio](logic-apps-serverless-get-started-vs.md) 或 [Visual Studio Code](quickstart-create-logic-apps-visual-studio-code.md) 中的工具即可。 在构建应用之后，可以[使用 Azure 资源管理器模板快速部署该应用](logic-apps-deploy-azure-resource-manager-templates.md)。 Azure 还提供监视功能，该功能可以通过 Azure 门户、API 或 SDK 或适用于 Azure Monitor 日志和 Application Insights 的集成工具来访问。

## <a name="next-steps"></a>后续步骤

* [在 Visual Studio 中构建无服务器应用](logic-apps-serverless-get-started-vs.md)
* [使用无服务器产品/服务创建 Customer Insights 仪表板](logic-apps-scenario-social-serverless.md)
* [自动完成逻辑应用部署](logic-apps-azure-resource-manager-templates-overview.md)
