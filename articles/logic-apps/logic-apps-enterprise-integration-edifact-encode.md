---
title: "为 EDIFACT 消息编码 - Azure 逻辑应用 | Microsoft 文档"
description: "使用 Enterprise Integration Pack 中的 EDIFACT 消息编码器为 Azure 逻辑应用验证 EDI 并生成 XML"
services: logic-apps
documentationcenter: .net,nodejs,java
author: padmavc
manager: anneta
editor: 
ms.assetid: 974ac339-d97a-4715-bc92-62d02281e900
ms.service: logic-apps
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/27/2017
ms.author: LADocs; padmavc
ms.translationtype: Human Translation
ms.sourcegitcommit: c308183ffe6a01f4d4bf6f5817945629cbcedc92
ms.openlocfilehash: e83488f4ec420c2af40d373239ba6989387da3cb
ms.contentlocale: zh-cn
ms.lasthandoff: 05/17/2017


---
# <a name="encode-edifact-messages-for-azure-logic-apps-with-the-enterprise-integration-pack"></a>使用 Enterprise Integration Pack 为 Azure 逻辑应用编码 EDIFACT 消息

使用编码 EDIFACT 消息连接器，可以验证 EDI 和特定于合作伙伴的属性，为每个事务集生成 XML 文档，并请求技术确认和/或功能确认。
若要使用此连接器，必须将此连接器添加到你的逻辑应用中的现有触发器。

## <a name="before-you-start"></a>开始之前

需要具有以下各项：

* Azure 帐户；可以创建[免费帐户](https://azure.microsoft.com/free)
* 已定义的、与你的 Azure 订阅关联的[集成帐户](logic-apps-enterprise-integration-create-integration-account.md)。 必须拥有集成帐户，才能使用编码 EDIFACT 消息连接器。 
* 已在集成帐户中定义了至少两个[合作伙伴](logic-apps-enterprise-integration-partners.md)
* 已在集成帐户中定义了 [EDIFACT 协议](logic-apps-enterprise-integration-edifact.md)

## <a name="encode-edifact-messages"></a>为 EDIFACT 消息编码

1. [创建逻辑应用](logic-apps-create-a-logic-app.md)。

2. 编码 EDIFACT 消息连接器没有触发器，因此必须添加用于启动逻辑应用的触发器，如请求触发器。 在逻辑应用设计器中，添加一个触发器，然后向逻辑应用中添加一个操作。

3.    在搜索框中，输入“EDIFACT”作为筛选器。 选择“编码 EDIFACT 消息(按协议名称)”或“编码为 EDIFACT 消息(按标识)”。
   
    ![搜索 EDIFACT](media/logic-apps-enterprise-integration-edifact-encode/edifactdecodeimage1.png)  

3. 如果以前未创建与集成帐户的任何连接，系统会提示你现在创建该连接。 为你的连接命名，并选择要连接的集成帐户。

    ![创建集成帐户连接](media/logic-apps-enterprise-integration-edifact-encode/edifactencodeimage1.png)  

    带有星号的属性必填。

    | 属性 | 详细信息 |
    | --- | --- |
    | 连接名称 * |为连接输入任何名称。 |
    | 集成帐户 * |输入集成帐户的名称。 确保集成帐户和逻辑应用位于同一 Azure 位置。 |

5.    完成后，连接详细信息应如此示例所示。 若要完成连接创建，请选择“创建”。

    ![集成帐户连接详细信息](media/logic-apps-enterprise-integration-edifact-encode/edifactencodeimage2.png)

    你的连接现已创建。

    ![集成帐户连接已创建](media/logic-apps-enterprise-integration-edifact-encode/edifactencodeimage4.png)

#### <a name="encode-edifact-message-by-agreement-name"></a>编码 EDIFACT 消息(按协议名称)

如果选择了按协议名称编码 EDIFACT 消息，请打开“EDIFACT 协议的名称”列表，输入或选择现有的 EDIFACT 协议名称。 输入要编码的 XML 消息。

![输入 EDIFACT 协议名称和要编码的 XML 消息](media/logic-apps-enterprise-integration-edifact-encode/edifactencodeimage6.png)

#### <a name="encode-edifact-message-by-identities"></a>编码 EDIFACT 消息(按标识)

如果选择了按标识编码 EDIFACT 消息，请根据你的 EDIFACT 协议中的配置输入发送方标识符、发送方限定符、接收方标识符和接收方限定符。 选择要编码的 XML 消息。

![提供发送方和接收方的标识，选择要编码的 XML 消息](media/logic-apps-enterprise-integration-edifact-encode/edifactencodeimage7.png)

## <a name="edifact-encode-details"></a>EDIFACT 编码详细信息

编码 EDIFACT 连接器执行以下任务： 

* 通过将发送方限定符和标识符与接收方限定符和标识符进行匹配来解析协议
* 序列化 EDI 交换，将 XML 编码的消息转换成交换中的 EDI 事务集。
* 应用事务集标头和尾部段
* 为每个传出交换生成交换控制编号、组控制编号和事务集控制编号
* 替换有效负载数据中的分隔符
* 验证 EDI 和特定于合作伙伴的属性
  * 针对消息架构进行的事务集数据元素架构验证。
  * 对事务集数据元素执行的 EDI 验证。
  * 对事务集数据元素执行的扩展验证
* 为每个事务集生成 XML 文档。
* 请求技术和/或功能确认（如果已配置）。
  * 作为技术确认，CONTRL 消息指示交换的接收。
  * 作为功能确认，CONTRL 消息指示收到的交换、组或消息的接受或拒绝（具有错误或不支持的功能的列表）

## <a name="view-swagger-file"></a>查看 Swagger 文件
若要查看 EDIFACT 连接器的 Swagger 详细信息，请参阅 [EDIFACT](/connectors/edifact/)。

## <a name="next-steps"></a>后续步骤
[了解有关 Enterprise Integration Pack 的详细信息](logic-apps-enterprise-integration-overview.md "了解 Enterprise Integration Pack") 


