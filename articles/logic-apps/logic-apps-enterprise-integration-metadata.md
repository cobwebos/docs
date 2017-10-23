---
title: "管理集成帐户项目元数据 - Azure 逻辑应用 |Microsoft Docs"
description: "在集成帐户中为 Azure 逻辑应用添加或检索项目元数据"
author: padmavc
manager: anneta
editor: 
services: logic-apps
documentationcenter: 
ms.assetid: bb7d9432-b697-44db-aa88-bd16ddfad23f
ms.service: logic-apps
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.custom: H1Hack27Feb2017
ms.date: 11/21/2016
ms.author: LADocs; padmavc
ms.openlocfilehash: 28bb8296ddd820ec5aa9793dc0928b4b1e67bf6f
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/11/2017
---
# <a name="manage-artifact-metadata-in-integration-accounts-for-logic-apps"></a>在集成帐户中为逻辑应用管理项目元数据

可以在集成帐户中定义项目的自定义元数据，并且在运行时期间为逻辑应用检索该元数据。 例如，可以指定项目的元数据（如合作伙伴、协议、架构和映射）- 所有使用键/值对的存储元数据。 目前，项目无法通过 UI 创建元数据，但可以使用 REST API 创建元数据。 若要在 Azure 门户中创建或选择合作伙伴、协议或架构时添加元数据，请选择“以 JSON 编辑”。 若要在逻辑应用中检索项目元数据，可以使用集成帐户项目查找功能。

## <a name="add-metadata-to-artifacts-in-integration-accounts"></a>在集成帐户中将元数据添加到项目

1. 创建[集成帐户](logic-apps-enterprise-integration-create-integration-account.md)。

2. 将项目添加到集成帐户，例如[合作伙伴](logic-apps-enterprise-integration-partners.md#how-to-create-a-partner)、[协议](logic-apps-enterprise-integration-agreements.md#how-to-create-agreements)或[架构](logic-apps-enterprise-integration-schemas.md)。

3.  选择项目，选择“以 JSON 编辑”并输入元数据详细信息。

    ![输入元数据](media/logic-apps-enterprise-integration-metadata/image1.png)

## <a name="retrieve-metadata-from-artifacts-for-logic-apps"></a>从项目中为逻辑应用检索元数据

1. 创建[逻辑应用](logic-apps-create-a-logic-app.md)。

2. 创建[从逻辑应用到集成帐户的链接](logic-apps-enterprise-integration-create-integration-account.md#link-an-integration-account-to-a-logic-app)。 

3. 在逻辑应用设计器中，将触发器（如请求或 HTTP）添加到逻辑应用。

4.  选择“下一步” > “添加操作”。 搜索集成以便找到并选择“集成帐户 - 集成帐户项目查找”。

    ![选择“集成帐户项目查找”](media/logic-apps-enterprise-integration-metadata/image2.png)

5. 选择“项目类型”并提供“项目名称”。

    ![选择项目类型并指定项目名称](media/logic-apps-enterprise-integration-metadata/image3.png)

## <a name="example-retrieve-partner-metadata"></a>示例：检索合作伙伴元数据

合作伙伴元数据含有以下 `routingUrl` 详细信息：

![查找合作伙伴“routingURL”元数据](media/logic-apps-enterprise-integration-metadata/image6.png)

1. 在逻辑应用中，添加触发器、用于合作伙伴的“集成帐户 - 集成帐户项目查找”操作和“HTTP”。

    ![将触发器、项目查找和“HTTP”添加到逻辑应用](media/logic-apps-enterprise-integration-metadata/image4.png)

2. 若要检索 URI，请转到逻辑应用的代码视图。 逻辑应用定义应如以下示例所示：

    ![搜索查找](media/logic-apps-enterprise-integration-metadata/image5.png)


## <a name="next-steps"></a>后续步骤
* [了解有关协议的详细信息](logic-apps-enterprise-integration-agreements.md "了解企业集成协议")  
