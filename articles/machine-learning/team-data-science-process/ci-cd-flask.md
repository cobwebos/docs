---
title: 使用 Azure Pipelines 创建 CI/CD 管道 - Team Data Science Process
description: 使用 Docker 和 Kubernetes 为人工智能 (AI) 应用程序创建持续集成和持续交付管道。
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
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/28/2020
ms.locfileid: "76721823"
---
# <a name="create-cicd-pipelines-for-ai-apps-using-azure-pipelines-docker-and-kubernetes"></a>使用 Azure Pipelines、Docker 和 Kubernetes 为 AI 应用创建 CI/CD 管道

人工智能 (AI) 应用程序是嵌入在预先训练的机器学习 (ML) 模型中的应用程序代码的组合。 AI 应用程序始终有两个工作流：数据科学家构建 ML 模型，应用开发人员构建应用并将其公开给最终用户使用。 本文介绍如何为将在应用源代码嵌入了 ML 模型的 AI 应用程序实现持续集成和持续交付 (CI/CD) 管道。 示例代码和教程使用 Python Flask web 应用程序，并从专用 Azure blob 存储帐户提取预先训练模型。 也可以使用 AWS S3 存储帐户。

> [!NOTE]
> 以下过程是执行 CI/CD 的几种方法之一。 除了此工具和先决条件之外，还有其他选择。

## <a name="source-code-tutorial-and-prerequisites"></a>源代码、教程和先决条件

可以从 GitHub 下载[源代码](https://github.com/Azure/DevOps-For-AI-Apps)和[详细教程](https://github.com/Azure/DevOps-For-AI-Apps/blob/master/Tutorial.md)。 按照教程步骤为自己的应用程序实现 CI/CD 管道。

若要使用下载的源代码和教程，需满足以下先决条件： 

- [源代码存储库](https://github.com/Azure/DevOps-For-AI-Apps)分叉到 GitHub 帐户
- [Azure DevOps 组织](/azure/devops/organizations/accounts/create-organization-msa-or-work-student)
- [Azure CLI](/cli/azure/install-azure-cli)
- [适用于 Kubernetes 的 Azure 容器服务 (AKS) 群集](/azure/container-service/kubernetes/container-service-tutorial-kubernetes-deploy-cluster)
- 用于运行命令并从 AKS 群集中提取配置的 [Kubectl](https://kubernetes.io/docs/tasks/tools/install-kubectl/) 
- [Azure 容器注册表 (ACR) 帐户](/azure/container-registry/container-registry-get-started-portal)

## <a name="cicd-pipeline-summary"></a>CI/CD 管道摘要

每个新的 Git 提交都会启动生成管道。 生成从 blob 存储帐户安全提取最新 ML 模型，并将其与应用代码打包到单个容器中。 应用程序开发和数据科学工作流的这种分离，可确保生产应用始终使用最新的 ML 模型运行最新的代码。 如果应用通过测试，管道会将生成映像安全存储在 ACR 中的 Docker 容器内。 然后，发布管道将使用 AKS 部署容器。 

## <a name="cicd-pipeline-steps"></a>CI/CD 管道步骤

下面的关系图和步骤介绍了 CI/CD 管道体系结构：

![CI/CD 管道体系结构](./media/ci-cd-flask/architecture.png)

1. 开发人员使用自己选择的 IDE 编写应用程序代码。
2. 开发人员将代码提交到 Azure Repos、GitHub 或其他 Git 源代码管理平台。 
3. 另一边，数据科学家在开发 ML 模型。
4. 数据科学家将完成的模型发布到模型存储库，在本例中为 blob 存储帐户。 
5. Azure Pipelines 基于 Git 提交启动生成。
6. 生成管道从 blob 存储中拉取最新的 ML 模型并创建容器。
7. 管道将生成映像推送到 ACR 中的专用映像存储库。
8. 发布管道基于成功的生成启动。
9. 管道从 ACR 中拉取最新的映像，并将其部署在 AKS 上的 Kubernetes 群集中。
10. 用户对应用的请求将通过 DNS 服务器。
11. DNS 服务器将请求传递给负载均衡器，并将响应发送回用户。

## <a name="see-also"></a>另请参阅

- [Team Data Science Process (TDSP)](/azure/machine-learning/team-data-science-process/)
- [Azure 机器学习 (AML)](/azure/machine-learning/)
- [Azure DevOps](https://azure.microsoft.com/services/devops/)
- [Azure Kubernetes 服务 (AKS)](/azure/aks/intro-kubernetes)
