---
title: "了解如何在逻辑应用中使用 Twitter 连接器 | Microsoft Docs"
description: "使用 REST API 参数的 Twitter 连接器概述"
services: 
documentationcenter: 
author: MandiOhlinger
manager: anneta
editor: 
tags: connectors
ms.assetid: 8bce2183-544d-4668-a2dc-9a62c152d9fa
ms.service: multiple
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/18/2016
ms.author: mandia; ladocs
ms.translationtype: Human Translation
ms.sourcegitcommit: c785ad8dbfa427d69501f5f142ef40a2d3530f9e
ms.openlocfilehash: be8163043535833ce45b3d50939a537406cf8152
ms.contentlocale: zh-cn
ms.lasthandoff: 05/26/2017

---
# <a name="get-started-with-the-twitter-connector"></a>Twitter 连接器入门
借助 Twitter 连接器，可以：

* 发布推文和获取推文
* 访问时间线、好友和关注者
* 执行下面所述的任意其他操作和触发器  

若要使用 [任何连接器](apis-list.md) ，首先需要创建逻辑应用。 可通过 [立即创建逻辑应用](../logic-apps/logic-apps-create-a-logic-app.md) 开始操作。  

## <a name="connect-to-twitter"></a>连接到 Twitter
在逻辑应用访问任何服务之前，必须先创建到该服务的*连接*。 [连接](connectors-overview.md)提供逻辑应用和其他服务之间的连接性。  

### <a name="create-a-connection-to-twitter"></a>创建到 Twitter 的连接
> [!INCLUDE [Steps to create a connection to Twitter](../../includes/connectors-create-api-twitter.md)]
> 
> 

## <a name="use-a-twitter-trigger"></a>使用 Twitter 触发器
触发器是用于启动在逻辑应用中定义的工作流的事件。 [了解有关触发器的详细信息](../logic-apps/logic-apps-what-are-logic-apps.md#logic-app-concepts)。

在此示例中，我将演示如何使用“发布新推文时”触发器搜索 #Seattle，如果找到了 #Seattle，则使用推文中的文本更新 Dropbox 中的文件。 在企业示例中，可搜索公司名称并使用推文中的文本更新 SQL 数据库。

1. 在逻辑应用设计器上的搜索框中输入“twitter”，然后选择“Twitter - 发布新推文时”触发器   
   ![Twitter 触发器图 1](./media/connectors-create-api-twitter/trigger-1.png)  
2. 在“搜索文本”控件中输入“#Seattle”  
   ![Twitter 触发器图 2](./media/connectors-create-api-twitter/trigger-2.png) 

此时，已使用将开始运行工作流中的其他触发器和操作的触发器配置了逻辑应用。 

> [!NOTE]
> 若要使逻辑应用正常工作，必须包含至少一个触发器和一个操作。 按照下一部分中的步骤添加操作。  
> 
> 

## <a name="add-a-condition"></a>添加条件
由于我们仅对具有多于 50 个关注者的用户发出的推文感兴趣，因此必须先向逻辑应用添加确认关注者数量的条件。  

1. 选择“+ 新步骤”，添加要在新推文中找到 #Seattle 时采取的操作  
   ![Twitter 操作图 1](../../includes/media/connectors-create-api-twitter/action-1.png)  
2. 选择**添加条件**链接。  
   ![Twitter 条件图 1](../../includes/media/connectors-create-api-twitter/condition-1.png)   
   这将打开“条件”控件，可在该控件中检查*等于*、*小于*、*大于*、*包含*等条件。  
   ![Twitter 条件图 2](../../includes/media/connectors-create-api-twitter/condition-2.png)   
3. 选择“选择值”控件。  
   在此控件中，可选择来自任何之前操作或触发器的一个或多个属性作为评价其条件为 true 还是 false 的值。
   ![Twitter 条件图 3](../../includes/media/connectors-create-api-twitter/condition-3.png)   
4. 选择“...”展开属性列表，以便看到所有可用属性。        
   ![Twitter 条件图 4](../../includes/media/connectors-create-api-twitter/condition-4.png)   
5. 选择“关注者计数”属性。    
   ![Twitter 条件图 5](../../includes/media/connectors-create-api-twitter/condition-5.png)   
6. 请注意，关注者计数属性现在在值控件中。    
   ![Twitter 条件图 6](../../includes/media/connectors-create-api-twitter/condition-6.png)   
7. 从运算符列表中选择“大于”。    
   ![Twitter 条件图 7](../../includes/media/connectors-create-api-twitter/condition-7.png)   
8. 输入 50 作为*大于*运算符的操作数。  
   现在添加条件。 使用上述菜单中的**保存**链接保存工作。    
   ![Twitter 条件图 8](../../includes/media/connectors-create-api-twitter/condition-8.png)   

## <a name="use-a-twitter-action"></a>使用 Twitter 操作
操作是指在逻辑应用中定义的工作流所执行的操作。 [了解有关操作的详细信息](../logic-apps/logic-apps-what-are-logic-apps.md#logic-app-concepts)。  

现已添加触发器，按照以下步骤添加操作，该操作将发布带有该触发器找到的推文内容的新推文。 在本演练中，将仅发布关注者超过 50 个的用户的推文。  

在下一步中，将添加一个 Twitter 操作，该操作将使用具有多于 50 个关注者的用户所发布的每条推文的某些属性发布推文。  

1. 选择“添加操作”。 这将打开搜索控件，可在该控件中搜索其他操作和触发器。  
   ![Twitter 条件图 9](../../includes/media/connectors-create-api-twitter/condition-9.png)   
2. 在搜索框中输入“twitter”，然后选择“Twitter - 发布推文”操作。 这打开“发布推文”控件，将在该控件中输入要发布的推文的所有详细信息。      
   ![Twitter 操作图 1-5](../../includes/media/connectors-create-api-twitter/action-1-5.png)   
3. 选择“推文文本”控件。 来自逻辑应用中的之前操作和触发器的所有输出现在都可见。 可选择其中任意一个，并将它们用作新推文的推文文本的一部分。     
   ![Twitter 操作图 2](../../includes/media/connectors-create-api-twitter/action-2.png)   
4. 选择“用户名”   
5. 在推文文本控件中输入“说:”。 紧跟在用户名后输入此内容。  
6. 选择“推文文本”。       
   ![Twitter 操作图 3](../../includes/media/connectors-create-api-twitter/action-3.png)   
7. 保存工作，然后发送带有 #Seattle 井号标签的推文以激活工作流。  


## <a name="connector-specific-details"></a>特定于连接器的详细信息

在[连接器详细信息](/connectors/twitterconnector/)中查看在 Swagger 中定义的触发器和操作，并查看限制。 

## <a name="next-steps"></a>后续步骤
[创建逻辑应用](../logic-apps/logic-apps-create-a-logic-app.md)


