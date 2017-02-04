---
title: GitHub | Microsoft Docs
description: "使用 Azure App Service 创建逻辑应用。 GitHub 是用于托管服务的基于 Web 的 Git 存储库。 它提供 Git 的所有已分配的版本控制和源代码管理 (SCM) 功能以及添加其自己的功能。"
services: logic-apps
documentationcenter: .net,nodejs,java
author: msftman
manager: erikre
editor: 
tags: connectors
ms.assetid: 8f873e6c-f4c0-4c2e-a5bd-2e953efe5e2b
ms.service: logic-apps
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: integration
ms.date: 08/18/2016
ms.author: deonhe
translationtype: Human Translation
ms.sourcegitcommit: b92f954680603891ced503a1134791312b5214f0
ms.openlocfilehash: 6f336db8c8719b99420b353dca82e36a0d837769


---
# <a name="get-started-with-the-github-connector"></a>GitHub 连接器入门
GitHub 是用于托管服务的基于 Web 的 Git 存储库。 它提供 Git 的所有已分配的版本控制和源代码管理 (SCM) 功能以及添加其自己的功能。

> [!NOTE]
> 此文章版本适用于 2015 年 8 月 1 日预览版架构的逻辑应用。 
> 
> 

若要立即开始创建逻辑应用，请参阅[创建逻辑应用](../logic-apps/logic-apps-create-a-logic-app.md)。

## <a name="triggers-and-actions"></a>触发器和操作
GitHub 连接器可用作操作；它具有触发器。 所有连接器都支持采用 JSON 和 XML 格式的数据。 

 GitHub 连接器具有以下可用操作和/或触发器：

### <a name="github-actions"></a>GitHub 操作
可执行以下操作：

| 操作 | 说明 |
| --- | --- |
| [CreateIssue](connectors-create-api-github.md#createissue) |创建问题 |

### <a name="github-triggers"></a>GitHub 触发器
可侦听以下事件：

| 触发器 | 说明 |
| --- | --- |
| 打开问题时 |打开问题 |
| 关闭问题时 |关闭问题 |
| 分配问题时 |分配问题 |

## <a name="create-a-connection-to-github"></a>创建到 GitHub 的连接
若要使用 GitHub 创建逻辑应用，必须先创建**连接**，然后提供以下属性的详细信息： 

| 属性 | 必选 | 说明 |
| --- | --- | --- |
| 令牌 |是 |提供 GitHub 凭据 |

创建连接后，可使用它执行操作并侦听触发器，如本文所述。 

> [!INCLUDE [Steps to create a connection to GitHub](../../includes/connectors-create-api-github.md)]
> 
> [!TIP]
> 可在其他逻辑应用中使用此连接。
> 
> 

## <a name="reference-for-github"></a>GitHub 的参考
适用于版本：1.0

## <a name="createissue"></a>CreateIssue
创建问题：创建问题 

```POST: /repos/{repositoryOwner}/{repositoryName}/issues``` 

| Name | 数据类型 | 必选 | 所在位置 | 默认值 | 说明 |
| --- | --- | --- | --- | --- | --- |
| repositoryOwner |字符串 |是 |路径 |无 |存储库所有者 |
| repositoryName |字符串 |是 |路径 |无 |存储库名称 |
| issueBasicDetails | |是 |body |无 |问题详细信息 |

#### <a name="response"></a>响应
| Name | 说明 |
| --- | --- |
| 200 |确定 |
| 400 |错误的请求 |
| 401 |未授权 |
| 403 |禁止 |
| 404 |未找到 |
| 500 |内部服务器错误。 发生未知错误 |
| default |操作失败。 |

## <a name="issueopened"></a>IssueOpened
打开问题时：打开问题 

```GET: /trigger/issueOpened``` 

此调用没有任何参数

#### <a name="response"></a>响应
| Name | 说明 |
| --- | --- |
| 200 |确定 |
| 400 |错误的请求 |
| 401 |未授权 |
| 403 |禁止 |
| 404 |未找到 |
| 500 |内部服务器错误。 发生未知错误 |
| default |操作失败。 |

## <a name="issueclosed"></a>IssueClosed
关闭问题时：关闭问题 

```GET: /trigger/issueClosed``` 

此调用没有任何参数

#### <a name="response"></a>响应
| Name | 说明 |
| --- | --- |
| 200 |确定 |
| 400 |错误的请求 |
| 401 |未授权 |
| 403 |禁止 |
| 404 |未找到 |
| 500 |内部服务器错误。 发生未知错误 |
| default |操作失败。 |

## <a name="issueassigned"></a>IssueAssigned
分配问题时：分配问题 

```GET: /trigger/issueAssigned``` 

此调用没有任何参数

#### <a name="response"></a>响应
| Name | 说明 |
| --- | --- |
| 200 |确定 |
| 400 |错误的请求 |
| 401 |未授权 |
| 403 |禁止 |
| 404 |未找到 |
| 500 |内部服务器错误。 发生未知错误 |
| default |操作失败。 |

## <a name="object-definitions"></a>对象定义
### <a name="issuebasicdetailsmodel"></a>IssueBasicDetailsModel
| 属性名称 | 数据类型 | 必选 |
| --- | --- | --- |
| title |字符串 |是 |
| body |字符串 |是 |
| assignee |字符串 |是 |

### <a name="issuedetailsmodel"></a>IssueDetailsModel
| 属性名称 | 数据类型 | 必选 |
| --- | --- | --- |
| title |字符串 |是 |
| body |字符串 |是 |
| assignee |字符串 |是 |
| 数字 |字符串 |否 |
| state |字符串 |否 |
| created_at |字符串 |否 |
| repository_url |字符串 |否 |

## <a name="next-steps"></a>后续步骤
[创建逻辑应用](../logic-apps/logic-apps-create-a-logic-app.md)




<!--HONumber=Jan17_HO3-->


