---
title: "为 X12 消息解码 - Azure 逻辑应用 | Microsoft 文档"
description: "使用 Enterprise Integration Pack 中的 X12 消息解码器为 Azure 逻辑应用验证 EDI 并生成确认"
services: logic-apps
documentationcenter: .net,nodejs,java
author: padmavc
manager: anneta
editor: 
ms.assetid: 4fd48d2d-2008-4080-b6a1-8ae183b48131
ms.service: logic-apps
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/27/2017
ms.author: LADocs; padmavc
ms.openlocfilehash: bc2e5c2b351fb87cb763459a9e24368a422ada1b
ms.sourcegitcommit: be9a42d7b321304d9a33786ed8e2b9b972a5977e
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/19/2018
---
# <a name="decode-x12-messages-for-azure-logic-apps-with-the-enterprise-integration-pack"></a>使用 Enterprise Integration Pack 为 Azure 逻辑应用解码 X12 消息

使用解码 X12 消息连接器，可针对贸易合作伙伴协议验证信封、EDI 和特定于合作伙伴的属性，将交换拆分为交易集或保留整个交换，以及为已处理的交易生成确认。 要使用此连接器，必须将此连接器添加到逻辑应用中的现有触发器。

## <a name="before-you-start"></a>开始之前

需要具有以下各项：

* Azure 帐户；可以创建[免费帐户](https://azure.microsoft.com/free)
* 已定义的、与 Azure 订阅关联的[集成帐户](logic-apps-enterprise-integration-create-integration-account.md)。 必须拥有集成帐户，才能使用解码 X12 消息连接器。
* 已在集成帐户中至少定义了两个[合作伙伴](logic-apps-enterprise-integration-partners.md)
* 已在集成帐户中定义了 [X12 协议](logic-apps-enterprise-integration-x12.md)

## <a name="decode-x12-messages"></a>解码 X12 消息

1. [创建逻辑应用](quickstart-create-first-logic-app-workflow.md)。

2. 解码 X12 消息连接器没有触发器，因此必须添加用于启动逻辑应用的触发器，如请求触发器。 在逻辑应用设计器中，添加触发器，然后将操作添加到逻辑应用。

3.  在搜索框中，输入“x12”作为筛选器。 选择“X12 - 解码 X12 消息”。
   
    ![搜索“x12”](media/logic-apps-enterprise-integration-x12-decode/x12decodeimage1.png)  

3. 如果以前未创建与集成帐户的任何连接，系统会提示现在创建该连接。 为连接命名，并选择要连接的集成帐户。 

    ![提供集成帐户连接详细信息](media/logic-apps-enterprise-integration-x12-decode/x12decodeimage4.png)

    带有星号的属性必填。

    | 属性 | 详细信息 |
    | --- | --- |
    | 连接名称 * |为连接输入任何名称。 |
    | 集成帐户 * |输入集成帐户的名称。 确保集成帐户和逻辑应用位于同一 Azure 位置。 |

5.  完成后，连接详细信息应如此示例所示。 若要完成连接创建，请选择“创建”。
   
    ![集成帐户连接详细信息](media/logic-apps-enterprise-integration-x12-decode/x12decodeimage5.png) 

6. 在创建连接后，选择要解码的 X12 平面文件消息，如此示例中所示。

    ![集成帐户连接已创建](media/logic-apps-enterprise-integration-x12-decode/x12decodeimage6.png) 

    例如：

    ![选择要解码的 X12 平面文件消息](media/logic-apps-enterprise-integration-x12-decode/x12decodeimage7.png) 

   > [!NOTE]
   > 消息数组的实际消息内容或有效负载无论是对的还错的都采用 base64 编码。 因此，必须指定用于处理此内容的表达式。
   > 下面是一个用于处理 XML 格式内容的示例，可以在代码视图中输入此示例或在设计器中使用表达式生成器生成。
   > ``` json
   > "content": "@xml(base64ToBinary(item()?['Payload']))"
   > ```
   > ![内容示例](media/logic-apps-enterprise-integration-x12-decode/content-example.png)
   >


## <a name="x12-decode-details"></a>X12 解码详细信息

X12 解码连接器执行以下任务：

* 针对贸易合作伙伴协议验证信封
* 验证 EDI 和特定于合作伙伴的属性
  * EDI 结构验证和扩展架构验证
  * 交换信封结构的验证。
  * 针对控制架构进行的信封架构验证。
  * 针对消息架构进行的事务集数据元素架构验证。
  * 对事务集数据元素执行的 EDI 验证 
* 验证交换、组和事务集控制编号不重复
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
* 生成技术和/或功能确认（如果已配置）。
  * 技术确认作为标头验证的结果生成。 技术确认报告地址接收方进行的交换标头和尾部处理的状态。
  * 功能确认作为正文验证的结果生成。 功能确认报告在处理收到的文档时遇到的每个错误

## <a name="view-the-swagger"></a>查看 Swagger
请参阅 [Swagger 详细信息](/connectors/x12/)。 

## <a name="next-steps"></a>后续步骤
[了解有关 Enterprise Integration Pack 的详细信息](../logic-apps/logic-apps-enterprise-integration-overview.md "了解 Enterprise Integration Pack") 

