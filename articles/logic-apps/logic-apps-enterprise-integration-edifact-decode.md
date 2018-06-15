---
title: 为 EDIFACT 消息解码 - Azure 逻辑应用 | Microsoft 文档
description: 使用 Enterprise Integration Pack 中的 EDIFACT 消息解码器为 Azure 逻辑应用验证 EDI 并生成确认
services: logic-apps
documentationcenter: .net,nodejs,java
author: padmavc
manager: jeconnoc
editor: ''
ms.assetid: 0e61501d-21a2-4419-8c6c-88724d346e81
ms.service: logic-apps
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/27/2017
ms.author: LADocs; padmavc
ms.openlocfilehash: bfb64b4abfca6e2a113489b79405b5df2b53c049
ms.sourcegitcommit: 6f6d073930203ec977f5c283358a19a2f39872af
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/11/2018
ms.locfileid: "35299148"
---
# <a name="decode-edifact-messages-for-azure-logic-apps-with-the-enterprise-integration-pack"></a>使用 Enterprise Integration Pack 为 Azure 逻辑应用解码 EDIFACT 消息

使用解码 EDIFACT 消息连接器，可验证 EDI 和特定于合作伙伴的属性、将交换拆分为事务集或保留整个交换，以及为已处理的交易生成确认。 要使用此连接器，必须将此连接器添加到逻辑应用中的现有触发器。

## <a name="before-you-start"></a>开始之前

需要具有以下各项：

* Azure 帐户；可以创建[免费帐户](https://azure.microsoft.com/free)
* 已定义的、与 Azure 订阅关联的[集成帐户](logic-apps-enterprise-integration-create-integration-account.md)。 必须拥有集成帐户，才能使用解码 EDIFACT 消息连接器。 
* 已在集成帐户中至少定义了两个[合作伙伴](logic-apps-enterprise-integration-partners.md)
* 已在集成帐户中定义了 [EDIFACT 协议](logic-apps-enterprise-integration-edifact.md)

## <a name="decode-edifact-messages"></a>为 EDIFACT 消息解码

1. [创建逻辑应用](quickstart-create-first-logic-app-workflow.md)。

2. 解码 EDIFACT 消息连接器没有触发器，因此必须添加用于启动逻辑应用的触发器，如请求触发器。 在逻辑应用设计器中，添加触发器，然后将操作添加到逻辑应用。

3. 在搜索框中，输入“EDIFACT”作为筛选器。 选择“解码 EDIFACT 消息”。
   
    ![搜索 EDIFACT](./media/logic-apps-enterprise-integration-edifact-decode/edifactdecodeimage1.png)

3. 如果以前未创建与集成帐户的任何连接，系统会提示现在创建该连接。 为连接命名，并选择要连接的集成帐户。
   
    ![创建集成帐户](./media/logic-apps-enterprise-integration-edifact-decode/edifactdecodeimage2.png)

    带有星号的属性必填。

    | 属性 | 详细信息 |
    | --- | --- |
    | 连接名称 * |为连接输入任何名称。 |
    | 集成帐户 * |输入集成帐户的名称。 确保集成帐户和逻辑应用位于同一 Azure 位置。 |

4. 在完成连接创建时，选择“创建”。 连接详细信息应如此示例所示：

    ![集成帐户详细信息](./media/logic-apps-enterprise-integration-edifact-decode/edifactdecodeimage3.png)  

5. 在创建连接后，选择要解码的 EDIFACT 平面文件消息，如此示例中所示。

    ![集成帐户连接已创建](./media/logic-apps-enterprise-integration-edifact-decode/edifactdecodeimage4.png)  

    例如：

    ![选择要解码的 EDIFACT 平面文件消息](./media/logic-apps-enterprise-integration-edifact-decode/edifactdecodeimage5.png)  

## <a name="edifact-decoder-details"></a>EDIFACT 解码器详细信息

解码 EDIFACT 连接器执行以下任务： 

* 针对贸易合作伙伴协议验证信封。
* 通过将发送方限定符和标识符与接收方限定符和标识符进行匹配来解析协议。
* 当交换有多个基于协议的接收设置配置的事务时，将交换拆分为多个事务。
* 分解交换。
* 验证 EDI 和特定于合作伙伴的属性包括：
  * 验证交换信封结构
  * 针对控制架构对信封进行架构验证
  * 针对消息架构对事务集数据元素进行架构验证
  * 对事务集数据元素执行的 EDI 验证
* 验证交换、组和事务集控制编号不重复（如果已配置） 
  * 针对以前收到的交换检查交换控制编号。 
  * 针对交换中的其他组控制编号检查组控制编号。 
  * 针对该组中的其他事务集控制编号检查事务集控制编号。
* 将交换拆分为事务集，或保留整个交换：
  * 将交换拆分为交易集 - 出错时暂停交易集：将交换拆分为交易集并分析每个交易集。 
  X12 解码操作仅将未通过验证的事务集输出到 `badMessages`，并将剩余事务集输出到 `goodMessages`。
  * 将交换拆分为交易集 - 出错时暂停交换：将交换拆分为交易集并分析每个交易集。 
  如果交换中的一个或多个交易集未能通过验证，X12 解码操作会将该交换中的所有交易集输出到 `badMessages`。
  * 保留交换 - 出错时暂停交易集：保留交换并处理整个批量交换。 
  X12 解码操作仅将未通过验证的事务集输出到 `badMessages`，并将剩余事务集输出到 `goodMessages`。
  * 保留交换 - 出错时暂停交易集：保留交换并处理整个批量交换。 
  如果交换中的一个或多个交易集未能通过验证，X12 解码操作会将该交换中的所有交易集输出到 `badMessages`。
* 生成技术（控制）和/或功能确认（如果已配置）。
  * 技术确认或 CONTRL ACK 报告收到的完整交换的语法检查结果。
  * 功能确认会确认接受或拒绝收到的交换或组

## <a name="view-swagger-file"></a>查看 Swagger 文件
若要查看 EDIFACT 连接器的 Swagger 详细信息，请参阅 [EDIFACT](/connectors/edifact/)。

## <a name="next-steps"></a>后续步骤
[了解有关 Enterprise Integration Pack 的详细信息](logic-apps-enterprise-integration-overview.md "了解 Enterprise Integration Pack") 

