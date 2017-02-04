---
title: "Azure 逻辑应用集成帐户元数据 |Microsoft Docs"
description: "集成帐户元数据概述"
author: padmavc
manager: anneta
editor: 
services: logic-apps
documentationcenter: 
ms.assetid: bb7d9432-b697-44db-aa88-bd16ddfad23f
ms.service: logic-apps
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/21/2016
ms.author: padmavc
translationtype: Human Translation
ms.sourcegitcommit: ba109fdc2a97e99f6216aa038261e6ccc5e356d9
ms.openlocfilehash: 68a1b8515bbcebbe7ce4d4224a4015f18af5040b


---
# <a name="azure-logic-apps-integration-account-metadata"></a>Azure 逻辑应用集成帐户元数据 

## <a name="overview"></a>概述

添加到集成帐户的合作伙伴、协议、架构、映射使用键值对存储元数据。 你可定义在运行时可以检索的自定义元数据。  目前该项目不能在 UI 中创建元数据；可使用 REST API 创建。  合作伙伴、协议和构架可以**编辑为 JSON** 并允许键入元数据信息。  在逻辑应用中，**集成帐户项目查找**有助于检索元数据信息。

## <a name="how-to-store-metadata"></a>如何存储元数据 

1. 创建[集成帐户](logic-apps-enterprise-integration-create-integration-account.md)   

2. 在集成帐户中添加[合作伙伴](logic-apps-enterprise-integration-partners.md#how-to-create-a-partner)或[协议](logic-apps-enterprise-integration-agreements.md#how-to-create-agreements)，或是[架构](logic-apps-enterprise-integration-schemas.md)

3. 选择一个合作伙伴或协议，或是架构。 选择“编辑为 JSON”并输入元数据详细信息    
![输入元数据](media/logic-apps-enterprise-integration-metadata/image1.png)  

## <a name="call-integration-account-artifact-lookup-from-a-logic-app"></a>从逻辑应用调用**集成帐户项目查找**

1. 创建[逻辑应用](logic-apps-create-a-logic-app.md)

2. 通过集成帐户[链接](logic-apps-enterprise-integration-create-integration-account.md#how-to-link-an-integration-account-to-a-logic-app)逻辑应用    

3. 创建触发器，例如搜索**集成帐户项目查找**前使用*请求*或 *HTTP*。  搜索**集成**以查找**集成帐户项目查找** 
![搜索查找](media/logic-apps-enterprise-integration-metadata/image2.png) 

3. 选择“集成帐户项目查找”  

4. 选择“项目类型”并提供**项目名称**  
![搜索查找](media/logic-apps-enterprise-integration-metadata/image3.png)

## <a name="an-example-to-retrieve-partner-metadata"></a>检索合作伙伴元数据的示例 

1. 合作伙伴元数据具有路由 URL 详细信息    
![搜索查找](media/logic-apps-enterprise-integration-metadata/image6.png)

2. 在逻辑应用中，配置**集成帐户项目查找**和 **HTTP**   
![搜索查找](media/logic-apps-enterprise-integration-metadata/image4.png)

3. 若要检索 URI，代码视图应如下所示    
![搜索查找](media/logic-apps-enterprise-integration-metadata/image5.png)


## <a name="next-steps"></a>后续步骤
* [了解有关协议的详细信息](logic-apps-enterprise-integration-agreements.md "了解企业集成协议")  


<!--HONumber=Jan17_HO3-->


