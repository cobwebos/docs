---
title: "自定义连接器概述 - Azure 逻辑应用 | Microsoft Docs"
description: "有关创建自定义连接器以支持和扩展集成方案的概述"
author: ecfan
manager: anneta
editor: 
services: logic-apps
documentationcenter: 
ms.assetid: 
ms.service: logic-apps
ms.workload: logic-apps
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/22/2017
ms.author: LADocs; estfan
ms.openlocfilehash: 0515b21bc7c7cc737e14fda016606bbea1aab476
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/11/2017
---
# <a name="custom-connectors-overview"></a>自定义连接器概述

可使用 [Azure 逻辑应用](https://azure.microsoft.com/services/logic-apps/)、[Microsoft Flow](https://flow.microsoft.com) 或 [Microsoft PowerApps](https://powerapps.microsoft.com) 生成工作流或应用，而无需编写任何代码。 为帮助你集成数据和业务流程，这些服务不仅为 Azure 和 SQL Server 等 Microsoft 服务和产品提供 [100 多个连接器](../connectors/apis-list.md)，同时也为 GitHub、Salesforce、Twitter 等其他服务提供。 

但有些时候，可能想要调用不可用作预生成的连接器的 API、服务和系统。 若要支持针对性更强的方案以满足用户的业务和生产力需求，可以使用他们自己的触发器和操作生成自定义连接器。
自定义连接器扩展了服务或产品的集成、可访问性、可发现性以及使用，这有助于提升客户采用率并加速客户的采用。

例如，此图示显示了一个 Web API、一个为该 API 创建的自定义逻辑应用连接器以及一个通过该自定义连接器与 API 配合使用的逻辑应用之间的交互：

![Web API、自定义连接器和逻辑应用的概念概述](./media/custom-connector-overview/custom-connector-conceptual.png)

此概述概括了创建、保护、注册和使用连接器以及选择性共享或认证连接器的常规高级任务：

![自定义连接器创作步骤](./media/custom-connector-overview/authoring-steps.png)

## <a name="prerequisites"></a>先决条件

若要从头至尾生成连接器，需要以下各项：

* Azure 订阅。 如果没有订阅，可以开始使用[免费的 Azure 帐户](https://azure.microsoft.com/free/)。 也可注册[即用即付订阅](https://azure.microsoft.com/pricing/purchase-options/)。

* 具有某种已经过身份验证的访问类型的一个 RESTful API。 有关详细信息，请参阅[从 Web API 创建自定义连接器](../logic-apps/custom-connector-build-web-api-app-tutorial.md)。 有关可用于连接器的触发器和操作的模式，请参阅[创建可从逻辑应用工作流调用的自定义 API](../logic-apps/logic-apps-create-api-app.md)。

* 以下任何项：

  * 一个 [OpenAPI 2.0 文件](https://github.com/OAI/OpenAPI-Specification/blob/master/versions/2.0.md)，之前称为 Swagger
  * 一个转至 OpenAPI 定义的 URL
  * 用于 API 的一个 [Postman 集合](../logic-apps/custom-connector-api-postman-collection.md) 

  如果没有上述任何一项，我们会为你提供指南。

* 可选：用作自定义连接器图标的一个图像

## <a name="1-build-your-restful-api"></a>1.生成 RESTful API

从技术上讲，连接器是围绕基于 OpenAPI（之前称为 Swagger）规范的 REST API 的包装器，并且使基础服务能够与逻辑应用、Microsoft Flow 或 PowerApps 通信。 因此，在创建自定义连接器之前，首先需要一个能完全正常工作的 API。 

可以为 API 使用任何语言和平台。 对于 Microsoft 技术，建议使用以下平台之一：

* [Azure Functions](https://azure.microsoft.com/services/functions/)
* [Azure Web 应用](https://azure.microsoft.com/services/app-service/web/)
* [Azure API 应用](https://azure.microsoft.com/services/app-service/api/)

例如，本教程演示[如何从 Web API 生成自定义连接器](../logic-apps/custom-connector-build-web-api-app-tutorial.md)。 有关可用于连接器的触发器和操作的模式，请参阅[创建可从逻辑应用工作流调用的自定义 API](../logic-apps/logic-apps-create-api-app.md)。

## <a name="2-secure-your-api"></a>2.保护 API

可以为 API 和连接器使用以下身份验证标准：

   * [OAuth 2.0](https://oauth.net/2/)，包括 [Azure Active Directory](https://azure.microsoft.com/develop/identity/) 或 Dropbox、GitHub 和 SalesForce 等特定服务
   * 通用 OAuth 2.0
   * [基本身份验证](https://swagger.io/docs/specification/authentication/basic-authentication/)
   * [API 密钥](https://swagger.io/docs/specification/authentication/api-keys/)

可以在 Azure 门户中为 API 设置 Azure Active Directory (Azure AD) 身份验证，这样便无需通过代码来实现身份验证。 或者，还可利用 API 代码要求并强制执行身份验证。 

有关详细信息，请参阅相应教程：

* [逻辑应用：保护自定义连接器](../logic-apps/custom-connector-azure-active-directory-authentication.md)
* [Microsoft Flow：保护自定义连接器](https://ms.flow.microsoft.com/documentation/customapi-azure-resource-manager-tutorial/)
* [PowerApps：保护自定义连接器](https://powerapps.microsoft.com/tutorials/customapi-azure-resource-manager-tutorial/)

## <a name="3-describe-your-api"></a>3.描述 API 

假设 API 具有某种已经过身份验证的访问类型，则需要描述 API 的接口和操作，使逻辑应用、Microsoft Flow 或 PowerApps 能够与 API 进行通信。
使用以下行业标准定义之一：

* 一个 [OpenAPI 2.0 文件](https://swagger.io/)，可以先使用现有 OpenAPI 文件进行生成。

  如果不熟悉 OpenAPI，请访问 swagger.io 站点上的 [Getting Started with Swagger](http://swagger.io/getting-started/)（Swagger 入门）。
  如需 OpenAPI 文件的示例，请参阅[文本分析 API 文档](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics.V2.0/export?DocumentFormat=Swagger&ApiName=Azure)。 

* 一个 Postman 集合，它会自动生成一个 OpenAPI 文件。 在尚没有 OpenAPI 文件且不想创建该文件时，仍可使用 Postman 集合轻松创建自定义连接器。

  如果不熟悉 Postman，请从其站点[安装 Postman 应用](https://www.getpostman.com/apps)。 有关详细信息，请参阅[使用 Postman 描述自定义连接器](../logic-apps/custom-connector-api-postman-collection.md)。

> [!IMPORTANT]
> 你的文件大小必须小于 1 MB。

在后台，逻辑应用、Microsoft Flow 和 PowerApps 最终会使用 OpenAPI、分析 Postman 集合并将该集合转换为 OpenAPI 定义文件。 虽然 OpenAPI 2.0 和 Postman 集合使用不同的格式，但二者都是与语言无关、计算机可读的文档，用于描述 API 的操作和参数。 根据 API 所用的语言和平台，还可以各种工具生成这些文档。 注册连接器时，也可以生成 OpenAPI 文件。

例如，可从任何 REST API 终结点创建 OpenAPI 文件或 Postman 集合，包括：

* 公开发布的连接器，例如 [Spotify](https://developer.spotify.com/)、[Slack](https://api.slack.com/)、[Rackspace](http://docs.rackspace.com/) 等等

* 创建并部署到任何云托管提供商（例如 Azure、Heroku、Google Cloud 等）的 API

* 部署到网络上的自定义业务线 API，前提是该 API 已在公共 Internet 上公开

## <a name="4-register-your-connector"></a>4.注册连接器

注册流程可帮助逻辑应用、Microsoft Flow 或 PowerApps 了解 API 的特征，包括说明、所需的身份验证以及操作（包括每个操作的参数和输出）。 开始进行注册流程时，可提供 OpenAPI 文件或 Postman 集合，这会自动填充注册向导中的元数据字段。 可以随时编辑这些字段的值。

![描述 API](./media/custom-connector-overview/choose-api-definition-file.png)

若要注册连接器，请按以下相应教程进行操作：

* [逻辑应用：注册连接器](../logic-apps/logic-apps-custom-connector-register.md)
* [Microsoft Flow：注册连接器](https://ms.flow.microsoft.com/documentation/register-custom-api/#register-your-custom-connector)
* [PowerApps：注册连接器](https://powerapps.microsoft.com/tutorials/register-custom-api/#register-your-custom-connector)

## <a name="5-use-your-connector-in-a-logic-app-flow-or-app"></a>5.在逻辑应用、Microsoft Flow 或应用中使用连接器 

可采用与使用 Microsoft 托管连接器相同的方式使用连接器。 例如，在逻辑应用流中添加自定义连接器，从而可创建到 API 的连接，并采用与为 Microsoft 托管连接器调用操作相同的方式调用该 API 提供的任何操作。

## <a name="6-share-your-connector"></a>6.共享连接器 

可采用与在逻辑应用、Microsoft Flow 或 PowerApps 中共享资源相同的方式与组织中的用户共享连接器。 尽管共享是可选操作，但也可能存在想要与其他用户共享连接器的情况。

已注册但未认证的自定义连接器与 Microsoft 托管连接器的工作方式类似，但前者仅对该连接器的创建者以及在部署逻辑应用的区域中对这些应用具有同一 Azure Active Directory 租户和 Azure 订阅的用户可见，并且仅可供他们使用。 下一步是讨论如何与这些边界外的外部用户共享连接器，例如与所有逻辑应用、Microsoft Flow 和 PowerApps 用户共享。

> [!IMPORTANT]
> 如果共享一个连接器，其他人可能会开始依赖该连接器。 
> 删除连接器会删除到该连接器的所有连接。

* [逻辑应用：共享连接器](../logic-apps/logic-apps-custom-connector-register.md)
* [Microsoft Flow：共享连接器](https://ms.flow.microsoft.com/documentation/register-custom-api/#share-your-custom-connector)
* [PowerApps：共享连接器](https://powerapps.microsoft.com/tutorials/register-custom-api/#share-your-custom-connector)

## <a name="7-certify-your-connector"></a>7.认证连接器

若要选择性地与逻辑应用、Microsoft Flow 和 PowerApps 中的所有用户共享连接器，请提交连接器以进行 Microsoft 认证。 在 Microsoft 发布连接器之前，此流程会审查连接器、检查技术和内容符合性，并针对逻辑应用、Microsoft Flow 和 PowerApps 验证功能性。 了解[如何提交连接器以进行 Microsoft 认证](../logic-apps/custom-connector-submit-certification.md)。

## <a name="get-support"></a>获取支持

* 有关载入和开发支持，请发送电子邮件至 [condevhelp@microsoft.com](mailto:condevhelp@microsoft.com)。Microsoft 会主动监视此帐户以了解开发人员的问题和疑问，并将他们路由到相应的团队。 

* 有关常见问题解答，请参阅[自定义连接器常见问题解答](../logic-apps/custom-connector-faq.md)

## <a name="next-steps"></a>后续步骤

* [从 Web API 生成自定义连接器](../logic-apps/custom-connector-build-web-api-app-tutorial.md)
* [为自定义连接器设置身份验证](../logic-apps/custom-connector-azure-active-directory-authentication.md)
* [使用 Postman 集合描述自定义连接器](../logic-apps/custom-connector-api-postman-collection.md)
* [提交自定义连接器以进行 Microsoft 认证](../logic-apps/custom-connector-submit-certification.md)
