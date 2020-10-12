---
title: include 文件
description: include 文件
services: functions
author: jeffhollan
ms.service: azure-functions
ms.topic: include
ms.date: 04/24/2020
ms.author: jehollan, glenga
ms.custom: include file
ms.openlocfilehash: 4dc85988d904fdec72e1e6d92f03582a2a8f1427
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "85121391"
---
1. 在 Azure 门户菜单或“主页”页中，选择“创建资源”   。

1. 在 **“新建”** 页面，选择 **“计算”**  >  **“函数应用”** 。

1. 在“基本信息”页面上，按照下列所述使用函数应用设置：

    | 设置      | 建议的值  | 说明 |
    | ------------ | ---------------- | ----------- |
    | **订阅** | 订阅 | 要在其下创建此新函数应用的订阅。 |
    | **[资源组](../articles/azure-resource-manager/management/overview.md)** |  *myResourceGroup* | 要在其中创建 Function App 的新资源组的名称。 |
    | **函数应用名称** | 全局唯一名称 | 用于标识新 Function App 的名称。 有效字符为 `a-z`（不区分大小写）、`0-9` 和 `-`。  |
    |**发布**| 代码 | 用于发布代码文件或 Docker 容器的选项。 |
    | **运行时堆栈** | 首选语言 | 选择支持你喜欢的函数编程语言的运行时。 对于 C# 和 F# 函数，选择 **.NET**。 |
    |**区域**| 首选区域 | 选择离你近或离函数访问的其他服务近的[区域](https://azure.microsoft.com/regions/)。 |

    ![“基本信息”页](./media/functions-premium-create/function-app-create-basics.png)

1. 在完成时选择“下一步:托管”。 在“托管”页面上，输入以下设置：

    | 设置      | 建议的值  | 说明 |
    | ------------ | ---------------- | ----------- |
    | [存储帐户](../articles/storage/common/storage-account-create.md) |  全局唯一名称 |  创建函数应用使用的存储帐户。 存储帐户名称必须为 3 到 24 个字符，并且只能包含数字和小写字母。 也可使用现有帐户，但该帐户必须符合[存储帐户要求](../articles/azure-functions/functions-scale.md#storage-account-requirements)。 |
    |**操作系统**| 首选操作系统 | 系统会根据你的运行时堆栈选择为你预先选择一个操作系统，但你可以根据需要更改该设置。 Python 仅在 Linux 上受支持。 |
    | **[计划](../articles/azure-functions/functions-scale.md)** | Premium | 定义如何将资源分配给 Function App 的托管计划。 选择“高级”。 默认创建一个新的应用服务计划。 默认的“SKU 和大小”为 EP1，其中 EP 表示“弹性高级”(elastic premium) 。 要了解详细信息，请参阅[高级 SKU 的列表](../articles/azure-functions/functions-premium-plan.md#available-instance-skus)。<br/>在高级计划上运行 JavaScript 函数时，应选择 vCPU 数更少的实例。 有关详细信息，请参阅[选择单核心高级计划](../articles/azure-functions/functions-reference-node.md#considerations-for-javascript-functions)。  |

    ![“托管”页](./media/functions-premium-create/function-app-premium-create-hosting.png)

1. 在完成时选择“下一步:监视”。 在“监视”页面上，输入以下设置：

    | 设置      | 建议的值  | 说明 |
    | ------------ | ---------------- | ----------- |
    | **[Application Insights](../articles/azure-functions/functions-monitoring.md)** | 默认 | 在最近的受支持的区域中，创建一个具有相同应用名称的 Application Insights 资源。 展开此设置即可更改“新建资源名称”，或者在 [Azure 地理位置](https://azure.microsoft.com/global-infrastructure/geographies/)选择其他位置来存储你的数据 。 |

    ![“监视”页](./media/functions-create-function-app-portal/function-app-create-monitoring.png)

1. 选择“查看 + 创建”，以便查看应用配置选择。

1. 在“查看 + 创建”页上查看设置，然后选择“创建”来预配并部署函数应用 。

1. 选择门户右上角的“通知”图标，留意是否显示了“部署成功”消息。 

1. 选择“转到资源”，查看新的函数应用。 还可选择“固定到仪表板”。 固定可以更轻松地从仪表板返回此函数应用资源。

    ![部署通知](./media/functions-premium-create/function-app-create-notification2.png)
