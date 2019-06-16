---
title: include 文件
description: include 文件
services: functions
author: jeffhollan
ms.service: azure-functions
ms.topic: include
ms.date: 04/01/2019
ms.author: jehollan, glenga
ms.custom: include file
ms.openlocfilehash: 0f3303e7bc87ca0bd29f367405372568ed6da7a7
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/13/2019
ms.locfileid: "66131559"
---
1. 转到 [Azure 门户](https://portal.azure.com)。

1. 选择 **+ 创建资源**上的左侧，然后选择**函数应用**。

1. 有关**托管计划**，选择**应用服务计划**，然后选择**应用服务计划/位置**。

    ![创建函数应用](./media/functions-premium-create/create-function-app-resource.png)

1. 选择**新建**，类型**应用服务计划**名称中，选择**位置**中[区域](https://azure.microsoft.com/regions/)离您最近或附近其他服务函数访问，然后依次**定价层**。

    ![创建应用服务计划](./media/functions-premium-create/new-app-service-plan.png)

1. 选择**EP1** （弹性高级版） 计划，然后选择**应用**。

    ![选择高级版计划](./media/functions-premium-create/hosting-plan.png) 

1. 选择**确定**创建计划，然后使用为剩余的 function app 设置中指定图像下的表。 

    ![已完成的应用服务计划](./media/functions-premium-create/create-function-app.png)  

    | 设置      | 建议的值  | 描述                                        |
    | ------------ |  ------- | -------------------------------------------------- |
    | **应用名称** | 全局唯一名称 | 用于标识新 Function App 的名称。 有效的字符是 `a-z`、`0-9` 和 `-`。  | 
    | **订阅** | 订阅 | 要在其下创建此新函数应用的订阅。 |
    |  [资源组](../articles/azure-resource-manager/resource-group-overview.md) |  myResourceGroup | 要在其中创建 Function App 的新资源组的名称。 此外可以使用建议的值。 |
    | **OS** | Windows | Linux 当前不支持在高级版计划中。 |
    | **运行时堆栈** | 首选语言 | 选择支持你喜欢的函数编程语言的运行时。 对于 C# 和 F# 函数，选择 **.NET**。 只有在你所选择的受支持的语言**OS**显示。 |
    | **[存储](../articles/storage/common/storage-quickstart-create-account.md)** |  全局唯一名称 |  创建函数应用使用的存储帐户。 存储帐户名称必须为 3 到 24 个字符，并且只能包含数字和小写字母。 也可使用现有帐户，但该帐户必须符合[存储帐户要求](../articles/azure-functions/functions-scale.md#storage-account-requirements)。 |
    | **[Application Insights](../articles/azure-functions/functions-monitoring.md)** | 默认 | 在最近的受支持的区域中，创建一个具有相同应用名称  的 Application Insights 资源。 展开此设置即可更改“新建资源名称”，或者在 [Azure 地理位置](https://azure.microsoft.com/global-infrastructure/geographies/)中选择另一个需要在其中存储数据的**位置**。  |

1. 验证你的设置后，选择**创建**。

1. 选择门户右上角的“通知”图标，留意是否显示“部署成功”消息。 

    ![定义新的函数应用设置](./media/functions-premium-create/function-app-create-notification.png)

1. 选择“转到资源”  ，查看新的函数应用。 还可选择“固定到仪表板”  。 固定可以更轻松地从仪表板返回此函数应用资源。