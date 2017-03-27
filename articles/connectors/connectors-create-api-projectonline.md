---
title: ProjectOnline | Microsoft Docs
description: "使用 Azure App Service 创建逻辑应用。 Project Online 是用于项目组合管理 (PPM) 和 Microsoft 日常工作的灵活联机解决方案。 通过 Office 365 交付的 Project Online 允许组织快速开始使用强大的项目管理功能，以便在几乎所有设备上随时随地规划、管理项目和项目组合投资，并设置优先顺序。"
services: logic-apps
documentationcenter: .net,nodejs,java
author: msftman
manager: erikre
editor: 
tags: connectors
ms.assetid: 40ce621e-4925-4653-93bb-71ab9abcbdf1
ms.service: logic-apps
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: integration
ms.date: 08/18/2016
ms.author: deonhe
translationtype: Human Translation
ms.sourcegitcommit: b92f954680603891ced503a1134791312b5214f0
ms.openlocfilehash: 77e106d9170fc49d66d322bd9c92bf0e14869259
ms.lasthandoff: 01/20/2017


---
# <a name="get-started-with-the-projectonline-connector"></a>ProjectOnline 连接器入门
Project Online 是用于项目组合管理 (PPM) 和 Microsoft 日常工作的灵活联机解决方案。 通过 Office 365 交付的 Project Online 允许组织快速开始使用强大的项目管理功能，以便在几乎所有设备上随时随地规划、管理项目和项目组合投资，并设置优先顺序。

> [!NOTE]
> 此文章版本适用于 2015 年 8 月 1 日预览版架构的逻辑应用。 
> 
> 

若要立即开始创建逻辑应用，请参阅[创建逻辑应用](../logic-apps/logic-apps-create-a-logic-app.md)。

## <a name="triggers-and-actions"></a>触发器和操作
ProjectOnline 连接器可用作操作；它具有触发器。 所有连接器都支持采用 JSON 和 XML 格式的数据。 

 ProjectOnline 连接器具有以下可用操作和/或触发器：

### <a name="projectonline-actions"></a>ProjectOnline 操作
可执行以下操作：

