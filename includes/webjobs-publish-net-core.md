---
title: include 文件
description: include 文件
services: app-service
author: ggailey777
ms.service: app-service
ms.topic: include
ms.date: 02/19/2019
ms.author: glenga
ms.custom: include file
ms.openlocfilehash: 4860532e59227618ce819772887556719ecb53fc
ms.sourcegitcommit: 3dc1a23a7570552f0d1cc2ffdfb915ea871e257c
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/15/2020
ms.locfileid: "76020921"
---
1. **在“解决方案资源管理器”** 中，右键单击该项目并选择“发布”。

1. 在 "**发布**" 对话框中，选择 " **Microsoft Azure 应用服务**"，选择 "**新建**"，然后选择 "**发布**"。

   ![选择发布目标](./media/webjobs-publish-netcore/pick-publish-target.png)

1. 在 "**创建应用服务**" 对话框中，使用映像下的表中指定的 "托管" 设置：

    ![“创建应用服务”对话框](./media/webjobs-publish-netcore/app-service-dialog.png)

    | 设置      | 建议的值  | Description                                |
    | ------------ |  ------- | -------------------------------------------------- |
    | **应用名称** | 全局唯一名称 | 用于唯一标识新 Function App 的名称。 |
    | **订阅** | 选择自己的订阅 | 要使用的 Azure 订阅。 |
    | [资源组](../articles/azure-resource-manager/management/overview.md) | myResourceGroup |  要在其中创建 Function App 的资源组的名称。 选择“新建”创建新的资源组。|
    | **[托管计划](../articles/app-service/overview-hosting-plans.md)** | 应用服务计划 | [应用服务计划](../articles/app-service/overview-hosting-plans.md)指定托管应用的 Web 服务器场的位置、大小和功能。 可以在托管多个应用时节省资金，方法是将多个 Web 应用配置为共用单个应用服务计划。 应用服务计划定义区域、实例大小、规模计数和 SKU （免费、共享、基本、标准或高级）。 选择 "**新建**" 以创建新的应用服务计划。 |

1. 单击 "**创建**"，使用这些设置在 Azure 中创建 WebJob 和相关资源，并部署项目代码。