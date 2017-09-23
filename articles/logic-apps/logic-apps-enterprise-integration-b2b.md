---
title: "创建 B2B 解决方案 - Azure 逻辑应用 | Microsoft 文档"
description: "使用 Enterprise Integration Pack 中的 B2B 功能在逻辑应用中接收数据"
services: logic-apps
documentationcenter: .net,nodejs,java
author: msftman
manager: anneta
editor: cgronlun
ms.assetid: 20fc3722-6f8b-402f-b391-b84e9df6fcff
ms.service: logic-apps
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/08/2016
ms.author: LADocs; padmavc
ms.translationtype: Human Translation
ms.sourcegitcommit: a30a90682948b657fb31dd14101172282988cbf0
ms.openlocfilehash: 0625787ddcbc0091e70b111f687e25929720ad15
ms.contentlocale: zh-cn
ms.lasthandoff: 05/25/2017

---
# <a name="receive-data-in-logic-apps-with-the-b2b-features-in-the-enterprise-integration-pack"></a>使用 Enterprise Integration Pack 中的 B2B 功能在逻辑应用中接收数据

创建具有合作伙伴和协议的集成帐户后，便可以使用 [Enterprise Integration Pack](logic-apps-enterprise-integration-overview.md) 为逻辑应用创建企业到企业 (B2B) 工作流。

## <a name="prerequisites"></a>先决条件

若要使用 AS2 和 X12 操作，必须具有企业集成帐户。 了解[如何创建企业集成帐户](../logic-apps/logic-apps-enterprise-integration-accounts.md)。

## <a name="create-a-logic-app-with-b2b-connectors"></a>创建使用 B2B 连接器的逻辑应用

按照以下步骤创建一个使用 AS2 和 X12 操作从贸易合作伙伴接收数据的 B2B 逻辑应用：

1. 创建一个逻辑应用，然后[将应用链接到集成帐户](../logic-apps/logic-apps-enterprise-integration-accounts.md)。

2. 向逻辑应用添加“请求 - 当收到 HTTP 请求时”触发器。

    ![](./media/logic-apps-enterprise-integration-b2b/flatfile-1.png)

3. 若要添加“解码 AS2”操作，请选择“添加操作”。

    ![](./media/logic-apps-enterprise-integration-b2b/transform-2.png)

4. 若要在所有操作中筛选出要使用的操作，请在搜索框中输入 **as2** 一词。

    ![](./media/logic-apps-enterprise-integration-b2b/b2b-5.png)

5. 选择“AS2 - 解码 AS2 消息”操作。

    ![](./media/logic-apps-enterprise-integration-b2b/b2b-6.png)

6. 添加要用作输入的**正文**。 在此示例中，选择触发逻辑应用的 HTTP 请求的正文。 或者在“标头”字段中输入用于输入标头的表达式：

    @triggerOutputs()['headers']

7. 为 AS2 添加所需的**标头**，这些标头可在 HTTP 请求标头中找到。 在此示例中，选择触发逻辑应用的 HTTP 请求的标头。

8. 现在添加“解码 X12 消息”操作。 选择“添加操作”。

    ![](./media/logic-apps-enterprise-integration-b2b/b2b-9.png)

9. 若要在所有操作中筛选出要使用的操作，请在搜索框中输入 **x12** 一词。

    ![](./media/logic-apps-enterprise-integration-b2b/b2b-10.png)

10. 选择“X12 - 解码 X12 消息”操作。

    ![](./media/logic-apps-enterprise-integration-b2b/b2b-as2message.png)

11. 现在，必须指定此操作的输入。 此输入是前面的 AS2 操作的输出。

    实际的消息内容在 JSON 对象中并且经过 base64 编码，因此你必须指定一个表达式作为输入。 
    在“要解码的 X12 平面文件消息”输入字段中输入以下表达式：
    
    @base64ToString(body('Decode_AS2_message')?['AS2Message']?['Content'])

    现在，添加相应步骤解码从贸易合作伙伴收到的 X12 数据，并在 JSON 对象中输出项目。 
    若要通知合作伙伴已收到数据，可以在 HTTP 响应操作中发送回包含 AS2 消息处置通知 (MDN) 的响应。

12. 若要添加“响应”操作，请选择“添加操作”。

    ![](./media/logic-apps-enterprise-integration-b2b/b2b-14.png)

13. 若要在所有操作中筛选出要使用的操作，请在搜索框中输入“响应”一词。

    ![](./media/logic-apps-enterprise-integration-b2b/b2b-15.png)

14. 选择“响应”操作。

    ![](./media/logic-apps-enterprise-integration-b2b/b2b-16.png)

15. 若要从“解码 X12 消息”操作的输出访问 MDN，请使用以下表达式设置响应“正文”字段：

    @base64ToString(body('Decode_AS2_message')?['OutgoingMdn']?['Content'])

    ![](./media/logic-apps-enterprise-integration-b2b/b2b-17.png)  

16. 保存工作。

    ![](./media/logic-apps-enterprise-integration-b2b/transform-5.png)  

现在已完成设置 B2B 逻辑应用。 在实际应用程序中，你可能要将解码的 X12 数据存储在业务线 (LOB) 应用或数据存储中。 若要连接自己的 LOB 应用并在逻辑应用中使用这些 API，可以添加其他操作或编写自定义 API。

## <a name="features-and-use-cases"></a>功能和用例

* 使用 AS2 和 X12 解码和编码操作，可以在逻辑应用中通过行业标准协议在贸易合作伙伴之间交换数据。
* 若要与贸易合作伙伴交换数据，可以使用 AS2 和/或 X12。
* B2B 操作可帮助你轻松地在集成帐户中创建合作伙伴和协议并在逻辑应用中使用它们。
* 使用其他操作扩展逻辑应用时，可以在其他应用和服务（如 SalesForce）之间发送和接收数据。

## <a name="learn-more"></a>了解详细信息
[了解有关 Enterprise Integration Pack 的详细信息](logic-apps-enterprise-integration-overview.md)