| 操作 | 说明 |
| --- | --- |
| [ListProjects](connectors-create-api-projectonline.md#listprojects) |在项目联机站点上列出项目 |
| [CreateProject](connectors-create-api-projectonline.md#createproject) |在项目联机站点上新建项目 |
| [CreateTask](connectors-create-api-projectonline.md#createtask) |在你的项目中新建任务 |
| [CreateResource](connectors-create-api-projectonline.md#createresource) |在项目联机站点上创建企业资源 |
| [ListTasks](connectors-create-api-projectonline.md#listtasks) |在项目中列出已发布任务 |
| [CheckoutProject](connectors-create-api-projectonline.md#checkoutproject) |在你的站点中签出项目 |
| [PublishProject](connectors-create-api-projectonline.md#publishproject) |在你的站点中签入并发布现有项目 |

### <a name="projectonline-triggers"></a>ProjectOnline 触发器
可侦听以下事件：

| 触发器 | 说明 |
| --- | --- |
| 新建项目时 |在新建项目时，触发流 |
| 新建资源时 |在新建资源时，触发流 |
| 新建任务时 |在新建任务时，触发流 |

## <a name="create-a-connection-to-projectonline"></a>创建到 ProjectOnline 的连接
若要使用 ProjectOnline 创建逻辑应用，必须先创建**连接**，然后提供以下属性的详细信息： 

| 属性 | 必选 | 说明 |
| --- | --- | --- |
| 令牌 |是 |提供 ProjectOnline 凭据 |

> [!INCLUDE [Steps to create a connection to ProjectOnline](../../includes/connectors-create-api-projectonline.md)]
> 
> [!TIP]
> 可在其他逻辑应用中使用此连接。
> 
> 

## <a name="reference-for-projectonline"></a>ProjectOnline 的参考
适用于版本：1.0

## <a name="onnewproject"></a>OnNewProject
新建项目时：在新建项目时，触发流 

```GET: /trigger/_api/ProjectData/Projects``` 

| Name | 数据类型 | 必选 | 所在位置 | 默认值 | 说明 |
| --- | --- | --- | --- | --- | --- |
| siteUrl |字符串 |是 |查询 |无 |项目站点的根站点 URL（示例：https://sampletenant.sharepoint.com/teams/sampleteam）。 |

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

## <a name="onnewresource"></a>OnNewResource
新建资源时：在新建资源时，触发流 

```GET: /trigger/_api/ProjectData/Resources``` 

| Name | 数据类型 | 必选 | 所在位置 | 默认值 | 说明 |
| --- | --- | --- | --- | --- | --- |
| siteUrl |字符串 |是 |查询 |无 |项目站点的根站点 URL（示例：https://sampletenant.sharepoint.com/teams/sampleteam）。 |

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

## <a name="onnewtask"></a>OnNewTask
新建任务时：在新建任务时，触发流 

```GET: /trigger/_api/ProjectData/Tasks``` 

| Name | 数据类型 | 必选 | 所在位置 | 默认值 | 说明 |
| --- | --- | --- | --- | --- | --- |
| siteUrl |字符串 |是 |查询 |无 |项目站点的根站点 URL（示例：https://sampletenant.sharepoint.com/teams/sampleteam）。 |

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

## <a name="listprojects"></a>ListProjects
列出项目：在项目联机站点上列出项目 

```GET: /_api/ProjectServer/Projects``` 

| Name | 数据类型 | 必选 | 所在位置 | 默认值 | 说明 |
| --- | --- | --- | --- | --- | --- |
| siteUrl |字符串 |是 |查询 |无 |项目站点的根站点 URL（示例：https://sampletenant.sharepoint.com/teams/sampleteam）。 |

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

## <a name="createproject"></a>CreateProject
新建项目：在项目联机站点上新建项目 

```POST: /_api/ProjectServer/Projects``` 

| Name | 数据类型 | 必选 | 所在位置 | 默认值 | 说明 |
| --- | --- | --- | --- | --- | --- |
| siteUrl |字符串 |是 |查询 |无 |项目站点的根站点 URL（示例：https://sampletenant.sharepoint.com/teams/sampleteam）。 |
| proj | |是 |body |无 |要新建的项目 |

#### <a name="response"></a>响应
| Name | 说明 |
| --- | --- |
| 200 |确定 |
| 400 |错误的请求 |
| 401 |未授权 |
| 403 |ForbIDden |
| 404 |未找到 |
| 500 |内部服务器错误。 发生未知错误 |
| default |操作失败。 |

## <a name="createtask"></a>CreateTask
新建任务：在你的项目中新建任务 

```POST: /_api/ProjectServer/Projects('{project_id}')/Draft/Tasks/Add``` 

| Name | 数据类型 | 必选 | 所在位置 | 默认值 | 说明 |
| --- | --- | --- | --- | --- | --- |
| siteUrl |字符串 |是 |查询 |无 |项目站点的根站点 URL（示例：https://sampletenant.sharepoint.com/teams/sampleteam）。 |
| project_id |字符串 |是 |路径 |无 |任务要添加到的项目的唯一 ID |
| task | |是 |body |无 |要添加到项目的新任务 |

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

## <a name="createresource"></a>CreateResource
新建资源：在项目联机站点上创建企业资源 

```POST: /_api/ProjectServer/EnterpriseResources``` 

| Name | 数据类型 | 必选 | 所在位置 | 默认值 | 说明 |
| --- | --- | --- | --- | --- | --- |
| siteUrl |字符串 |是 |查询 |无 |项目站点的根站点 URL（示例：https://sampletenant.sharepoint.com/teams/sampleteam）。 |
| resource | |是 |body |无 |要添加到项目的新企业资源 |

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

## <a name="listtasks"></a>ListTasks
列出任务：在项目中列出已发布任务 

```GET: /_api/ProjectServer/Projects('{project_id}')/Tasks``` 

| Name | 数据类型 | 必选 | 所在位置 | 默认值 | 说明 |
| --- | --- | --- | --- | --- | --- |
| siteUrl |字符串 |是 |查询 |无 |项目站点的根站点 URL（示例：https://sampletenant.sharepoint.com/teams/sampleteam）。 |
| project_id |字符串 |是 |路径 |无 |要提取任务的项目的唯一 ID |

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

## <a name="checkoutproject"></a>CheckoutProject
签出项目：在你的站点中签出项目 

```POST: /_api/ProjectServer/Projects('{project_id}')/checkOut``` 

| Name | 数据类型 | 必选 | 所在位置 | 默认值 | 说明 |
| --- | --- | --- | --- | --- | --- |
| siteUrl |字符串 |是 |查询 |无 |项目站点的根站点 URL（示例：https://sampletenant.sharepoint.com/teams/sampleteam）。 |
| project_id |字符串 |是 |路径 |无 |任务要添加到的项目的唯一 ID |

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

## <a name="publishproject"></a>PublishProject
签入并发布项目：在你的站点中签入并发布现有项目 

```POST: /_api/ProjectServer/Projects('{project_id}')/Draft/Publish(true)``` 

| Name | 数据类型 | 必选 | 所在位置 | 默认值 | 说明 |
| --- | --- | --- | --- | --- | --- |
| siteUrl |字符串 |是 |查询 |无 |项目站点的根站点 URL（示例：https://sampletenant.sharepoint.com/teams/sampleteam）。 |
| project_id |字符串 |是 |路径 |无 |要签入的项目的唯一 ID |

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
### <a name="triggerprojectswrapper"></a>TriggerProjectsWrapper
| 属性名称 | 数据类型 | 必选 |
| --- | --- | --- |
| value |数组 |否 |

### <a name="triggerproject"></a>TriggerProject
| 属性名称 | 数据类型 | 必选 |
| --- | --- | --- |
| ProjectStartDate |字符串 |否 |
| ProjectFinishDate |字符串 |否 |
| ProjectCreatedDate |字符串 |否 |
| ProjectId |字符串 |否 |
| ProjectModifiedDate |字符串 |否 |
| ProjectType |integer |否 |
| ProjectName |字符串 |否 |

### <a name="triggerresourceswrapper"></a>TriggerResourcesWrapper
| 属性名称 | 数据类型 | 必选 |
| --- | --- | --- |
| value |数组 |否 |

### <a name="triggerresource"></a>TriggerResource
| 属性名称 | 数据类型 | 必选 |
| --- | --- | --- |
| ResourceId |字符串 |否 |
| ResourceBaseCalendar |字符串 |否 |
| ResourceBookingType |integer |否 |
| ResourceCanLevel |布尔值 |否 |
| ResourceCostPerUse |数字 |否 |
| ResourceCreatedDate |字符串 |否 |
| ResourceEarliestAvailableFrom |字符串 |否 |
| ResourceEmail |字符串 |否 |
| ResourceInitials |字符串 |否 |
| ResourceIsActive |布尔值 |否 |
| ResourceIsGeneric |布尔值 |否 |
| ResourceLatestAvailableTo |字符串 |否 |
| ResourceModifiedDate |字符串 |否 |
| ResourceName |字符串 |否 |
| ResourceStatsuName |字符串 |否 |
| ResourceType |integer |否 |
| TypeDescription |字符串 |否 |
| TypeName |字符串 |否 |

### <a name="triggertaskswrapper"></a>TriggerTasksWrapper
| 属性名称 | 数据类型 | 必选 |
| --- | --- | --- |
| value |数组 |否 |

### <a name="triggertask"></a>TriggerTask
| 属性名称 | 数据类型 | 必选 |
| --- | --- | --- |
| ProjectId |字符串 |否 |
| TaskId |字符串 |否 |
| ProjectName |字符串 |否 |
| TaskName |字符串 |否 |
| TaskCreatedDate |字符串 |否 |
| TaskModifieddate |字符串 |否 |
| TaskStartDate |字符串 |否 |
| TaskFinishDate |字符串 |否 |
| TaskPriority |integer |否 |
| TaskIsActive |布尔值 |否 |

### <a name="newproject"></a>NewProject
| 属性名称 | 数据类型 | 必选 |
| --- | --- | --- |
| Name |字符串 |是 |
| 说明 |字符串 |否 |
| 开始 |字符串 |否 |

### <a name="newreource"></a>NewReource
| 属性名称 | 数据类型 | 必选 |
| --- | --- | --- |
| Name |字符串 |是 |
| IsBudget |布尔值 |否 |
| IsGeneric |布尔值 |否 |
| IsInactive |布尔值 |否 |

### <a name="project"></a>Project
| 属性名称 | 数据类型 | 必选 |
| --- | --- | --- |
| ApprovedStart |字符串 |否 |
| ApprovedEnd |字符串 |否 |
| CheckedOutDate |字符串 |否 |
| CheckOutDescription |字符串 |否 |
| CheckOutId |字符串 |否 |
| CreatedDate |字符串 |否 |
| ID |字符串 |否 |
| IsCheckedOut |布尔值 |否 |
| LastPublishedDate |字符串 |否 |
| LastSavedDate |字符串 |否 |
| OptimizerDecision |integer |否 |
| PlannerDecision |integer |否 |
| ProjectType |integer |否 |
| Name |字符串 |否 |
| WinprojVersion |字符串 |否 |

### <a name="projectswrapper"></a>ProjectsWrapper
| 属性名称 | 数据类型 | 必选 |
| --- | --- | --- |
| value |数组 |否 |

### <a name="newtask"></a>NewTask
| 属性名称 | 数据类型 | 必选 |
| --- | --- | --- |
| 参数 |未定义 |是 |

### <a name="taskparameters"></a>TaskParameters
| 属性名称 | 数据类型 | 必选 |
| --- | --- | --- |
| Name |字符串 |是 |
| 说明 |字符串 |否 |
| 开始 |字符串 |否 |
| 持续时间 |字符串 |否 |

### <a name="enterpriseresource"></a>EnterpriseResource
| 属性名称 | 数据类型 | 必选 |
| --- | --- | --- |
| CanLevel |布尔值 |否 |
| 代码 |字符串 |否 |
| CostAccrual |integer |否 |
| CostCenter |字符串 |否 |
| 已创建 |字符串 |否 |
| DefaultBookingType |integer |否 |
| 电子邮件 |字符串 |否 |
| ExternalId |字符串 |否 |
| 组 |字符串 |否 |
| HireDate |字符串 |否 |
| ID |字符串 |否 |
| Initials |字符串 |否 |
| IsActive |布尔值 |否 |
| IsBudget |布尔值 |否 |
| IsCheckedOut |布尔值 |否 |
| IsGeneric |布尔值 |否 |
| IsTeam |布尔值 |否 |
| MaterialLabel |字符串 |否 |
| 修改时间 |字符串 |否 |
| Name |字符串 |否 |
| Phonetics |字符串 |否 |
| ResourceType |integer |否 |
| TerminationDate |字符串 |否 |

### <a name="taskswrapper"></a>TasksWrapper
| 属性名称 | 数据类型 | 必选 |
| --- | --- | --- |
| value |数组 |否 |

### <a name="task"></a>任务
| 属性名称 | 数据类型 | 必选 |
| --- | --- | --- |
| 已创建 |字符串 |否 |
| 修改时间 |字符串 |否 |
| 开始 |字符串 |否 |
| 完成 |字符串 |否 |
| Name |字符串 |否 |
| ID |字符串 |否 |
| Priority |integer |否 |
| PercentComplete |integer |否 |
| 说明 |字符串 |否 |
| 联系人 |字符串 |否 |

## <a name="next-steps"></a>后续步骤
[创建逻辑应用](../logic-apps/logic-apps-create-a-logic-app.md)


