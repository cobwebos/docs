---
title: 管理集成帐户项目元数据 - Azure 逻辑应用 |Microsoft Docs
description: 通过 Azure 逻辑应用和 Enterprise Integration Pack 在集成帐户中添加或获取项目元数据
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: jonfan, estfan, LADocs
ms.topic: article
ms.assetid: bb7d9432-b697-44db-aa88-bd16ddfad23f
ms.date: 01/17/2019
ms.openlocfilehash: 5ebdf45bec4e7cfceb75354af40c7a21c22c6eef
ms.sourcegitcommit: 9b6492fdcac18aa872ed771192a420d1d9551a33
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/22/2019
ms.locfileid: "54446776"
---
# <a name="manage-artifact-metadata-in-integration-accounts-with-azure-logic-apps-and-enterprise-integration-pack"></a>使用 Azure 逻辑应用和 Enterprise Integration Pack 管理集成帐户中的项目元数据

可以在集成帐户中定义项目的自定义元数据，并且在运行时获取该元数据供逻辑应用使用。 例如，可以提供项目的元数据（例如合作伙伴、协议、架构和映射）- 所有使用键值对的存储元数据。 

## <a name="prerequisites"></a>先决条件

* Azure 订阅。 如果没有订阅，可以<a href="https://azure.microsoft.com/free/" target="_blank">注册免费的 Azure 帐户</a>。

* 一个基本的[集成帐户](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md)，其中包含要添加元数据的项目，例如： 

  * [合作伙伴](logic-apps-enterprise-integration-partners.md)
  * [协议](logic-apps-enterprise-integration-agreements.md)
  * [架构](logic-apps-enterprise-integration-schemas.md)
  * [Map](logic-apps-enterprise-integration-maps.md)

* 一个已链接到所要使用的集成帐户和项目元数据的逻辑应用。 如果尚未链接该逻辑应用，请了解[如何将逻辑应用链接到集成帐户](logic-apps-enterprise-integration-create-integration-account.md#link-account)。 

  如果没有逻辑应用，请了解[如何创建逻辑应用](../logic-apps/quickstart-create-first-logic-app-workflow.md)。 
  添加用于管理项目元数据的触发器和操作。 如果你只是想要试用相关功能，请将一个触发器（例如“请求”或“HTTP”）添加到逻辑应用。

## <a name="add-metadata-to-artifacts"></a>将元数据添加到项目

1. 使用 Azure 帐户凭据登录到 <a href="https://portal.azure.com" target="_blank">Azure 门户</a>。 找到并打开你的集成帐户。

1. 选择要在其中添加元数据的项目，然后选择“编辑”。 输入该项目的元数据详细信息，例如：

   ![输入元数据](media/logic-apps-enterprise-integration-metadata/add-partner-metadata.png)

1. 完成后，选择“确定”。

1. 若要在集成帐户的 JavaScript 对象表示法 (JSON) 定义中查看此元数据，请选择“作为 JSON 编辑”以打开 JSON 编辑器： 

   ![合作伙伴元数据的 JSON](media/logic-apps-enterprise-integration-metadata/partner-metadata.png)

## <a name="get-artifact-metadata"></a>获取项目元数据

1. 在 Azure 门户中，打开已链接到所需集成帐户的逻辑应用。 

1. 在逻辑应用设计器中，若要在工作流中的触发器或最后一个操作下添加用于获取元数据的步骤，请选择“新建步骤” > “添加操作”。 

1. 在搜索框中，输入“集成帐户”。 在搜索框下，选择“所有”。 在操作列表中选择此操作：**集成帐户项目查找 - 集成帐户**

   ![选择“集成帐户项目查找”](media/logic-apps-enterprise-integration-metadata/integration-account-artifact-lookup.png)

1. 提供要查找的项目的以下信息：

   | 属性 | 必选 | 值 | 说明 | 
   |----------|---------|-------|-------------| 
   | **项目类型** | 是 | “架构”、“映射”、“合作伙伴”、“协议”或自定义类型 | 所需项目的类型 | 
   | **项目名称** | 是 | <*artifact-name*> | 所需项目的名称 | 
   ||| 

   例如，假设你要获取某个参与方项目的元数据：

   ![选择项目类型并输入项目名称](media/logic-apps-enterprise-integration-metadata/artifact-lookup-information.png)

1. 添加用于处理该元数据的操作，例如：

   1. 在“集成帐户项目查找”操作下选择“下一步”，然后选择“添加操作”。 

   1. 在搜索框中输入“http”。 在搜索框中选择“内置”，然后选择以下操作：**HTTP - HTTP**

      ![添加 HTTP 操作](media/logic-apps-enterprise-integration-metadata/http-action.png)

   1. 提供要管理的项目元数据的信息。 

      例如，假设你要获取在本主题中前面添加的 `routingUrl` 元数据。 下面是可以指定的属性值： 

      | 属性 | 必选 | 值 | 说明 | 
      |----------|----------|-------|-------------| 
      | **方法** | 是 | <*operation-to-run*> | 要针对项目运行的 HTTP 操作。 例如，此 HTTP 操作使用 **GET** 方法。 | 
      | **URI** | 是 | <*metadata-location*> | 若要访问检索的项目中的 `routingUrl` 元数据值，可以使用表达式，例如： <p>`@{outputs('Integration_Account_Artifact_Lookup')['properties']['metadata']['routingUrl']}` | 
      | **标头** | 否 | <*header-values*> | 触发器中的、要传入到 HTTP 操作的所有标头输出。 例如，若要传入触发器的 `headers` 属性值，可以使用表达式，如下所示： <p>`@triggeroutputs()['headers']` | 
      | **正文** | 否 | <body-content> | 要通过 HTTP 操作的 `body` 属性传递的其他任何内容。 此示例将项目的 `properties` 值传入 HTTP 操作： <p>1.在“正文”属性中单击，以显示动态内容列表。 如果未显示任何属性，请选择“查看更多”。 <br>2.在动态内容列表中的“集成帐户项目查找”下，选择“属性”。 | 
      |||| 

      例如：

      ![指定 HTTP 操作的值和表达式](media/logic-apps-enterprise-integration-metadata/add-http-action-values.png)

   1. 若要检查为 HTTP 操作提供的信息，请查看逻辑应用的 JSON 定义。 在逻辑应用设计器工具栏上，选择“代码视图”以显示应用的 JSON 定义，例如：

      ![逻辑应用 JSON 定义](media/logic-apps-enterprise-integration-metadata/finished-logic-app-definition.png)

      切换回到逻辑应用设计器后，使用的所有表达式现在会显示为已求解，例如：

      ![逻辑应用设计器中已求解的表达式](media/logic-apps-enterprise-integration-metadata/resolved-expressions.png)

## <a name="next-steps"></a>后续步骤

* [了解有关协议的详细信息](logic-apps-enterprise-integration-agreements.md)
