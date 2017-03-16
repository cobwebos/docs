---
title: "通过 Azure 门户创建函数 | Microsoft Docs"
description: "只需不到两分钟即可生成你的第一个 Azure Function（无服务器应用程序）。"
services: functions
documentationcenter: na
author: ggailey777
manager: erikre
editor: 
tags: 
ms.assetid: 96cf87b9-8db6-41a8-863a-abb828e3d06d
ms.service: functions
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 02/02/2017
ms.author: glenga
translationtype: Human Translation
ms.sourcegitcommit: 1e6ae31b3ef2d9baf578b199233e61936aa3528e
ms.openlocfilehash: 4ee741cbec8db6b6400ff9f27daa2a0120bd2618
ms.lasthandoff: 03/03/2017


---
# <a name="create-a-function-from-the-azure-portal"></a>通过 Azure 门户创建函数
## <a name="overview"></a>概述
Azure Functions 提供事件驱动、按需计算体验，可以扩展现有的 Azure 应用程序平台，并能够实现由其他 Azure 服务、SaaS 产品以及本地系统中发生的事件触发的代码。 借助 Azure Functions，应用程序可以按需缩放，且仅需对消耗的资源付费。 Azure Functions 让你能够创建以各种编程语言实现的计划或触发代码单元。 若要了解有关 Azure Functions 的详细信息，请参阅 [Azure Functions 概述](functions-overview.md)。

本主题演示如何使用 Azure 门户创建由 HTTP 触发器调用的简单“hello world”Node.js Azure 函数。 在 Azure 门户中创建函数前，必须在 Azure App Service 中显示创建 Function App。 若要自动创建 Function App，请参阅[另一 Azure Functions 快速入门教程](functions-create-first-azure-function.md)，其为更简单的快速入门体验且包括视频。

## <a name="create-a-function-app"></a>创建 Function App
Function App 在 Azure 中托管函数的执行。 如果尚未创建 Azure 帐户，请查看[试用 Functions](https://functions.azure.com/try) 主题，或[免费创建一个 Azure 帐户](https://azure.microsoft.com/free/)。 请按照下列步骤在 Azure 门户中创建 Function App。

1. 转到 [Azure 门户](https://portal.azure.com)，然后使用你的 Azure 帐户登录。
2. 单击“+新建” > “计算” > “Function App”，选择你的订阅，键入用于标识函数应用的唯一**应用名称**，然后指定以下设置：
   
   * **[资源组](../azure-resource-manager/resource-group-overview.md)**：选择“新建”并为新的资源组输入名称。 还可选择现有资源组，但是可能无法为 Function App 创建基于使用情况的应用服务计划。
   * **[托管计划](../app-service/azure-web-sites-web-hosting-plans-in-depth-overview.md)**，可以是下列其中一项： 
     * **使用计划**：Azure Functions 的默认计划类型。 选择使用计划时，还必须选择“位置”并设置“内存分配”（以 MB 为单位）。 有关内存分配如何影响成本的信息，请参阅 [Azure Functions 定价](https://azure.microsoft.com/pricing/details/functions/)。 
     * **应用服务计划**：应用服务计划要求创建**应用服务计划/位置**或选择现有应用服务计划/位置。 这些设置将确定与应用关联的[位置、功能、成本和计算资源](https://azure.microsoft.com/pricing/details/app-service/)。  
   * **存储帐户**：每个 Function App 都需要存储帐户。 你可以使用现有存储帐户，也可以创建一个存储帐户。 
     
     ![在 Azure 门户中创建新的 Function App](./media/functions-create-first-azure-function-azure-portal/function-app-create-flow.png)

    请注意，必须输入有效的**应用名称**，其中只能包含字母、数字和连字符。 下划线 (**_**) 是不允许的字符。

3. 单击“创建”来预配和部署新的 Function App。  

### <a name="storage-account-requirements"></a>存储帐户要求

创建 Function App 时，必须创建或链接支持 Blob、队列和表存储的常规用途的 Azure 存储帐户。 Azure Functions 内部使用 Azure 存储以进行操作（如管理触发器和记录函数执行）。 某些存储帐户不支持队列和表，例如仅限 blob 的存储帐户（包括高级存储）和使用 ZRS 复制的常规用途的存储帐户。 创建新的 Function App 时，将从“存储帐户”边栏选项卡对这些帐户进行筛选。
使用消耗托管计划时，Function App 内容（如函数代码文件和绑定配置）存储在主存储帐户的 Azure 文件共享上。 如果删除主存储帐户，此内容将被删除，并且无法恢复。

若要了解有关存储帐户类型的详细信息，请参阅 [Azure 存储服务简介] (.../ storage/storage-introduction.md#introducing-the-azure-storage-services)。

## <a name="create-a-function"></a>创建函数
这些步骤从 Azure Functions 快速入门中创建函数。

1. 在“快速入门”选项卡中，单击“WebHook + API”和“JavaScript”，然后单击“创建函数”。 将创建新的预定义 Node.js 函数。 
   
    ![](./media/functions-create-first-azure-function-azure-portal/function-app-quickstart-node-webhook.png)

2. （可选）此时，在快速入门中，可以选择在门户内进行 Azure Functions 功能快速浏览。    完成或跳过浏览后，可以使用 HTTP 触发器测试新函数。


## <a name="test-the-function"></a>测试函数
[!INCLUDE [Functions quickstart test](../../includes/functions-quickstart-test.md)]

## <a name="next-steps"></a>后续步骤
[!INCLUDE [Functions quickstart next steps](../../includes/functions-quickstart-next-steps.md)]

[!INCLUDE [Getting Started Note](../../includes/functions-get-help.md)]


