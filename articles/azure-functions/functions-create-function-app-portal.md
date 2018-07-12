---
title: 通过 Azure 门户创建 Function App | Microsoft Docs
description: 通过门户在 Azure 应用服务中创建新的 Function App。
services: functions
documentationcenter: na
author: ggailey777
manager: cfowler
editor: ''
tags: ''
ms.assetid: ''
ms.service: functions
ms.devlang: multiple
ms.topic: quickstart
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 04/11/2017
ms.author: glenga
ms.custom: mvc
ms.openlocfilehash: 4716d29a7b8c0830e2311398f033f8358ef24529
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/11/2018
ms.locfileid: "38467639"
---
# <a name="create-a-function-app-from-the-azure-portal"></a>通过 Azure 门户创建 Function App

Azure Function App 使用 Azure 应用服务基础结构。 本主题演示如何在 Azure 门户中创建 Function App。 Function App 是托管各个函数执行的容器。 如果在应用服务托管计划中创建 Function App，则 Function App 可以使用应用服务的所有功能。

## <a name="create-a-function-app"></a>创建函数应用

[!INCLUDE [functions-create-function-app-portal](../../includes/functions-create-function-app-portal.md)]

创建 Function App 时，需提供有效的**应用名称**，其中只能包含字母、数字和连字符。 下划线 (**_**) 是不允许的字符。

存储帐户名称必须为 3 到 24 个字符，并且只能包含数字和小写字母。 存储帐户名称在 Azure 中必须是唯一的。 

创建 Function App 后，可以使用一种或多种不同的语言创建各个函数。 [通过使用门户](functions-create-first-azure-function.md#create-function)、[连续部署](functions-continuous-deployment.md)，或通过[使用 FTP 上传](https://github.com/projectkudu/kudu/wiki/Accessing-files-via-ftp)来创建函数。

## <a name="service-plans"></a>服务计划

Azure Functions 有两个不同的服务计划：消耗量计划和应用服务计划。 消耗量计划会在代码运行时自动分配计算能力，根据处理负载的需要扩大规模，在代码不运行时缩小规模。 应用服务计划为 Function App 提供访问应用服务所有工具的权限。 创建 Function App 后，必须选择服务计划，该选项当前无法更改。 有关详细信息，请参阅[选择 Azure Functions 托管计划](functions-scale.md)。

如果计划在应用服务计划上运行 JavaScript 函数，则应选择具有较少核心的计划。 有关详细信息，请参阅[函数的 JavaScript 参考](functions-reference-node.md#choose-single-vcpu-app-service-plans)。

<a name="storage-account-requirements"></a>

## <a name="storage-account-requirements"></a>存储帐户要求

在应用服务中创建 Function App 时，必须创建或链接支持 Blob、队列和表存储的常规用途的 Azure 存储帐户。 Azure Functions 内部使用存储以进行管理触发器和记录函数执行等操作。 某些存储帐户不支持队列和表，例如仅限 blob 的存储帐户、Azure 高级存储和使用 ZRS 复制的常规用途的存储帐户。 创建 Function App 时，将从“存储帐户”边栏选项卡中筛选出这些帐户。

>[!NOTE]
>使用消耗托管计划时，函数代码和绑定配置文件存储在主存储帐户的 Azure 文件存储中。 删除主存储帐户时，此内容将随之删除且无法恢复。

若要了解有关存储帐户类型的详细信息，请参阅 [Azure 存储服务简介](../storage/common/storage-introduction.md#azure-storage-services)。 

## <a name="next-steps"></a>后续步骤

[!INCLUDE [Functions quickstart next steps](../../includes/functions-quickstart-next-steps.md)]



