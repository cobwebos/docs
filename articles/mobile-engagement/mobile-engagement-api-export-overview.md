---
title: "Mobile Engagement 导出 API 概述"
description: "了解有关导出由你的用户设备生成的原始数据的基础知识，以在你自己的工具中加以利用"
services: mobile-engagement
documentationcenter: mobile
author: kpiteira
manager: erikre
editor: 
ms.assetid: 9380d47b-d7fa-4d4c-888f-97e6482196bb
ms.service: mobile-engagement
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: mobile-multiple
ms.workload: mobile
ms.date: 04/26/2016
ms.author: kapiteir
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 9c17a1cb33616ad297d0fad9b8ea674de75bd8fb


---
# <a name="mobile-engagement-export-api-overview"></a>Mobile Engagement 导出 API 概述
## <a name="introduction"></a>介绍
在本文档中，你将了解有关导出由你的用户设备生成的原始数据的基础知识，以在你自己的工具中加以利用。

## <a name="pre-requisites"></a>先决条件
从 Mobile Engagement 导出原始数据有以下几个要求：

* 为了能够使用 API 而进行的 API 身份验证设置（请参阅[身份验证手动设置](mobile-engagement-api-authentication-manual.md)），
* 使用 REST API 或 [.net SDK](mobile-engagement-dotnet-sdk-service-api.md)，
* 一个 Azure 存储帐户。

> [!NOTE]
> 我们还建议至少在开发阶段使用出色的 [Microsoft Azure 存储资源管理器](http://storageexplorer.com/)，因为该存储资源管理器提供了一个可与 Azure 存储进行交互的易于使用的 UI。
> 
> 

## <a name="what-can-be-exported"></a>可以导出什么？
Mobile Engagement 允许其用户收集许多类型的数据，因此，它有适合这些不同数据类型的几种导出类型。
下面是两种基本的导出类型︰

* 快照︰通常用于导出表示一种状态的数据，Mobile Engagement 没有该数据的历史。 例如，这包含标记 (app-info)、令牌或推送营销活动反馈。 因此，这些导出类型与日期无关。
* 历史︰ 此导出类型用于随时间累积的数据，比如事件或活动。

下表穷举了所有可能的导出︰

| 导出类型 | 数据类型 | 说明 |
| --- | --- | --- |
| 快照 |推送 |按 deviceid/userid 生成推送营销活动反馈的导出 |
| 快照 |标记 |生成与每个设备关联的标记 (app-info) 的导出 |
| 快照 |设备 |生成关于设备的大多数数据的导出，比如技术信息（型号、区域设置、时区等）、标记、第一次出现时间等 |
| 快照 |令牌 |生成所有有效令牌的导出 |
| 历史记录 |活动 |生成在给定时间段上每个设备的所有活动的导出 |
| 历史记录 |事件 |生成在给定时间段上每个设备的所有活动的导出 |
| 历史记录 |作业 |生成在给定时间段上每个设备的所有作业的导出 |
| 历史记录 |错误 |生成在给定时间段上每个设备的所有错误的导出 |

## <a name="how-does-it-work"></a>工作原理
导出包括需要长时间运行的许多任务，可能会产生大型数据文件。 出于此原因，无法调用它们来立即返回要下载的文件。
为了从 Mobile Engagement 导出数据，你必须在通常指定的位置通过 API 创建一个**导出作业**：

* 导出的类型（快照或历史记录）
* 数据类型，
* 将在其中写入导出结果的 **Azure 存储容器**（包括一个具有写入访问权限的有效 SAS）。

请注意，可能需要几分钟才能启动你的作业，然后，对于微小应用，你的作业可能会运行几秒，而对于包含大量用户或活动的应用，可能会运行几个小时。

创建了作业之后，就立即可以检查其状态以查看它是否仍在运行或者是否已完成。

作业成功之后，生成的数据文件就会立即在提供的存储容器中可用。




<!--HONumber=Nov16_HO3-->


