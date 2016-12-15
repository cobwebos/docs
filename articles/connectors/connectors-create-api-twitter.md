---
title: "了解如何在逻辑应用中使用 Twitter 连接器 | Microsoft Docs"
description: "使用 REST API 参数的 Twitter 连接器概述"
services: 
documentationcenter: 
author: msftman
manager: erikre
editor: 
tags: connectors
ms.assetid: 8bce2183-544d-4668-a2dc-9a62c152d9fa
ms.service: multiple
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/18/2016
ms.author: deonhe
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: a9a1bcd75dbea49a4eff8e7eb9113cc47f7ab86f


---
# <a name="get-started-with-the-twitter-connector"></a>Twitter 连接器入门
借助 Twitter 连接器，可以：

* 发布推文和获取推文
* 访问时间线、好友和关注者
* 执行下面所述的任意其他操作和触发器  

若要使用“任何连接器”[](apis-list.md)，首先需要创建逻辑应用。 可通过“立即创建逻辑应用”[](../app-service-logic/app-service-logic-create-a-logic-app.md)开始操作。  

## <a name="connect-to-twitter"></a>连接到 Twitter
需要先创建到任何服务的*连接*，然后逻辑应用才能访问该服务。 [连接](connectors-overview.md)提供逻辑应用和其他服务之间的连接性。  

### <a name="create-a-connection-to-twitter"></a>创建到 Twitter 的连接
> [!INCLUDE [Steps to create a connection to Twitter](../../includes/connectors-create-api-twitter.md)]
> 
> 

