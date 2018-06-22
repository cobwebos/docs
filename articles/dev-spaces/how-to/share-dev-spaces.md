---
title: 如何共享 Azure Dev Spaces | Microsoft Docs
titleSuffix: Azure Dev Spaces
services: azure-dev-spaces
ms.service: azure-dev-spaces
ms.component: azds-kubernetes
author: ghogen
ms.author: ghogen
ms.date: 05/11/2018
ms.topic: article
description: 使用 Azure 上的容器和微服务快速开发 Kubernetes
keywords: Docker, Kubernetes, Azure, AKS, Azure Kubernetes 服务, 容器
manager: douge
ms.openlocfilehash: ca644cc412ad0c8f0e2a0781d9419fba58ed8d12
ms.sourcegitcommit: 50f82f7682447245bebb229494591eb822a62038
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/08/2018
ms.locfileid: "35247698"
---
# <a name="share-azure-dev-spaces"></a>共享 Azure Dev Spaces

借助 Azure Dev Spaces，可以与团队中的其他人共享开发空间。 每个开发人员都可以在自己的空间中工作，而不必担心会破坏他人。 另外，在一个空间中一起工作可让你测试端到端代码，而不必创建模拟依赖关系。 请参阅[了解团队开发](../get-started-nodejs.md#learn-about-team-development)指南以获取详细信息。

若要为多个开发人员设置开发空间，请执行以下操作：
1. 在 Azure 中创建开发空间。 选择 [.NET Core 和 VS Code](../get-started-netcore.md)、[.NET Core 和 Visual Studio](../get-started-netcore-visualstudio.md) 或 [Node.js 和 VS Code](../get-started-nodejs.md)。 需要对所选 Azure 订阅具有“所有者”或“参与者”访问权限。
1. 配置 Azure Dev Space 的**资源组**，向每个团队成员[授予“参与者”访问权限](/azure/active-directory/role-based-access-control-configure)。 可以运行下面的命令来检查开发空间的资源组：`azds list`
1. 让团队成员选择要用于开发的开发空间。
     * **命令行或 VS Code**：若要查看现有 Azure Dev Spaces，可以访问 `azds space list`。 若要选择开发空间，可以访问 `azds space select`。
     * **Visual Studio IDE**：在 Visual Studio 中打开项目，从“启动设置”下拉列表中选择 **Azure Dev Spaces**。 在打开的对话框中，选择现有群集。

![Visual Studio“启动设置”下拉列表](../media/get-started-netcore-visualstudio/LaunchSettings.png)