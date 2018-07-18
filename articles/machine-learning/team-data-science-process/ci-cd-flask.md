---
title: 人工智能 (AI) 应用程序的 DevOps：使用 Docker、Kubernetes 和 Python Flask 应用程序在 Azure 上创建连续集成管道
description: 人工智能 (AI) 应用程序的 DevOps：使用 Docker 和 Kubernetes 在 Azure 上创建连续集成管道
services: machine-learning, team-data-science-process
documentationcenter: ''
author: jainr
manager: deguhath
editor: cgronlun
ms.assetid: b8fbef77-3e80-4911-8e84-23dbf42c9bee
ms.service: machine-learning
ms.component: team-data-science-process
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/22/2018
ms.author: jainr
ms.openlocfilehash: 4d95fc25ed6f2f2efec8313e5b208b3cccbb619f
ms.sourcegitcommit: f606248b31182cc559b21e79778c9397127e54df
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/12/2018
ms.locfileid: "38968785"
---
# <a name="devops-for-artificial-intelligence-ai-applications-creating-continuous-integration-pipeline-on-azure-using-docker-and-kubernetes"></a>人工智能 (AI) 应用程序的 DevOps：使用 Docker 和 Kubernetes 在 Azure 上创建持续集成管道
对于 AI 应用程序，通常有两个工作流：数据科学家构建机器学习模型，应用开发人员构建应用程序并将其公开给最终用户供使用。 在本文中，我们演示了如何为 AI 应用程序实现持续集成 (CI)/持续交付 (CD) 管道。 AI 应用程序是嵌入在预先训练的机器学习 (ML) 模型中的应用程序代码的组合。 在本文中，我们将从一个专用 Azure blob 存储帐户中提取预先训练的模型，它也可以是 AWS S3 帐户。 对于本文，我们将使用一个简单的 python flask Web 应用程序。

> [!NOTE]
> 这是可以执行 CI/CD 的几种方式之一。 下文提到了一些替代工具和其他先决条件。 随着我们开发的内容越来越多，我们将发布那些内容。
>
>

## <a name="github-repository-with-document-and-code"></a>包含文档和代码的 GitHub 存储库
可以从 [GitHub](https://github.com/Azure/DevOps-For-AI-Apps) 下载源代码。 还提供了一个[详细教程](https://github.com/Azure/DevOps-For-AI-Apps/blob/master/Tutorial.md)。

## <a name="pre-requisites"></a>先决条件
下面是实现下文介绍的 CI/CD 管道的先决条件：
* [Visual Studio Team Services 帐户](https://docs.microsoft.com/vsts/accounts/create-account-msa-or-work-student)
* [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)
* [运行 Kubernetes 的 Azure 容器服务 (AKS) 群集](https://docs.microsoft.com/azure/container-service/kubernetes/container-service-tutorial-kubernetes-deploy-cluster)
* [Azure 容器注册表 (ACR) 帐户](https://docs.microsoft.com/azure/container-registry/container-registry-get-started-portal)
* [安装 Kubectl 以针对 Kubernetes 群集运行命令。](https://kubernetes.io/docs/tasks/tools/install-kubectl/) 我们将需要使用此工具从 ACS 群集提取配置。 
* 将存储库分叉到 GitHub 帐户。

## <a name="description-of-the-cicd-pipeline"></a>CI/CD 管道的说明
该管道将针对每个新提交启动，然后运行测试套件，如果测试通过，则会获取最新版本并将其打包到 Docker 容器中。 然后将使用 Azure 容器服务 (ACS) 部署容器，映像将安全地存储到 Azure 容器注册表 (ACR) 中。 ACS 运行 Kubernetes 来管理容器群集，但你可以选择 使用 Docker Swarm 或 Mesos。

应用程序安全地从 Azure 存储帐户拉取最新模型并将其打包为应用程序的一部分。 已部署的应用程序将应用代码和 ML 模型打包为单个容器。 这将应用开发人员与数据科学家分离开来，以确保他们的生产应用始终运行最新的代码和最新的 ML 模型。

下面给出了管道体系结构。 

![体系结构](./media/ci-cd-flask/Architecture.PNG?raw=true)

## <a name="steps-of-the-cicd-pipeline"></a>CI/CD 管道的步骤
1. 开发人员使用自己选择的 IDE 编写应用程序代码。
2. 他们将代码提交到他们选择的源代码管理工具（VSTS 能够很好地支持各种源代码管理工具）
3. 另一边，数据科学家在开发模型。
4. 对模型感到满意后，他们将模型发布到一个模型存储库，在本例中我们使用的是 blob 存储帐户。 这可以通过 REST API 轻松替换为 Azure ML Workbench 的模型管理服务。
5. 生成过程根据 GitHub 中的提交在 VSTS 中启动。
6. VSTS 生成管道从 Blob 容器中拉取最新的模型，并创建一个容器。
7. VSTS 将映像推送到 Azure 容器注册表中的一个专用映像存储库。
8. 发布管道按设定的计划（每夜）启动。
9. 拉取 ACR 中的最新映像并将其部署到 ACS 上的 Kubernetes 群集中。
10. 用户向应用发出的请求经过 DNS 服务器。
11. DNS 服务器将请求传递到负载平衡器，并向用户发送响应。

## <a name="next-steps"></a>后续步骤
* 参阅[教程]((https://github.com/Azure/DevOps-For-AI-Apps/blob/master/Tutorial.md))来理解细节并为应用程序实现你自己的 CI/CD 管道。

## <a name="references"></a>参考
* [Team Data Science Process (TDSP)](https://aka.ms/tdsp)
* [Azure 机器学习 (AML)](https://docs.microsoft.com/azure/machine-learning/service/)
* [Visual Studio Team Services (VSTS)](https://www.visualstudio.com/vso/)
* [Azure Kubernetes 服务 (AKS)](https://docs.microsoft.com/azure/aks/intro-kubernetes)