## <a name="use-a-twitter-trigger"></a>使用 Twitter 触发器
触发器是用于启动在逻辑应用中定义的工作流的事件。 [了解有关触发器的详细信息](../app-service-logic/app-service-logic-what-are-logic-apps.md#logic-app-concepts)。

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
操作是指在逻辑应用中定义的由工作流执行的操作。 [了解有关操作的详细信息](../app-service-logic/app-service-logic-what-are-logic-apps.md#logic-app-concepts)。  

现已添加触发器，按照以下步骤添加操作，该操作将发布带有该触发器找到的推文内容的新推文。 在本演练中，将仅发布来自具有多于 50 个关注者的用户推文。  

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

## <a name="technical-details"></a>技术详细信息
下面详细介绍了此连接支持的触发器、操作和响应：

## <a name="twitter-triggers"></a>Twitter 触发器
Twitter 连接器具有以下触发器：  

| 触发器 | 说明 |
| --- | --- |
| [发布新推文时](connectors-create-api-twitter.md#when-a-new-tweet-is-posted) |此操作在发布与给定搜索查询匹配的新推文时触发流。 |

## <a name="twitter-actions"></a>Twitter 操作
Twitter 连接器具有以下操作：

| 操作 | 说明 |
| --- | --- |
| [获取用户时间线](connectors-create-api-twitter.md#get-user-timeline) |此操作获取给定用户发布的最新推文列表。 |
| [获取主页时间线](connectors-create-api-twitter.md#get-home-timeline) |此操作获取我和我的关注者发布的最新推文和转推。 |
| [搜索推文](connectors-create-api-twitter.md#search-tweets) |此操作获取与搜索查询匹配的相关推文列表。 |
| [获取关注者](connectors-create-api-twitter.md#get-followers) |此操作获取关注给定用户的用户列表。 |
| [获取我的关注者](connectors-create-api-twitter.md#get-my-followers) |此操作获取正在关注我的用户列表。 |
| [获取关注](connectors-create-api-twitter.md#get-following) |此操作获取给定用户关注的人员列表。 |
| [获取我的关注](connectors-create-api-twitter.md#get-my-following) |此操作获取我正在关注的用户列表。 |
| [获取用户](connectors-create-api-twitter.md#get-user) |此操作获取给定用户的个人资料详细信息，如用户名、说明、关注者计数等。 |
| [发布推文](connectors-create-api-twitter.md#post-a-tweet) |此操作发布新推文。 |

## <a name="action-details"></a>操作详细信息
下面详细介绍了此连接器的操作和触发器及其响应：

### <a name="get-user-timeline"></a>获取用户时间线
此操作获取给定用户发布的最新推文列表。 

| 属性名称 | 显示名称 | 说明 |
| --- | --- | --- |
| userName* |用户名 |用户的 Twitter 句柄 |
| maxResults |最大结果 |要返回的最大推文数 |

* 指示属性是必需的

#### <a name="output-details"></a>输出详细信息
TweetModel：推文对象的表示形式

| 属性名称 | 数据类型 | 说明 |
| --- | --- | --- |
| TweetText |字符串 |推文的文本内容 |
| TweetId |字符串 |推文的 ID |
| CreatedAt |字符串 |发布推文的时间 |
| RetweetCount |integer |推文的转推总次数 |
| TweetedBy |字符串 |发布推文的用户名称 |
| MediaUrls |数组 |与推文一起发布的媒体的 URL |
| TweetLanguageCode |字符串 |推文的语言代码 |
| TweetInReplyToUserId |字符串 |当前推文回复的推文创作者的用户 ID |
| Favorited |布尔值 |指示推文是否标记为收藏 |
| UserMentions |数组 |推文中提到的用户列表 |
| OriginalTweet |未定义 |当前推文所转推自的原始推文 |
| UserDetails |未定义 |发布推文的用户详细信息 |

### <a name="get-home-timeline"></a>获取主页时间线
此操作获取我和我的关注者发布的最新推文和转推。 

| 属性名称 | 显示名称 | 说明 |
| --- | --- | --- |
| maxResults |最大结果 |要返回的最大推文数 |

* 指示属性是必需的

#### <a name="output-details"></a>输出详细信息
TweetModel：推文对象的表示形式

| 属性名称 | 数据类型 | 说明 |
| --- | --- | --- |
| TweetText |字符串 |推文的文本内容 |
| TweetId |字符串 |推文的 ID |
| CreatedAt |字符串 |发布推文的时间 |
| RetweetCount |integer |推文的转推总次数 |
| TweetedBy |字符串 |发布推文的用户名称 |
| MediaUrls |数组 |与推文一起发布的媒体的 URL |
| TweetLanguageCode |字符串 |推文的语言代码 |
| TweetInReplyToUserId |字符串 |当前推文回复的推文创作者的用户 ID |
| Favorited |布尔值 |指示推文是否标记为收藏 |
| UserMentions |数组 |推文中提到的用户列表 |
| OriginalTweet |未定义 |当前推文所转推自的原始推文 |
| UserDetails |未定义 |发布推文的用户详细信息 |

### <a name="search-tweets"></a>搜索推文
此操作获取与搜索查询匹配的相关推文列表。 

| 属性名称 | 显示名称 | 说明 |
| --- | --- | --- |
| searchQuery* |搜索文本 |搜索词，如“欢乐时刻”、#haiku、喜欢或讨厌 |
| maxResults |最大结果 |要返回的最大推文数 |

* 指示属性是必需的

#### <a name="output-details"></a>输出详细信息
TweetModel：推文对象的表示形式

| 属性名称 | 数据类型 | 说明 |
| --- | --- | --- |
| TweetText |字符串 |推文的文本内容 |
| TweetId |字符串 |推文的 ID |
| CreatedAt |字符串 |发布推文的时间 |
| RetweetCount |integer |推文的转推总次数 |
| TweetedBy |字符串 |发布推文的用户名称 |
| MediaUrls |数组 |与推文一起发布的媒体的 URL |
| TweetLanguageCode |字符串 |推文的语言代码 |
| TweetInReplyToUserId |字符串 |当前推文回复的推文创作者的用户 ID |
| Favorited |布尔值 |指示推文是否标记为收藏 |
| UserMentions |数组 |推文中提到的用户列表 |
| OriginalTweet |未定义 |当前推文所转推自的原始推文 |
| UserDetails |未定义 |发布推文的用户详细信息 |

### <a name="get-followers"></a>获取关注者
此操作获取关注给定用户的用户列表。 

| 属性名称 | 显示名称 | 说明 |
| --- | --- | --- |
| userName* |用户名 |用户的 Twitter 句柄 |
| maxResults |最大结果 |要返回的最大用户数 |

* 指示属性是必需的

#### <a name="output-details"></a>输出详细信息
UserDetailsModel：Twitter 用户详细信息

| 属性名称 | 数据类型 | 说明 |
| --- | --- | --- |
| FullName |字符串 |用户的名称 |
| 位置 |字符串 |用户的位置 |
| ID |integer |用户的 Twitter ID |
| UserName |字符串 |用户的屏幕名称 |
| FollowersCount |integer |关注者数量 |
| 说明 |字符串 |用户说明 |
| StatusesCount |integer |用户状态计数 |
| FriendsCount |integer |好友数量 |
| FavouritesCount |integer |用户已收藏的推文数量 |
| ProfileImageUrl |字符串 |个人资料图像的 URL |

### <a name="get-my-followers"></a>获取我的关注者
此操作获取正在关注我的用户列表。 

| 属性名称 | 显示名称 | 说明 |
| --- | --- | --- |
| maxResults |最大结果 |要获取的最大用户数 |

* 指示属性是必需的

#### <a name="output-details"></a>输出详细信息
UserDetailsModel：Twitter 用户详细信息

| 属性名称 | 数据类型 | 说明 |
| --- | --- | --- |
| FullName |字符串 |用户的名称 |
| 位置 |字符串 |用户的位置 |
| ID |integer |用户的 Twitter ID |
| UserName |字符串 |用户的屏幕名称 |
| FollowersCount |integer |关注者数量 |
| 说明 |字符串 |用户说明 |
| StatusesCount |integer |用户状态计数 |
| FriendsCount |integer |好友数量 |
| FavouritesCount |integer |用户已收藏的推文数量 |
| ProfileImageUrl |字符串 |个人资料图像的 URL |

### <a name="get-following"></a>获取关注
此操作获取给定用户关注的人员列表。 

| 属性名称 | 显示名称 | 说明 |
| --- | --- | --- |
| userName* |用户名 |用户的 Twitter 句柄 |
| maxResults |最大结果 |要返回的最大用户数 |

* 指示属性是必需的

#### <a name="output-details"></a>输出详细信息
UserDetailsModel：Twitter 用户详细信息

| 属性名称 | 数据类型 | 说明 |
| --- | --- | --- |
| FullName |字符串 |用户的名称 |
| 位置 |字符串 |用户的位置 |
| ID |integer |用户的 Twitter ID |
| UserName |字符串 |用户的屏幕名称 |
| FollowersCount |integer |关注者数量 |
| 说明 |字符串 |用户说明 |
| StatusesCount |integer |用户状态计数 |
| FriendsCount |integer |好友数量 |
| FavouritesCount |integer |用户已收藏的推文数量 |
| ProfileImageUrl |字符串 |个人资料图像的 URL |

### <a name="get-my-following"></a>获取我的关注
此操作获取我正在关注的用户列表。 

| 属性名称 | 显示名称 | 说明 |
| --- | --- | --- |
| maxResults |最大结果 |要返回的最大用户数 |

* 指示属性是必需的

#### <a name="output-details"></a>输出详细信息
UserDetailsModel：Twitter 用户详细信息

| 属性名称 | 数据类型 | 说明 |
| --- | --- | --- |
| FullName |字符串 |用户的名称 |
| 位置 |字符串 |用户的位置 |
| ID |integer |用户的 Twitter ID |
| UserName |字符串 |用户的屏幕名称 |
| FollowersCount |integer |关注者数量 |
| 说明 |字符串 |用户说明 |
| StatusesCount |integer |用户状态计数 |
| FriendsCount |integer |好友数量 |
| FavouritesCount |integer |用户已收藏的推文数量 |
| ProfileImageUrl |字符串 |个人资料图像的 URL |

### <a name="get-user"></a>获取用户
此操作获取给定用户的个人资料详细信息，如用户名、说明、关注者计数等。 

| 属性名称 | 显示名称 | 说明 |
| --- | --- | --- |
| userName* |用户名 |用户的 Twitter 句柄 |

* 指示属性是必需的

#### <a name="output-details"></a>输出详细信息
UserDetailsModel：Twitter 用户详细信息

| 属性名称 | 数据类型 | 说明 |
| --- | --- | --- |
| FullName |字符串 |用户的名称 |
| 位置 |字符串 |用户的位置 |
| ID |integer |用户的 Twitter ID |
| UserName |字符串 |用户的屏幕名称 |
| FollowersCount |integer |关注者数量 |
| 说明 |字符串 |用户说明 |
| StatusesCount |integer |用户状态计数 |
| FriendsCount |integer |好友数量 |
| FavouritesCount |integer |用户已收藏的推文数量 |
| ProfileImageUrl |字符串 |个人资料图像的 URL |

### <a name="post-a-tweet"></a>发布推文
此操作发布新推文。 

| 属性名称 | 显示名称 | 说明 |
| --- | --- | --- |
| tweetText |推文文本 |要发布的推文 |
| body |媒体 |要发布的媒体 |

* 指示属性是必需的

#### <a name="output-details"></a>输出详细信息
TweetResponseModel：表示已发布推文的模型

| 属性名称 | 数据类型 | 说明 |
| --- | --- | --- |
| TweetId |字符串 |检索到的推文的 ID |

### <a name="when-a-new-tweet-is-posted"></a>发布新推文时
此操作在发布与给定搜索查询匹配的新推文时触发流。 

| 属性名称 | 显示名称 | 说明 |
| --- | --- | --- |
| searchQuery* |搜索文本 |搜索词，如“欢乐时刻”、#haiku、喜欢或讨厌 |

* 指示属性是必需的

#### <a name="output-details"></a>输出详细信息
TriggerBatchResponse[TweetModel]

| 属性名称 | 数据类型 |
| --- | --- |
| value |数组 |

## <a name="http-responses"></a>HTTP 响应
上述操作和触发器可以返回以下一个或多个 HTTP 状态代码： 

| Name | 说明 |
| --- | --- |
| 200 |确定 |
| 202 |已接受 |
| 400 |错误的请求 |
| 401 |未授权 |
| 403 |禁止 |
| 404 |未找到 |
| 500 |内部服务器错误。 发生未知错误。 |
| default |操作失败。 |

## <a name="next-steps"></a>后续步骤
[创建逻辑应用](../app-service-logic/app-service-logic-create-a-logic-app.md)




<!--HONumber=Nov16_HO3-->


