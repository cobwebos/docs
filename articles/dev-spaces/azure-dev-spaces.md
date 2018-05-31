---
title: Azure Dev Spaces | Microsoft Docs
services: azure-dev-spaces
ms.service: azure-dev-spaces
ms.component: azds-kubernetes
author: ghogen
ms.author: ghogen
ms.date: 05/11/2018
ms.topic: tutorial
description: 使用 Azure 上的容器和微服务快速开发 Kubernetes
keywords: Docker, Kubernetes, Azure, AKS, Azure Kubernetes 服务, 容器
manager: douge
ms.openlocfilehash: 344947b7906d15e819e372e0affe4af3c34ba69b
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/16/2018
ms.locfileid: "34198752"
---
# <a name="azure-dev-spaces"></a>Azure Dev Spaces
Azure Dev Spaces 可帮助你在 Kubernetes 上快速开发。 使用 Azure Dev Spaces，还可以将全部开发功能（如调试）添加到 Azure Kubernetes 容器中，并且可以使用 VS Code、Visual Studio 或命令行等熟悉的工具在云中以迭代方式开发容器。 Azure Dev Spaces 在团队开发中尤为重要，因为在它们自己的空间中隔离各个代码分支是开发生命周期的关键部分。

## <a name="how-azure-dev-spaces-simplifies-kubernetes-development"></a>Azure Dev Spaces 如何简化 Kubernetes 开发 

此方法具有以下几个优点：

* 可获得无基础结构的开发环境，该环境可以代表生产环境，并对云资源具有完全访问权限。
* 可使用 VS Code 或 Visual Studio 直接在 Kubernetes 中调试 Node.js 和 .NET Core 容器。 所有其他语言都可以使用命令行接口进行开发。
* 可在开发团队中分享 Kubernetes 实例以节省成本，并可最大限度地减少新团队成员的本地计算机设置。
* 可独立开发代码，并与其他组件一起进行端到端测试，而无需复制或模拟依赖关系。

[!INCLUDE[](includes/get-started.md)]

![](media/azure-dev-spaces/vscode-overview.png)