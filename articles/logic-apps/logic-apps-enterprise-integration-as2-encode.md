---
title: "为 AS2 消息编码 - Azure 逻辑应用 | Microsoft 文档"
description: "如何为 Azure 逻辑应用使用 Enterprise Integration Pack 中的 AS2 编码器"
services: logic-apps
documentationcenter: .net,nodejs,java
author: padmavc
manager: anneta
editor: 
ms.assetid: 332fb9e3-576c-4683-bd10-d177a0ebe9a3
ms.service: logic-apps
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/27/2017
ms.author: LADocs; padmavc
ms.openlocfilehash: bccc8f81a3a664e090796ae8a3cbb38c890c0479
ms.sourcegitcommit: be9a42d7b321304d9a33786ed8e2b9b972a5977e
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/19/2018
---
# <a name="encode-as2-messages-for-azure-logic-apps-with-the-enterprise-integration-pack"></a>使用 Enterprise Integration Pack 为 Azure 逻辑应用编码 AS2 消息

若要在传输消息时建立安全性和可靠性，请使用编码 AS2 消息连接器。 此连接器通过邮件处置通知 (MDN) 提供数字签名、解密和确认等功能，这还会实现对不可否认性的支持。

## <a name="before-you-start"></a>开始之前

需要具有以下各项：

* Azure 帐户；可以创建[免费帐户](https://azure.microsoft.com/free)
* 已定义的、与 Azure 订阅关联的[集成帐户](logic-apps-enterprise-integration-create-integration-account.md)。 必须拥有集成帐户，才能使用编码 AS2 消息连接器。
* 已在集成帐户中定义了至少两个[合作伙伴](logic-apps-enterprise-integration-partners.md)
* 已在集成帐户中定义了 [AS2 协议](logic-apps-enterprise-integration-as2.md)

## <a name="encode-as2-messages"></a>为 AS2 消息编码

1. [创建逻辑应用](quickstart-create-first-logic-app-workflow.md)。

2. 编码 AS2 消息连接器没有触发器，因此必须添加用于启动逻辑应用的触发器，如请求触发器。 在逻辑应用设计器中，添加触发器，并将操作添加到逻辑应用。

3.  在搜索框中，输入“AS2”作为筛选器。 选择“AS2 - 编码 AS2 消息”。
   
    ![搜索“AS2”](./media/logic-apps-enterprise-integration-as2-encode/as2decodeimage1.png)

4. 如果以前未创建与集成帐户的任何连接，系统会提示现在创建该连接。 为连接命名，并选择要连接的集成帐户。 
   
    ![创建到集成帐户的连接](./media/logic-apps-enterprise-integration-as2-encode/as2encodeimage1.png)  

    带有星号的属性必填。

    | 属性 | 详细信息 |
    | --- | --- |
    | 连接名称 * |为连接输入任何名称。 |
    | 集成帐户 * |输入集成帐户的名称。 确保集成帐户和逻辑应用位于同一 Azure 位置。 |

5.  完成后，连接详细信息应如此示例所示。 若要完成连接创建，请选择“创建”。
   
    ![集成连接详细信息](./media/logic-apps-enterprise-integration-as2-encode/as2encodeimage2.png)

6. 如此示例中所示，在创建连接后，根据协议中的配置提供 **AS2 发件人标识符**和 **AS2 收件人标识符**的详细信息，并提供**正文**（即消息有效负载）。
   
    ![提供必填字段](./media/logic-apps-enterprise-integration-as2-encode/as2encodeimage3.png)

## <a name="as2-encoder-details"></a>AS2 编码器详细信息

编码 AS2 连接器执行以下任务： 

* 应用 AS2/HTTP 标头
* 对传出消息进行签名（如果已配置）
* 对传出消息进行加密（如果已配置）
* 压缩消息（如果已配置）

## <a name="try-this-sample"></a>尝试此示例

若要尝试部署完全正常运行的逻辑应用和示例 AS2 方案，请参阅 [AS2 逻辑应用模板和方案](https://azure.microsoft.com/documentation/templates/201-logic-app-as2-send-receive/)。

## <a name="view-the-swagger"></a>查看 Swagger
请参阅 [Swagger 详细信息](/connectors/as2/)。 

## <a name="next-steps"></a>后续步骤
[了解有关 Enterprise Integration Pack 的详细信息](logic-apps-enterprise-integration-overview.md "了解 Enterprise Integration Pack") 

