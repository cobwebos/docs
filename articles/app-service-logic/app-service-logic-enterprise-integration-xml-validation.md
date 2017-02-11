---
title: "Enterprise Integration Pack 中的 XML 验证概述 | Microsoft Docs"
description: "了解验证在 Enterprise Integration Pack 和逻辑应用中的工作原理"
services: logic-apps
documentationcenter: .net,nodejs,java
author: msftman
manager: erikre
editor: cgronlun
ms.assetid: d700588f-2d8a-4c92-93eb-e1e6e250e760
ms.service: logic-apps
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/08/2016
ms.author: deonhe
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 7a40b20c8845d09547e80eb230c73ef7683eb2b7


---
# <a name="enterprise-integration-with-xml-validation"></a>企业集成与 XML 验证
## <a name="overview"></a>概述
通常在 B2B 方案中，协议的合作伙伴需要先验证它们相互之间交换的消息是否有效，然后才能处理数据。 在 Enterprise Integration Pack 中，可以使用 XML 验证连接器针对预定义的架构验证文档。  

## <a name="how-to-validate-a-document-with-the-xml-validation-connector"></a>如何使用 XML 验证连接器验证文档
1. 创建逻辑应用并[将它链接到包含将用于验证 XML 数据的架构的集成帐户](app-service-logic-enterprise-integration-accounts.md "了解如何将集成帐户链接到逻辑应用")。
2. 向逻辑应用添加“请求 - 当收到 HTTP 请求时”触发器  
   ![](./media/app-service-logic-enterprise-integration-xml/xml-1.png)    
3. 通过首先选择“添加操作”来添加“XML 验证”操作  
4. 在搜索框中输入 xml，以便在所有操作中筛选出要使用的操作 
5. 选择“XML 验证”     
   ![](./media/app-service-logic-enterprise-integration-xml/xml-2.png)   
6. 选择“内容”文本框  
   ![](./media/app-service-logic-enterprise-integration-xml/xml-1-5.png)
7. 选择正文标记作为要验证的内容。   
   ![](./media/app-service-logic-enterprise-integration-xml/xml-3.png)  
8. 选择“架构名称”列表框，然后选择要用于验证上面的输入*内容*的架构     
   ![](./media/app-service-logic-enterprise-integration-xml/xml-4.png) 
9. 保存工作  
   ![](./media/app-service-logic-enterprise-integration-xml/xml-5.png) 

此时，已完成验证连接器设置。 在实际应用程序中，你可能要将验证的数据存储在 LOB 应用程序中，如 SalesForce。 可以轻松地添加操作以将验证的输出发送给 Salesforce。 

现在可以通过向 HTTP 终结点发出请求来测试验证操作。  

## <a name="next-steps"></a>后续步骤
[了解有关 Enterprise Integration Pack 的详细信息](app-service-logic-enterprise-integration-overview.md "了解 Enterprise Integration Pack")   




<!--HONumber=Nov16_HO3-->


