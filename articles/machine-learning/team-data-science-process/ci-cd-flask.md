---
title: 使用 Azure Pipelines 团队数据科学流程创建 CI/CD 管道
description: 使用 Docker 和 Kubernetes 为人工智能（AI）应用程序创建持续集成和持续交付管道。
services: machine-learning
author: marktab
manager: marktab
editor: marktab
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 01/10/2020
ms.author: tdsp
ms.custom: seodec18, previous-author=jainr, previous-ms.author=jainr
ms.openlocfilehash: 42433ec419ac9e02077cd0359e18b5114206f27d
ms.sourcegitcommit: f52ce6052c795035763dbba6de0b50ec17d7cd1d
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/24/2020
ms.locfileid: "76721823"
---
# <a name="create-cicd-pipelines-for-ai-apps-using-azure-pipelines-docker-and-kubernetes"></a>使用 Azure Pipelines、Docker 和 Kubernetes 为 AI 应用创建 CI/CD 管道

人工智能（AI）应用程序是嵌入了预先训练机器学习（ML）模型的应用程序代码。 AI 应用程序始终有两个工作流：数据科学家构建 ML 模型，应用开发人员构建应用并将其公开给最终用户使用。 本文介绍如何为 AI 应用程序实现持续集成和持续交付（CI/CD）管道，将 ML 模型嵌入应用程序源代码。 示例代码和教程使用 Python Flask web 应用程序，并从专用 Azure blob 存储帐户提取预先训练模型。 你还可以使用 AWS S3 存储帐户。

> [!NOTE]
> 以下过程是执行 CI/CD 的几种方法之一。 此工具和先决条件有一些替代方法。

## <a name="source-code-tutorial-and-prerequisites"></a>源代码、教程和先决条件

可以从 GitHub 下载[源代码](https://github.com/Azure/DevOps-For-AI-Apps)和[详细教程](https://github.com/Azure/DevOps-For-AI-Apps/blob/master/Tutorial.md)。 按照教程步骤为自己的应用程序实现 CI/CD 管道。

若要使用下载的源代码和教程，需要满足以下先决条件： 

- [源代码存储库](https://github.com/Azure/DevOps-For-AI-Apps)分叉到 GitHub 帐户
- [Azure DevOps 组织](/azure/devops/organizations/accounts/create-organization-msa-or-work-student)
- [Azure CLI](/cli/azure/install-azure-cli)
- [适用于 Kubernetes （AKS）群集的 Azure 容器服务](/azure/container-service/kubernetes/container-service-tutorial-kubernetes-deploy-cluster)
- [Kubectl](https://kubernetes.io/docs/tasks/tools/install-kubectl/)从 AKS 群集运行命令和提取配置 
- [Azure 容器注册表（ACR）帐户](/azure/container-registry/container-registry-get-started-portal)

## <a name="cicd-pipeline-summary"></a>CI/CD 管道摘要

每个新的 Git commit 都关闭生成管道。 该版本从 blob 存储帐户中安全地提取最新的 ML 模型，并使用单个容器中的应用代码将其打包。 这种分离的应用程序开发和数据科学工作流可确保生产应用始终运行最新版本的最新 ML 模型。 如果应用通过测试，则管道会将生成映像安全地存储在 ACR 中的 Docker 容器中。 然后，发布管道使用 AKS 部署容器。 

## <a name="cicd-pipeline-steps"></a>CI/CD 管道步骤

以下关系图和步骤介绍了 CI/CD 管道体系结构：

![CI/CD 管道体系结构](./media/ci-cd-flask/architecture.png)

1. 开发人员可在其选择的 IDE 中处理应用程序代码。
2. 开发人员将代码提交到 Azure Repos、GitHub 或其他 Git 源代码管理提供程序。 
3. 数据科学家分别用于开发 ML 模型。
4. 数据科学家将完成的模型发布到模型存储库，在本例中为 blob 存储帐户。 
5. Azure Pipelines 基于 Git commit 启动生成。
6. 生成管道从 blob 存储中提取最新的 ML 模型并创建容器。
7. 管道将生成映像推送到 ACR 中的专用映像存储库。
8. 发布管道基于成功的生成启动。
9. 管道从 ACR 中提取最新的映像，并将其部署在 AKS 上的 Kubernetes 群集中。
10. 用户对应用的请求将通过 DNS 服务器。
11. DNS 服务器将请求传递给负载均衡器，并将响应发送回用户。

## <a name="see-also"></a>另请参阅

- [Team Data Science Process (TDSP)](/azure/machine-learning/team-data-science-process/)
- [Azure 机器学习 (AML)](/azure/machine-learning/)
- [Azure DevOps](https://azure.microsoft.com/services/devops/)
- [Azure Kubernetes 服务 (AKS)](/azure/aks/intro-kubernetes)
