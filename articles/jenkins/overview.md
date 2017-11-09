---
title: "Jenkins 和 Azure 概述 | Microsoft 文档"
description: "在 Azure 中托管 Jenkins 生成和部署自动化服务器，并使用 Azure 计算和存储资源来扩展持续集成及部署 (CI/CD) 管道。"
services: jenkins
author: rloutlaw
manager: justhe
ms.service: jenkins
ms.devlang: NA
ms.topic: article
ms.workload: na
ms.date: 08/22/2017
ms.author: routlaw
ms.custom: mvc
ms.openlocfilehash: daa202ddf0dc934c491ead3951ddc4fdc3dd819c
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/11/2017
---
# <a name="azure-and-jenkins"></a>Azure 和 Jenkins

[Jenkins](https://jenkins.io/) 是一个受欢迎的开源自动化服务器，用于设置软件项目的持续集成和交付 (CI/CD)。 可以使用 Azure 资源在 Azure 中托管 Jenkins 部署或扩展现有的 Jenkins 配置。 此外，Jenkins 插件还可用来简化应用程序在 Azure 中的 CI/CD 过程。

本文将介绍如何将 Azure 用于 Jenkins，详细说明可供 Jenkins 用户使用的核心 Azure 功能。 若要在 Azure 中开始使用你自己的 Jenkins 服务器，请参阅我们的[快速入门](install-jenkins-solution-template.md)。

## <a name="host-your-jenkins-servers-in-azure"></a>在 Azure 中托管 Jenkins 服务器

在 Azure 中托管 Jenkins，以集中执行生成自动化，并根据软件项目增长的需要来扩展部署。 可以使用以下项在 Azure 中部署 Jenkins：
 
- Azure Marketplace 中的 [Jenkins 解决方案模板](install-jenkins-solution-template.md)。
- [Azure 虚拟机](/azure/virtual-machines/linux/overview)。 请参阅我们的[教程](/azure/virtual-machines/linux/tutorial-jenkins-github-docker-cicd)，在 VM 上创建 Jenkins 实例。
- 有关在 [Azure 容器服务](/azure/container-service/kubernetes/container-service-kubernetes-walkthrough)中运行的 Kubernetes 群集，请参阅我们的[操作指南](/azure/container-service/kubernetes/container-service-kubernetes-jenkin)。

使用 [Log Analytics](/azure/log-analytics/log-analytics-overview)、[Operations Management Suite](/azure/operations-management-suite/operations-management-suite-overview)，和 [Azure CLI] (/cli/azure/overview) 来监视和管理 Azure Jenkins 部署。

## <a name="scale-your-build-automation-on-demand"></a>按需扩展生成自动化

将生成代理添加到现有 Jenkins 部署来扩展 Jenkins 生成能力，因为作业和管道的生成数量及复杂性都在增加。 你可以通过使用 [Azure VM 代理插件](jenkins-azure-vm-agents.md)在 Azure 虚拟机上运行这些生成代理。 请参阅我们的[教程](/azure/jenkins/jenkins-azure-vm-agents)，了解详细信息。

配置 [Azure 服务主体](/azure/azure-resource-manager/resource-group-overview)后，Jenkins 作业和管道可以使用此凭据执行以下操作：

- 使用 [Azure 存储插件](https://plugins.jenkins.io/windows-azure-storage)在 [Azure 存储](/azure/storage/common/storage-introduction)中安全存储和存档生成项目。 查看 [Jenkins 存储操作方法](/azure/storage/common/storage-java-jenkins-continuous-integration-solution)了解详细信息。
- 使用 [Azure CLI](/azure/jenkins/execute-cli-jenkins-pipeline) 管理和配置 Azure 资源。

## <a name="deploy-your-code-into-azure-services"></a>将代码部署到 Azure 服务

使用 Jenkins 插件将应用程序作为 Jenkins CI/CD 管道的一部分部署到 Azure。 通过部署到 [Azure 应用服务](/azure/app-service/)和 [Azure 容器服务](/azure/container-service/kubernetes/)，可以暂存和测试更新，并将其发布到应用程序，而无需管理基础结构。

 插件可用于部署到以下服务和环境：

- [Linux 上的 Azure Web 应用](/azure/app-service/containers/app-service-linux-intro)。 请参阅[教程](java-deploy-webapp-tutorial.md)以开始使用。
- [Azure Web 应用](/azure/app-service/app-service-web-overview)。 请参阅[操作指南](deploy-Jenkins-app-service-plugin.md)以开始使用。

