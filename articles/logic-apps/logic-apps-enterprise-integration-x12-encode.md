---
title: "为 X12 消息编码 - Azure 逻辑应用 | Microsoft 文档"
description: "使用 Enterprise Integration Pack 中的 X12 消息编码器为 Azure 逻辑应用验证 EDI 并转换 XML 编码的消息"
services: logic-apps
documentationcenter: .net,nodejs,java
author: padmavc
manager: anneta
editor: 
ms.assetid: a01e9ca9-816b-479e-ab11-4a984f10f62d
ms.service: logic-apps
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/27/2017
ms.author: LADocs; padmavc
ms.openlocfilehash: f7408f240a1b05e0d53716764a9f8d1e19229ebe
ms.sourcegitcommit: be9a42d7b321304d9a33786ed8e2b9b972a5977e
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/19/2018
---
# <a name="encode-x12-messages-for-azure-logic-apps-with-the-enterprise-integration-pack"></a>使用 Enterprise Integration Pack 为 Azure 逻辑应用编码 X12 消息

使用编码 X12 消息连接器，可以验证 EDI 和特定于合作伙伴的属性，将 XML 编码的消息转换为交换中的 EDI 事务集并请求技术确认和/或功能确认。
要使用此连接器，必须将此连接器添加到逻辑应用中的现有触发器。

## <a name="before-you-start"></a>开始之前

需要具有以下各项：

* Azure 帐户；可以创建[免费帐户](https://azure.microsoft.com/free)
* 已定义的、与 Azure 订阅关联的[集成帐户](logic-apps-enterprise-integration-create-integration-account.md)。 必须拥有集成帐户，才能使用编码 X12 消息连接器。
* 已在集成帐户中至少定义了两个[合作伙伴](logic-apps-enterprise-integration-partners.md)
* 已在集成帐户中定义了 [X12 协议](logic-apps-enterprise-integration-x12.md)

## <a name="encode-x12-messages"></a>编码 X12 消息

1. [创建逻辑应用](quickstart-create-first-logic-app-workflow.md)。

2. 编码 X12 消息连接器没有触发器，因此必须添加用于启动逻辑应用的触发器，如请求触发器。 在逻辑应用设计器中，添加触发器，然后将操作添加到逻辑应用。

3.  在搜索框中，输入“x12”作为筛选器。 选择“X12 - 编码为 X12 消息(按协议名称)”，或者选择“X12 - 编码为 X 12 消息(按标识)”。
   
    ![搜索“x12”](./media/logic-apps-enterprise-integration-x12-encode/x12decodeimage1.png) 

3. 如果以前未创建与集成帐户的任何连接，系统会提示现在创建该连接。 为连接命名，并选择要连接的集成帐户。 
   
    ![集成帐户连接](./media/logic-apps-enterprise-integration-x12-encode/x12encodeimage1.png)

    带有星号的属性必填。

    | 属性 | 详细信息 |
    | --- | --- |
    | 连接名称 * |为连接输入任何名称。 |
    | 集成帐户 * |输入集成帐户的名称。 确保集成帐户和逻辑应用位于同一 Azure 位置。 |

5.  完成后，连接详细信息应如此示例所示。 若要完成连接创建，请选择“创建”。

    ![集成帐户连接已创建](./media/logic-apps-enterprise-integration-x12-encode/x12encodeimage2.png)

    连接现已创建。

    ![集成帐户连接详细信息](./media/logic-apps-enterprise-integration-x12-encode/x12encodeimage3.png) 

#### <a name="encode-x12-messages-by-agreement-name"></a>编码 X12 消息(按协议名称)

如果选择了按协议名称编码 X12 消息，请打开“X12 协议的名称”列表，输入或选择现有的 X12 协议。 输入要编码的 XML 消息。

![输入 X12 协议名称和要编码的 XML 消息](./media/logic-apps-enterprise-integration-x12-encode/x12encodeimage4.png)

#### <a name="encode-x12-messages-by-identities"></a>编码 X12 消息(按标识)

如果选择了按标识编码 X12 消息，请根据 X12 协议中的配置输入发送方标识符、发送方限定符、接收方标识符和接收方限定符。 选择要编码的 XML 消息。
   
![提供发送方和接收方的标识，选择要编码的 XML 消息](./media/logic-apps-enterprise-integration-x12-encode/x12encodeimage5.png) 

## <a name="x12-encode-details"></a>X12 编码详细信息

X12 编码连接器执行以下任务：

* 通过匹配发送方和接收方上下文属性进行协议解析。
* 序列化 EDI 交换，将 XML 编码的消息转换成交换中的 EDI 事务集。
* 应用事务集标头和尾部段
* 为每个传出交换生成交换控制编号、组控制编号和事务集控制编号
* 替换有效负载数据中的分隔符
* 验证 EDI 和特定于合作伙伴的属性
  * 针对消息架构进行的事务集数据元素架构验证
  * 对事务集数据元素执行的 EDI 验证。
  * 对事务集数据元素执行的扩展验证
* 请求技术和/或功能确认（如果已配置）。
  * 技术确认作为标头验证的结果生成。 技术确认报告地址接收方进行的交换标头和尾部处理的状态
  * 功能确认作为正文验证的结果生成。 功能确认报告在处理收到的文档时遇到的每个错误

## <a name="view-the-swagger"></a>查看 Swagger
请参阅 [Swagger 详细信息](/connectors/x12/)。 

## <a name="next-steps"></a>后续步骤
[了解有关 Enterprise Integration Pack 的详细信息](logic-apps-enterprise-integration-overview.md "了解 Enterprise Integration Pack") 

