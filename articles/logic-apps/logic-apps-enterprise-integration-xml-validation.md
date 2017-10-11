---
title: "验证 XML - Azure 逻辑应用 | Microsoft 文档"
description: "通过 Enterprise Integration Pack 使用 Azure 逻辑应用和 B2B 方案的架构验证 XML"
services: logic-apps
documentationcenter: .net,nodejs,java
author: msftman
manager: anneta
editor: cgronlun
ms.assetid: d700588f-2d8a-4c92-93eb-e1e6e250e760
ms.service: logic-apps
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/08/2016
ms.author: LADocs; padmavc
ms.openlocfilehash: 8558efffa354cc4bb93820c837077ee997924c95
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/11/2017
---
# <a name="validate-xml-for-enterprise-integration"></a>验证企业集成的 XML

通常在 B2B 方案中，协议中的合作伙伴必须确保他们交换的消息有效，才能开始处理数据。 可以使用 Enterprise Integration Pack 中的 XML 验证连接器针对预定义的架构验证文档。

## <a name="validate-a-document-with-the-xml-validation-connector"></a>使用 XML 验证连接器验证文档

1. 创建逻辑应用并[将应用链接到包含用于验证 XML 数据的架构的集成帐户](../logic-apps/logic-apps-enterprise-integration-accounts.md "了解如何将集成帐户链接到逻辑应用")。

2. 向逻辑应用添加“请求 - 当收到 HTTP 请求时”触发器。

    ![](./media/logic-apps-enterprise-integration-xml/xml-1.png)

3. 若要添加“XML 验证”操作，请选择“添加操作”。

4. 若要在所有操作中筛选出所需的操作，请在搜索框中输入“xml”。 选择“XML 验证”。

    ![](./media/logic-apps-enterprise-integration-xml/xml-2.png)

5. 若要指定要验证的 XML 内容，请选择“内容”。

    ![](./media/logic-apps-enterprise-integration-xml/xml-1-5.png)

6. 选择正文标记作为要验证的内容。

    ![](./media/logic-apps-enterprise-integration-xml/xml-3.png)

7. 若要指定要用于验证以前的*内容*输入的架构，请选择**架构名称**。

    ![](./media/logic-apps-enterprise-integration-xml/xml-4.png)

8. 保存工作  

    ![](./media/logic-apps-enterprise-integration-xml/xml-5.png)

现在已完成设置验证连接器。 在实际应用程序中，你可能想要将已验证的数据存储在业务线 (LOB) 应用（如 SalesForce）中。 要将已验证的输出发送到 Salesforce，请添加操作。

若要测试验证操作，请向 HTTP 终结点发出请求。

## <a name="next-steps"></a>后续步骤
[了解有关 Enterprise Integration Pack 的详细信息](../logic-apps/logic-apps-enterprise-integration-overview.md "了解 Enterprise Integration Pack")   

