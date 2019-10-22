---
title: EDIFACT 消息中的 UNH 2.5 段-Azure 逻辑应用
description: 通过 Enterprise Integration Pack 在 Azure 逻辑应用中通过 UNH 2.5 段解析 EDIFACT 消息
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: jonfan, estfan, LADocs
ms.topic: article
ms.date: 04/27/2017
ms.openlocfilehash: c30c35375a45171c938f80e94dd7d9be4c3ee8b1
ms.sourcegitcommit: d37991ce965b3ee3c4c7f685871f8bae5b56adfa
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/21/2019
ms.locfileid: "72679928"
---
# <a name="handle-edifact-documents-with-unh25-segments-in-azure-logic-apps"></a>在 Azure 逻辑应用中处理具有 UNH2.5 段的 EDIFACT 文档

如果 EDIFACT 文档中存在 UNH 2.5 段，则段用于架构查找。 例如，在此示例 EDIFACT 消息中，"UNH" 字段 `EAN008`：

`UNH+SSDD1+ORDERS:D:03B:UN:EAN008`

若要处理此消息，请执行下面所述的以下步骤：

1. 更新架构。

1. 检查协议设置。

## <a name="update-the-schema"></a>更新架构

若要处理消息，需要部署具有 UNH 2.5 根节点名称的架构。 例如，`EFACT_D03B_ORDERS_EAN008` 示例 UNH 字段的架构根名称。 对于具有不同 UNH 2.5 段的每个 `D03B_ORDERS`，你必须部署一个单独的架构。

## <a name="add-schema-to-edifact-agreement"></a>向 EDIFACT 协议添加架构

### <a name="edifact-decode"></a>EDIFACT 解码

若要解码传入消息，请在 EDIFACT 协议的接收设置中设置架构：

1. 在[Azure 门户](https://portal.azure.com)中，打开你的集成帐户。

1. 将架构添加到集成帐户。

1. 在 EDIFACT 协议的接收设置中配置架构。

1. 选择 EDIFACT 协议，然后选择 "**编辑为 JSON**"。 将 UNH 2.5 值添加到接收协议的 `schemaReferences` 部分：

   ![将 UNH 2.5 添加到接收协议](./media/logic-apps-enterprise-integration-edifact_inputfile_unh2.5/image1.png)

### <a name="edifact-encode"></a>EDIFACT 编码

若要对传入消息进行编码，请在 EDIFACT 协议发送设置中配置架构

1. 在[Azure 门户](https://portal.azure.com)中，打开你的集成帐户。

1. 将架构添加到集成帐户。

1. 在 EDIFACT 协议的发送设置中配置架构。

1. 选择 EDIFACT 协议，并单击“编辑为 JSON”。  在发送协议**schemaReferences**中添加 unh 2.5 值

1. 选择 EDIFACT 协议，然后选择 "**编辑为 JSON**"。 将 UNH 2.5 值添加到发送协议的 `schemaReferences` 部分：

   ![将 UNH 2.5 添加到发送协议](./media/logic-apps-enterprise-integration-edifact_inputfile_unh2.5/image2.png)

## <a name="next-steps"></a>后续步骤

* 了解有关[集成帐户协议](../logic-apps/logic-apps-enterprise-integration-agreements.md)的详细信息