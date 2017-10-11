---
title: "逻辑应用 B2B edifact 解码解析 UNH2.5 - Azure 逻辑应用 | Microsoft Docs"
description: "Azure 逻辑应用 B2B edifact 解码解析 UNH2.5"
services: logic-apps
documentationcenter: .net,nodejs,java
author: padmavc
manager: anneta
editor: 
ms.assetid: cf44af18-1fe5-41d5-9e06-cc57a968207c
ms.service: logic-apps
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/27/2017
ms.author: LADocs; padmavc
ms.openlocfilehash: 62ad8183cc6e9f56255b2729a04ee7710d00a21a
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/11/2017
---
# <a name="how-to-handle-edifact-documents-having-unh25-segment"></a>如何处理具有 UNH2.5 段的 EDIFACT 文档
EDIFACT 文档中存在 UNH2.5 时，它用于架构查找。 

示例：UNH 字段是 EDIFACT 消息中的 **EAN008**  
UNH+SSDD1+ORDERS:D:03B:UN:**EAN008**'  

处理消息要遵循的步骤 
1. 更新架构
2. 检查协议设置  

## <a name="update-the-schema"></a>更新架构
若要处理消息，需要部署具有 UNH2.5 根节点名称的架构。  对于给定的示例，架构根名称将为 **EFACT_D03B_ORDERS_EAN008**  

对于具有不同 UNH2.5 段的每个 D03B_ORDERS，将需要部署单个架构。  

## <a name="add-schema-to-the-edifact-agreement"></a>将架构添加到 EDIFACT 协议
### <a name="edifact-decode"></a>EDIFACT 解码
若要解码传入消息，请在 EDIFACT 协议接收设置中配置架构
1. 将架构添加到集成帐户    
2. 在 EDIFACT 协议接收设置中配置架构。 
3. 选择 EDIFACT 协议，并单击“编辑为 JSON”。  在接收协议 **schemaReferences**
![](./media/logic-apps-enterprise-integration-edifact_inputfile_unh2.5/image1.png) 中添加 UNH2.5 值

### <a name="edifact-encode"></a>EDIFACT 编码
若要编码传入消息，请在 EDIFACT 协议发送设置中配置架构
1. 将架构添加到集成帐户    
2. 在 EDIFACT 协议发送设置中配置架构。 
3. 选择 EDIFACT 协议，并单击“编辑为 JSON”。  在发送协议 **schemaReferences**
![](./media/logic-apps-enterprise-integration-edifact_inputfile_unh2.5/image2.png) 中添加 UNH2.5 值

## <a name="next-steps"></a>后续步骤
* [详细了解集成帐户协议](../logic-apps/logic-apps-enterprise-integration-agreements.md "了解企业集成协议")  