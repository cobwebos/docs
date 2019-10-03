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
ms.openlocfilehash: 9bac92bc1cc94b88dc694b468b795049db4ac9df
ms.sourcegitcommit: a874064e903f845d755abffdb5eac4868b390de7
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/24/2019
ms.locfileid: "68443982"
---
1. 转到 [Azure 门户](https://portal.azure.com)。

1. 选择左侧的 " **+ 创建资源**", 然后选择 "**函数应用**"。

1. 对于 "**托管计划**", 请选择 "**应用服务计划**", 然后选择 "**应用服务计划/位置**"。

    ![创建函数应用](./media/functions-premium-create/create-function-app-resource.png)

1. 选择 "**新建**", 键入**应用服务计划**名称, 选择附近或附近的[区域](https://azure.microsoft.com/regions/)中的某个**位置**或函数访问的其他服务, 然后选择 "**定价层**"。

    ![创建应用服务计划](./media/functions-premium-create/new-app-service-plan.png)

1. 选择**EP1** (弹性高级) 计划, 并选择 "**应用**"。

    ![选择高级计划](./media/functions-premium-create/hosting-plan.png) 

1. 选择 **"确定"** 以创建计划, 并使用图像下的表中指定的其余 function app 设置。 

    ![已完成应用服务计划](./media/functions-premium-create/create-function-app.png)  

    | 设置      | 建议的值  | 描述                                        |
    | ------------ |  ------- | -------------------------------------------------- |
    | **应用名称** | 全局唯一名称 | 用于标识新 Function App 的名称。 有效的字符是 `a-z`、`0-9` 和 `-`。  | 
    | **订阅** | 你的订阅 | 要在其下创建此新函数应用的订阅。 |
    | [资源组](../articles/azure-resource-manager/resource-group-overview.md) |  myResourceGroup | 要在其中创建 Function App 的新资源组的名称。 你还可以使用建议的值。 |
    | **OS** | 首选操作系统 | 高级计划支持 Linux 和 Windows。 |
    | **运行时堆栈** | 首选语言 | 选择支持你喜欢的函数编程语言的运行时。 对于 C# 和 F# 函数，选择 **.NET**。 仅显示所选**OS**上支持的语言。 |
    | **[存储](../articles/storage/common/storage-quickstart-create-account.md)** |  全局唯一名称 |  创建函数应用使用的存储帐户。 存储帐户名称必须为 3 到 24 个字符，并且只能包含数字和小写字母。 也可使用现有帐户，但该帐户必须符合[存储帐户要求](../articles/azure-functions/functions-scale.md#storage-account-requirements)。 |
    | **[Application Insights](../articles/azure-functions/functions-monitoring.md)** | 默认 | 在最近的受支持的区域中，创建一个具有相同应用名称的 Application Insights 资源。 展开此设置即可更改“新建资源名称”，或者在 [Azure 地理位置](https://azure.microsoft.com/global-infrastructure/geographies/)中选择另一个需要在其中存储数据的**位置**。 |

1. 验证设置后, 选择 "**创建**"。

1. 选择门户右上角的“通知”图标，留意是否显示“部署成功”消息。

    ![定义新的函数应用设置](./media/functions-premium-create/function-app-create-notification.png)

1. 选择“转到资源”，查看新的函数应用。 还可选择“固定到仪表板”。 固定可以更轻松地从仪表板返回此函数应用资源。