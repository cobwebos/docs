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
ms.sourcegitcommit: ff9688050000593146b509a5da18fbf64e24fbeb
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/06/2020
ms.locfileid: "75666544"
---
# <a name="azure-serverless-overview-for-building-cloud-based-apps-and-solutions-with-azure-logic-apps-and-azure-functions"></a>Azure 无服务器：通过 Azure 逻辑应用和 Azure Functions 构建基于云的应用和解决方案概述

[无服务器](https://azure.microsoft.com/solutions/serverless/)应用程序提供了更多优点，如提高开发速度、降低代码、简易性和规模。 本文介绍无服务器解决方案和 Azure 无服务器产品/服务的不同属性。

## <a name="what-is-serverless"></a>什么是无服务器？

无服务器并不意味着没有服务器，而是让开发人员不必担心服务器。 在传统应用程序开发中，很大一部分工作是解决与缩放、托管以及监视解决方案相关的问题以满足应用程序的需求。 在无服务器的情况下，这些问题是解决方案的一部分。 此外，无服务器应用按基于消耗的计划计费。 如果从未使用过该应用，则不会产生任何费用。 这些功能可帮助开发人员仅专注于解决方案的业务逻辑。

用于无服务器的核心 Azure 服务是[Azure 逻辑应用](https://azure.microsoft.com/services/logic-apps/)和[Azure Functions](https://azure.microsoft.com/services/functions/)。 这两个解决方案都遵循前面所述的原则，可帮助开发人员以最少的代码构建可靠的云应用。

## <a name="what-is-azure-logic-apps"></a>什么是 Azure 逻辑应用？

[Azure 逻辑应用](logic-apps-overview.md)提供了一种方法来简化和实现云中可缩放的集成和工作流。 此服务提供了一个可视化设计器，可将流程作为一系列步骤（称为工作流）进行建模并实现自动化。 云服务和本地系统之间有许多[连接器](../connectors/apis-list.md)，可快速将无服务器应用程序连接到其他 api。 每个逻辑应用都以触发器开头，例如 "将帐户添加到 Dynamics CRM 时"。 触发触发器后，工作流可以运行操作、转换和条件逻辑的组合。 当在一个进程中协调不同的 Azure Functions 时，尤其是当该进程需要与外部系统或 API 交互时，逻辑应用是一种很好的选择。

要开始使用逻辑应用，请先[创建第一个逻辑应用](quickstart-create-first-logic-app-workflow.md)。 有关逻辑应用的更多技术信息，请参阅[开发人员参考](logic-apps-workflow-definition-language.md)。

## <a name="what-is-azure-functions"></a>Azure Functions 是什么？

Azure Functions 是一项服务，用于在云中轻松运行代码段或 "函数"。 你可以仅编写当前问题所需的代码，而无需担心整个应用或所需的基础结构。 Functions 可使开发更有效率，并可以使用自己所选的开发语言，例如 C#、F#、Node.js、Python 或 PHP。 只需为代码运行的时间付费，Azure 会根据需要进行缩放。

若要开始 Azure Functions，请从[创建第一个 Azure 函数](../azure-functions/functions-create-first-azure-function.md)开始。 有关函数的更多技术信息，请参阅[开发人员参考](../azure-functions/functions-reference.md)。

## <a name="how-can-i-build-and-deploy-serverless-apps-in-azure"></a>如何在 Azure 中构建和部署无服务器应用？

Azure 提供丰富的工具，用于开发、部署和管理无服务器应用。 可以通过[Visual Studio](logic-apps-serverless-get-started-vs.md)中的工具或[Visual Studio Code](quickstart-create-logic-apps-visual-studio-code.md)直接在 Azure 门户中生成应用。 生成应用后，可以[通过 Azure 资源管理器模板快速部署该应用](logic-apps-deploy-azure-resource-manager-templates.md)。 Azure 还提供监视，可以通过 Azure 门户、API 或 Sdk 访问该工具，也可以使用集成的工具 Azure Monitor 日志和 Application Insights 进行监视。

## <a name="next-steps"></a>后续步骤

* [在 Visual Studio 中构建无服务器应用](logic-apps-serverless-get-started-vs.md)
* [创建包含无服务器的 customer insights 仪表板](logic-apps-scenario-social-serverless.md)
* [自动部署逻辑应用](logic-apps-azure-resource-manager-templates-overview.md)
