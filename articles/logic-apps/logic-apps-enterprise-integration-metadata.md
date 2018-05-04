---
title: 管理集成帐户项目元数据 - Azure 逻辑应用 |Microsoft Docs
description: 在集成帐户中为 Azure 逻辑应用添加或检索项目元数据
author: padmavc
manager: anneta
editor: ''
services: logic-apps
documentationcenter: ''
ms.assetid: bb7d9432-b697-44db-aa88-bd16ddfad23f
ms.service: logic-apps
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.custom: H1Hack27Feb2017
ms.date: 02/23/2018
ms.author: LADocs; padmavc
ms.openlocfilehash: 8ebca60c21366cddadccfd0456880696457d7777
ms.sourcegitcommit: 6e43006c88d5e1b9461e65a73b8888340077e8a2
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/01/2018
---
# <a name="manage-artifact-metadata-in-integration-accounts-for-logic-apps"></a>在集成帐户中为逻辑应用管理项目元数据

可以在集成帐户中定义项目的自定义元数据，并且在运行时期间为逻辑应用检索该元数据。 例如，可以指定项目的元数据（如合作伙伴、协议、架构和映射）- 所有使用键/值对的存储元数据。 

## <a name="add-metadata-to-artifacts-in-integration-accounts"></a>在集成帐户中将元数据添加到项目

1. 在 Azure 门户中，创建[集成帐户](logic-apps-enterprise-integration-create-integration-account.md)。

2. 将项目添加到集成帐户，例如[合作伙伴](logic-apps-enterprise-integration-partners.md)、[协议](logic-apps-enterprise-integration-agreements.md)或[架构](logic-apps-enterprise-integration-schemas.md)。

3. 选择项目，选择“编辑”并输入元数据详细信息。

   ![输入元数据](media/logic-apps-enterprise-integration-metadata/image1.png)

## <a name="retrieve-metadata-from-artifacts-for-logic-apps"></a>从项目中为逻辑应用检索元数据

1. 在 Azure 门户中，创建一个[逻辑应用](quickstart-create-first-logic-app-workflow.md)。

2. 创建[从逻辑应用到集成帐户的链接](logic-apps-enterprise-integration-create-integration-account.md#link-account)。 

3. 在逻辑应用设计器中，将触发器（如请求或 HTTP）添加到逻辑应用。

4. 在该触发器下，选择“新建步骤” > “添加操作”。 搜索“集成帐户”以便找到并选择此操作：“集成帐户 - 集成帐户项目查找”

   ![选择“集成帐户项目查找”](media/logic-apps-enterprise-integration-metadata/image2.png)

5. 选择“项目类型”并提供“项目名称”。 例如：

   ![选择项目类型并指定项目名称](media/logic-apps-enterprise-integration-metadata/image3.png)

## <a name="example-retrieve-partner-metadata"></a>示例：检索合作伙伴元数据

假设该合作伙伴具有此元数据和 `routingUrl` 详细信息：

![查找合作伙伴“routingURL”元数据](media/logic-apps-enterprise-integration-metadata/image6.png)

1. 在逻辑应用中，添加触发器、用于合作伙伴的“集成帐户 - 集成帐户项目查找”操作和“HTTP”操作，例如：

   ![将触发器、“项目查找”操作和“HTTP”操作添加到逻辑应用](media/logic-apps-enterprise-integration-metadata/image4.png)

2. 若要检索 URI，请在逻辑应用设计器工具栏上，针对逻辑应用选择“代码视图”。 逻辑应用定义应如以下示例所示：

   ![搜索查找](media/logic-apps-enterprise-integration-metadata/image5.png)

## <a name="next-steps"></a>后续步骤

* [了解有关协议的详细信息](logic-apps-enterprise-integration-agreements.md)
