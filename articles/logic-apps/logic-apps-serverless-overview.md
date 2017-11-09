---
title: "Azure 无服务器产品/服务概述 | Microsoft Docs"
description: "在云中创建功能强大的解决方案，而无需考虑基础结构。"
keywords: 
services: logic-apps
author: jeffhollan
manager: anneta
editor: 
documentationcenter: 
ms.assetid: d565873c-6b1b-4057-9250-cf81a96180ae
ms.service: logic-apps
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/30/2017
ms.author: LADocs; jehollan
ms.openlocfilehash: 6803e22a78e27c15ff4fec301cd5bdd55aacd3e3
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/11/2017
---
# <a name="overview-of-azure-serverless-with-functions-and-logic-apps"></a>Azure 无服务器产品/服务以及 Functions 和逻辑应用的概述

无服务器应用程序提供了以下优势：部署速度快、减少了所需的代码，并且缩放简单。  本文将探讨无服务器解决方案的各种属性以及 Azure 无服务器产品/服务。

## <a name="what-is-serverless"></a>什么是无服务器？

无服务器的意思并不是说没有服务器，它只是说开发人员不需要考虑服务器。  在传统应用程序开发中，很大一部分工作是解决与缩放、托管以及监视解决方案相关的问题以满足应用程序的需求。  使用无服务器产品/服务时，解决方案中已考虑了这些问题。  此外，无服务器应用程序是按基于消耗的计划计费的。  如果永远不使用应用程序，则永远不会产生费用。  这些功能使得开发人员可以集中精力来处理解决方案的业务逻辑。

Azure 中围绕无服务器产品/服务的核心服务有 [Azure Functions](https://azure.microsoft.com/services/functions/) 和 [Azure 逻辑应用](https://azure.microsoft.com/services/logic-apps/)。  这两种解决方案都遵守上述原则，并使开发人员可以通过最少的代码构建可靠的云应用程序。

## <a name="what-are-azure-functions"></a>什么是 Azure Functions？

Azure Functions 是用于在云中轻松运行小段代码或“函数”的一个解决方案。 用户可以只编写解决现有问题所需的代码，而无需担心要运行该代码的整个应用程序或基础结构。 Functions 可使开发更有效率，并可以使用自己所选的开发语言，例如 C#、F#、Node.js、Python 或 PHP。 只需为代码运行的时间付费，并且 Azure 会根据需要进行缩放。

如果要立即投入和开始使用 Azure Functions，请从 [创建第一个 Azure 函数](../azure-functions/functions-create-first-azure-function.md)开始。 如果要查找有关 Functions 的更多技术信息，请参阅 [开发人员参考](../azure-functions/functions-reference.md)。

## <a name="what-are-azure-logic-apps"></a>什么是 Azure 逻辑应用？

Azure 逻辑应用提供了用于在云中简化并实现可缩放的集成和工作流的方式。 它提供了可视化设计器，用于为流程建模并将流程作为一系列步骤（称为工作流）自动执行。  在云服务和本地服务之间有[许多连接器](../connectors/apis-list.md)可用来快速将无服务器应用连接到其他 API。  逻辑应用以触发器开头（例如，“当将帐户添加到 Dynamics CRM 时”），在触发之后许多组合操作、转换和条件逻辑才能开始。  在流程中安排不同的 Azure Functions 时，逻辑应用是一个很好的选择 - 尤其是当流程需要与外部系统或 API 进行交互时。

要开始使用逻辑应用，请先[创建第一个逻辑应用](logic-apps-create-a-logic-app.md)。  如果要查找有关逻辑应用的更多技术信息，请参阅[开发人员参考](logic-apps-workflow-actions-triggers.md)。

## <a name="how-can-i-build-and-deploy-serverless-applications-in-azure"></a>如何在 Azure 中构建和部署无服务器应用程序？

Azure 提供了在开发、部署和管理无服务器应用时可以使用的一组丰富工具。  可以直接在 Azure 门户中构建这类应用，也可以使用 [Visual Studio 中的工具](logic-apps-serverless-get-started-vs.md)进行构建。  开发应用程序后，可以将其[立即部署](logic-apps-create-deploy-template.md)。  Azure 还提供了针对无服务器应用程序的监视功能。  可以从 Azure 门户、通过 API 或 SDK 或者使用集成到 OMS 和 Application Insights 的工具来访问此监视功能。

## <a name="next-steps"></a>后续步骤

* [开始在 Visual Studio 中构建无服务器应用](logic-apps-serverless-get-started-vs.md)
* [使用无服务器产品/服务创建 customer insights 仪表板](logic-apps-scenario-social-serverless.md)
* [为逻辑应用构建部署模板](logic-apps-create-deploy-template.md)