---
title: include 文件
description: include 文件
services: functions
author: jeffhollan
ms.service: azure-functions
ms.topic: include
ms.date: 10/25/2019
ms.author: jehollan, glenga
ms.custom: include file
ms.openlocfilehash: fca8c3b0b1bd4d22720a6d15313e297d05b7fac9
ms.sourcegitcommit: 3dc1a23a7570552f0d1cc2ffdfb915ea871e257c
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/15/2020
ms.locfileid: "76020910"
---
1. 从 [https://portal.azure.com](https://portal.azure.com) 打开 Azure 门户

1. 选择“创建资源”按钮

    ![创建资源](./media/functions-create-function-app-portal/function-app-create-resource.png)

1. 选择“计算” > “函数应用”。

    ![在 Azure 门户中创建函数应用](./media/functions-premium-create/function-app-create-start.png)

1. 使用图像下面的表格中指定的函数应用设置。

    ![基础](./media/functions-premium-create/function-app-create-basics.png)

    | 设置      | 建议的值  | Description |
    | ------------ | ---------------- | ----------- |
    | **订阅** | 订阅 | 要在其下创建此新函数应用的订阅。 |
    | [资源组](../articles/azure-resource-manager/management/overview.md) |  *myResourceGroup* | 要在其中创建 Function App 的新资源组的名称。 |
    | **函数应用名称** | 全局唯一名称 | 用于标识新 Function App 的名称。 有效字符为 `a-z`（不区分大小写）、`0-9` 和 `-`。  |
    |**发布**| 代码 | 用于发布代码文件或 Docker 容器的选项。 |
    | **运行时堆栈** | 首选语言 | 选择支持你喜欢的函数编程语言的运行时。 对于 C# 和 F# 函数，选择 **.NET**。 |
    |**区域**| 首选区域 | 选择离你近或离函数访问的其他服务近的[区域](https://azure.microsoft.com/regions/)。 |

    选择**下一步：宿主 >** 按钮。

1. 输入以下宿主设置。

    ![托管](./media/functions-premium-create/function-app-premium-create-hosting.png)

    | 设置      | 建议的值  | Description |
    | ------------ | ---------------- | ----------- |
    | [存储帐户](../articles/storage/common/storage-account-create.md) |  全局唯一名称 |  创建函数应用使用的存储帐户。 存储帐户名称必须为 3 到 24 个字符，并且只能包含数字和小写字母。 也可使用现有帐户，但该帐户必须符合[存储帐户要求](../articles/azure-functions/functions-scale.md#storage-account-requirements)。 |
    |**操作系统**| 首选操作系统 | 系统会根据你的运行时堆栈选择为你预先选择一个操作系统，但你可以根据需要更改该设置。 |
    | **[计划](../articles/azure-functions/functions-scale.md)** | 高级 | 对于 "计划类型"，选择 "**高级（预览版）** "，然后选择 " *Windows 计划*和*Sku" 和 "大小*" 选项的默认值。 |

    选择**下一步：监视 >** 按钮。

1. 输入以下监视设置。

    ![监视](./media/functions-create-function-app-portal/function-app-create-monitoring.png)

    | 设置      | 建议的值  | Description |
    | ------------ | ---------------- | ----------- |
    | **[Application Insights](../articles/azure-functions/functions-monitoring.md)** | 默认 | 在最近的受支持的区域中，创建一个具有相同应用名称的 Application Insights 资源。 展开此设置即可更改“新建资源名称”，或者在 [Azure 地理位置](https://azure.microsoft.com/global-infrastructure/geographies/)中选择另一个需要在其中存储数据的**位置**。 |

    选择“查看 + 创建”，以便查看应用配置选择。

1. 选择“创建”以预配和部署函数应用。