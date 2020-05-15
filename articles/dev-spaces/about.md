---
title: Azure Dev Spaces 是什么？
services: azure-dev-spaces
ms.date: 05/07/2019
ms.topic: overview
description: 了解 Azure Dev Spaces 如何为团队提供 Azure Kubernetes 服务群集的一种快速、迭代的 Kubernetes 开发体验
keywords: Docker, Kubernetes, Azure, AKS, Azure Kubernetes 服务, 容器, kubectl, k8s
manager: gwallace
ms.openlocfilehash: 8b22181bcddda9e4156c0e0dbe61d7d813498d96
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/29/2020
ms.locfileid: "80529733"
---
# <a name="what-is-azure-dev-spaces"></a>Azure Dev Spaces 是什么？

Azure Dev Spaces 为团队提供 Azure Kubernetes 服务 (AKS) 群集的一种快速、迭代的 Kubernetes 开发体验。 还可以通过 Azure Dev Spaces 使用最小的开发计算机设置调试和测试 AKS 中应用程序的所有组件，无需复制或模拟依赖关系。

![](media/azure-dev-spaces/collaborate-graphic.gif)

## <a name="how-azure-dev-spaces-simplifies-kubernetes-development"></a>Azure Dev Spaces 如何简化 Kubernetes 开发

Azure Dev Spaces 允许团队直接使用其整个微服务体系结构或在 AKS 中运行的应用程序，帮助团队专注于其微服务应用程序的开发和快速迭代。 也可通过 Azure Dev Spaces 以隔离方式单独更新部分微服务体系结构，不影响 AKS 群集的其余部分，也不影响其他开发人员。 Azure Dev Spaces 适用于在较低级别的开发和测试环境中进行开发和测试，不适用于在生产性 ASK 群集上运行。

由于团队可以使用整个应用程序并在 AKS 中直接协作，因此 Azure Dev Spaces 具有以下特点：

* 尽量减少本地计算机设置
* 缩短团队中新开发人员的设置时间
* 通过加快迭代速度，提高团队的速度
* 由于团队成员可以共享群集，因此减少了冗余开发和集成环境的数目
* 不需复制或模拟依赖关系
* 加强了开发团队以及合作团队（例如 DevOps 团队）之间的协作

Azure Dev Spaces 提供的工具可用于为项目生成 Docker 和 Kubernetes 资产。 可以通过该工具轻松地向开发空间和其他 AKS 群集添加新的和现有的应用程序。

若要详细了解 Azure Dev Spaces 工作原理，请参阅 [Azure Dev Spaces 的工作原理及其配置方式][how-dev-spaces-works]。

## <a name="supported-regions-and-configurations"></a>支持的区域和配置

仅[某些区域][supported-regions]中的 AKS 群集支持 Azure Dev Spaces。 Azure Dev Spaces 支持在 Linux、macOS 或 Windows 8 或更高版本上安装了 [Azure Dev Spaces 扩展](https://marketplace.visualstudio.com/items?itemName=azuredevspaces.azds)的情况下使用 [Azure CLI](/cli/azure/install-azure-cli?view=azure-cli-latest) 或 [Visual Studio Code](https://code.visualstudio.com/download)，从而在 AKS 上生成和运行应用程序。 它还支持使用安装在 Windows 8 或更高版本上的 [Visual Studio](https://aka.ms/vsdownload?utm_source=mscom&utm_campaign=msdocs)。 对于 Visual Studio 2019，将需要“Azure 开发”工作负荷。 对于 Visual Studio 2017，将需要“Web 开发”工作负荷和 [Visual Studio Tools for Kubernetes](https://aka.ms/get-vsk8stools)。

## <a name="next-steps"></a>后续步骤

使用 Azure Dev Spaces 的团队可以通过[团队开发快速入门][team-development-quickstart]详细了解如何进行快速的迭代开发。

[how-dev-spaces-works]: how-dev-spaces-works.md
[supported-regions]: https://azure.microsoft.com/global-infrastructure/services/?products=kubernetes-service
[team-development-quickstart]: quickstart-team-development.md
