---
title: Azure Dev Spaces | Microsoft Docs
services: azure-dev-spaces
ms.service: azure-dev-spaces
ms.component: azds-kubernetes
author: ghogen
ms.author: ghogen
ms.date: 06/01/2018
ms.topic: tutorial
description: 使用 Azure 上的容器和微服务快速开发 Kubernetes
keywords: Docker, Kubernetes, Azure, AKS, Azure Kubernetes 服务, 容器
manager: douge
ms.openlocfilehash: 93440b8a1c9fd1b386931e5998c70133071a079e
ms.sourcegitcommit: 3017211a7d51efd6cd87e8210ee13d57585c7e3b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/06/2018
ms.locfileid: "34823038"
---
# <a name="azure-dev-spaces"></a>Azure Dev Spaces
Azure Dev Spaces 为团队提供快速、迭代的 Kubernetes 开发体验。 只需最少的开发计算机设置，即可直接在 Azure Kubernetes 服务 (AKS) 中以迭代方式运行和调试容器。 使用熟悉的工具（如 Visual Studio、Visual Studio Code 或命令行）在 Windows、Mac 或 Linux 上进行开发。

[!INCLUDE[](includes/dev-spaces-preview.md)]

## <a name="how-azure-dev-spaces-simplifies-kubernetes-development"></a>Azure Dev Spaces 如何简化 Kubernetes 开发 

Azure Dev Spaces 通过以下方式帮助开发团队在 Kubernetes 上提高生产力：
- 最大程度地减少每个团队成员的本地开发计算机设置，并可直接在AKS（Azure 中托管的 Kubernetes 群集）中工作。
- 使用 Visual Studio 2017 或 Visual Studio Code 直接在 Kubernetes 中快速迭代和调试代码。
- 生成 Docker 和 Kubernetes 配置即代码资产，供你从开发到生产使用。 
- 与团队共享托管的 Kubernetes 群集，并协同合作。 可独立开发代码，并与其他组件一起进行端到端测试，而无需复制或模拟依赖关系。

[!INCLUDE[](includes/get-started.md)]

![](media/azure-dev-spaces/vscode-overview.png)

## <a name="see-also"></a>另请参阅

[Azure Kubernetes 服务](/azure/aks)