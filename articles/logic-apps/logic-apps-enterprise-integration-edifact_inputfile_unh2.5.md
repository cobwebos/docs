---
title: 联合国H 2.5 分段在EDIFACT电给
description: 使用企业集成包在 Azure 逻辑应用中使用 UNH2.5 段解析 EDIFACT 消息
services: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: jonfan, estfan, logicappspm
ms.topic: article
ms.date: 04/27/2017
ms.openlocfilehash: ad50cbb423f8c60f1caad159bc1a20cf96ed98aa
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/27/2020
ms.locfileid: "74792538"
---
# <a name="handle-edifact-documents-with-unh25-segments-in-azure-logic-apps"></a>在 Azure 逻辑应用中处理具有 UNH2.5 段的 EDIFACT 文档

如果 EDIFACT 文档中存在 UNH2.5 段，则该段用于架构查找。 例如，在此示例 EDIFACT 消息中，UNH 字段`EAN008`为 ：

`UNH+SSDD1+ORDERS:D:03B:UN:EAN008`

要处理此消息，请按照下面介绍的步骤操作：

1. 更新架构。

1. 检查协议设置。

## <a name="update-the-schema"></a>更新架构

要处理消息，您需要部署具有 UNH2.5 根节点名称的架构。 例如，示例 UNH 字段的架构根名称为`EFACT_D03B_ORDERS_EAN008`。 对于具有`D03B_ORDERS`不同 UNH2.5 段的每个架构，必须部署单个架构。

## <a name="add-schema-to-edifact-agreement"></a>将架构添加到 EDIFACT 协议

### <a name="edifact-decode"></a>EDIFACT 解码

要解码传入消息，请设置 EDIFACT 协议接收设置中的架构：

1. 在[Azure 门户](https://portal.azure.com)中，打开集成帐户。

1. 将架构添加到集成帐户。

1. 在 EDIFACT 协议的接收设置中配置架构。

1. 选择 EDIFACT 协议，然后选择 **"编辑为 JSON**"。 将 UNH2.5 值添加到接收协议部分`schemaReferences`：

   ![添加 UNH2.5 以接收协议](./media/logic-apps-enterprise-integration-edifact_inputfile_unh2.5/image1.png)

### <a name="edifact-encode"></a>EDIFACT 编码

要对传入消息进行编码，请在 EDIFACT 协议发送设置中配置架构

1. 在[Azure 门户](https://portal.azure.com)中，打开集成帐户。

1. 将架构添加到集成帐户。

1. 在 EDIFACT 协议的发送设置中配置架构。

1. 选择 EDIFACT 协议，并单击“编辑为 JSON”****。  在发送协议 **schemaReferences** 中添加 UNH2.5 值

1. 选择 EDIFACT 协议，然后选择 **"编辑为 JSON**"。 将 UNH2.5 值添加到发送协议的`schemaReferences`部分：

   ![添加 UNH2.5 以发送协议](./media/logic-apps-enterprise-integration-edifact_inputfile_unh2.5/image2.png)

## <a name="next-steps"></a>后续步骤

* 了解有关[集成帐户协议的更多内容](../logic-apps/logic-apps-enterprise-integration-agreements.md)