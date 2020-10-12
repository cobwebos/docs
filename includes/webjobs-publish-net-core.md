---
title: include 文件
description: include 文件
services: app-service
author: ggailey777
ms.service: app-service
ms.topic: include
ms.date: 06/26/2020
ms.author: glenga
ms.custom: include file
ms.openlocfilehash: e208b52c67f173bd0d289715b63562df656b1ec9
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "88009732"
---
1. **在“解决方案资源管理器”** 中，右键单击该项目并选择“发布”  。

1. 在 " **发布** " 对话框中，选择 " **Azure** " 作为 **目标**，然后选择 " **下一步**"。 

1. 选择**特定目标** **Azure WebJobs** ，然后选择 "**下一步**"。

1. 选择 " **创建新的 Azure WebJob**"。

   ![选取发布目标](./media/webjobs-publish-netcore/pick-publish-target.png)

1. 在 **应用服务 (Windows) ** "对话框中，使用下表中的托管设置。

    | 设置      | 建议的值  | 说明                                |
    | ------------ |  ------- | -------------------------------------------------- |
    | **名称** | 全局唯一名称 | 用于唯一标识新 Function App 的名称。 |
    | **订阅** | 选择订阅 | 要使用的 Azure 订阅。 |
    | **[资源组](../articles/azure-resource-manager/management/overview.md)** | myResourceGroup |  要在其中创建函数应用的资源组的名称。 选择“新建”  创建新的资源组。|
    | **[托管计划](../articles/app-service/overview-hosting-plans.md)** | 应用服务计划 | [应用服务计划](../articles/app-service/overview-hosting-plans.md)指定托管应用的 Web 服务器场的位置、大小和功能。 可以在托管多个应用时节省资金，方法是将多个 Web 应用配置为共用单个应用服务计划。 应用服务计划定义区域、实例大小、规模计数和 SKU（免费、共享、基本、标准或高级）。 然后，选择“新建”**** 以创建一个新的应用服务计划。 |

    ![“创建应用服务”对话框](./media/webjobs-publish-netcore/app-service-dialog.png)

1. 选择 " **创建** "，使用这些设置在 Azure 中创建 WebJob 和相关资源，并部署项目代码。