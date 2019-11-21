---
title: Create a function app from the Azure Portal
description: Create a new function app in Azure from the portal.
ms.topic: conceptual
ms.date: 08/29/2019
ms.custom: mvc
ms.openlocfilehash: 74eaa6837f362c849277a761da3ae79c3a8ac353
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/20/2019
ms.locfileid: "74230774"
---
# <a name="create-a-function-app-from-the-azure-portal"></a>通过 Azure 门户创建 Function App

This topic shows you how to use Azure Functions to create a function app in the Azure portal. Function App 是托管各个函数执行的容器。 

## <a name="create-a-function-app"></a>创建函数应用

[!INCLUDE [functions-create-function-app-portal](../../includes/functions-create-function-app-portal.md)]

创建 Function App 时，需提供有效的**应用名称**，其中只能包含字母、数字和连字符。 下划线 ( **_** ) 是不允许的字符。

存储帐户名称必须为 3 到 24 个字符，并且只能包含数字和小写字母。 存储帐户名称在 Azure 中必须是唯一的。 

创建 Function App 后，可以使用一种或多种不同的语言创建各个函数。 [通过使用门户](functions-create-first-azure-function.md#create-function)、[连续部署](functions-continuous-deployment.md)，或通过[使用 FTP 上传](https://github.com/projectkudu/kudu/wiki/Accessing-files-via-ftp)来创建函数。

## <a name="service-plans"></a>服务计划

Azure Functions has three different service plans: Consumption plan, Premium plan, and Dedicated (App Service) plan. You must choose your service plan when your function app is created, and it cannot subsequently be changed. 有关详细信息，请参阅[选择 Azure Functions 托管计划](functions-scale.md)。

If you are planning to run JavaScript functions on a Dedicated (App Service) plan, you should choose a plan with fewer cores. 有关详细信息，请参阅[函数的 JavaScript 参考](functions-reference-node.md#choose-single-vcpu-app-service-plans)。

<a name="storage-account-requirements"></a>

## <a name="storage-account-requirements"></a>存储帐户要求

When creating a function app, you must create or link to a general-purpose Azure Storage account that supports Blob, Queue, and Table storage. Azure Functions 内部使用存储以进行管理触发器和记录函数执行等操作。 某些存储帐户不支持队列和表，例如仅限 blob 的存储帐户、Azure 高级存储和使用 ZRS 复制的常规用途的存储帐户。 创建 Function App 时，将从“存储帐户”边栏选项卡中筛选出这些帐户。

>[!NOTE]
>使用消耗托管计划时，函数代码和绑定配置文件存储在主存储帐户的 Azure 文件存储中。 删除主存储帐户时，此内容将随之删除且无法恢复。

若要了解有关存储帐户类型的详细信息，请参阅 [Azure 存储服务简介](../storage/common/storage-introduction.md#azure-storage-services)。 

## <a name="next-steps"></a>后续步骤

虽然 Azure 门户可以轻松创建和试用 Functions，但我们建议[本地开发](functions-develop-local.md)。 在门户中创建函数应用后，仍然需要添加一个函数。 

> [!div class="nextstepaction"]
> [添加 HTTP 触发的函数](functions-create-first-azure-function.md#create-function)
