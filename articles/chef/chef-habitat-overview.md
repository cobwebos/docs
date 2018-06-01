---
title: 使用 Habitat 将应用程序部署到 Azure
description: 了解如何以一致的方式将应用程序部署到 Azure 虚拟机和容器
keywords: azure, chef, devops, 虚拟机, 概述, 自动化, habitat
ms.service: virtual-machines-linux
author: tomarcher
manager: jeconnoc
ms.author: tarcher
ms.date: 05/15/2018
ms.topic: article
ms.openlocfilehash: 73c6834eb53c0496fa673dd25ab90abc18a6a139
ms.sourcegitcommit: 96089449d17548263691d40e4f1e8f9557561197
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/17/2018
ms.locfileid: "34266968"
---
# <a name="use-habitat-to-deploy-your-application-to-azure"></a>使用 Habitat 将应用程序部署到 Azure
[Habitat](https://www.habitat.sh/) 是一开创性的开源项目，为应用程序管理提供了全新的方法。 Habitat 使应用程序及其自动化成为部署单元。 将应用程序封装在轻型“habitat”中以后，就不再需要关注运行时环境（不管它是容器、裸机还是 PaaS），而运行时环境也不会约束应用程序。 

本文介绍使用 Habitat 的好处。

## <a name="support-for-the-modern-application"></a>支持现代应用程序
Habitat 包囊括应用程序完成其生命周期所需的一切。 Habitat 的核心组件为：
- 打包格式 - Habitat 包中的应用程序是原子的、不可变的、可审核的。
- Habitat 主管在运行应用程序包时了解包的对等关系、升级策略和安全策略。 Habitat 主管针对存在的任何环境来配置和管理应用程序。
- Habitat 生态系统还提供一项生成服务，该服务采用 Habitat 生成计划，并在创建 Habitat 包后将其发布到仓库。

## <a name="run-any-application-anywhere"></a>在任何位置运行任意应用程序
有了 Habitat，应用程序就可以不做修改在任意运行时环境中运行。 这包括从裸机和虚拟机到容器（例如 Docker）、群集管理系统（例如 Mesosphere 或 Kubernetes）、PaaS 系统（例如 Pivotal Cloud Foundry）的一切环境。

## <a name="easily-port-legacy-applications"></a>轻松移植旧应用程序
将旧版应用程序包装在 Habitat 包中时，这些应用程序独立于最初设计时所针对的环境。 可以将这些包快速移到更现代的环境，例如云或容器。 另外，由于 Habitat 包有一个标准的对向界面，旧版应用程序变得更容易管理得多。

## <a name="improve-the-container-experience"></a>改善容器体验
Habitat 降低了在生产环境中管理容器的复杂性。 Habitat 可以在容器中自动完成应用程序配置，解决了开发人员在将基于容器的应用程序从开发环境移至生产环境中时面临的难题。

## <a name="integrate-into-the-chef-devops-workflow"></a>集成到 Chef DevOps 工作流中
Habitat 项目由 Chef 发起。 Habitat 充分利用了 Chef 在基础结构自动化方面的大量经验，为应用程序提供前所未有的自动化功能。 Chef 将为 Habitat 提供商业支持，确保在 Habitat 和 Chef 交付之间进行无缝集成，实现应用程序发布周期（从开发到部署）的完全自动化。

## <a name="next-steps"></a>后续步骤
* [在 Azure 上使用 Chef 创建 Windows 虚拟机](/azure/virtual-machines/windows/chef-automation)