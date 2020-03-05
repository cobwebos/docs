---
title: 适用于 Azure Kubernetes 的部署中心
description: Azure DevOps 中的部署中心简化了为应用程序设置可靠的 Azure DevOps 管道的过程
ms.author: puagarw
ms.topic: tutorial
ms.date: 07/12/2019
author: pulkitaggarwl
monikerRange: vsts
ms.openlocfilehash: eecb4dba39ff847515a4a312b7cb74698867c693
ms.sourcegitcommit: e4c33439642cf05682af7f28db1dbdb5cf273cc6
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/03/2020
ms.locfileid: "78247912"
---
# <a name="deployment-center-for-azure-kubernetes"></a>适用于 Azure Kubernetes 的部署中心

Azure DevOps 中的部署中心简化了为应用程序设置可靠的 Azure DevOps 管道的过程。 默认情况下，部署中心会配置一个 Azure DevOps 管道，以将应用程序更新部署到 Kubernetes 群集。 可以扩展默认的已配置 Azure DevOps 管道，并添加更丰富的功能：在部署之前获得批准、预配其他 Azure 资源、运行脚本、升级应用程序，甚至运行其他验证测试。

在本教程中，将：

> [!div class="checklist"]
> * 配置一个 Azure DevOps 管道，以将应用程序更新部署到 Kubernetes 群集。
> * 检查持续集成 (CI) 管道。
> * 检查持续交付 (CD) 管道。
> * 清理资源。

## <a name="prerequisites"></a>先决条件

* Azure 订阅。 可以通过 [Visual Studio Dev Essentials](https://visualstudio.microsoft.com/dev-essentials/) 免费获取一个。

* 一个 Azure Kubernetes 服务 (AKS) 群集。

## <a name="create-an-aks-cluster"></a>创建 AKS 群集

1. 登录到 [Azure 门户](https://portal.azure.com/)。

1. 在 Azure 门户中菜单栏的右侧选择[Cloud Shell](https://docs.microsoft.com/azure/cloud-shell/overview)选项。

1. 若要创建 AKS 群集，请运行以下命令：

    ```azurecli
    # Create a resource group in the South India location:

    az group create --name azooaks --location southindia

    # Create a cluster named azookubectl with one node.

    az aks create --resource-group azooaks --name azookubectl --node-count 1 --enable-addons monitoring --generate-ssh-keys
    ```

## <a name="deploy-application-updates-to-a-kubernetes-cluster"></a>将应用程序更新部署到 Kubernetes 群集

1. 转到你在上一部分创建的资源组。

1. 选择 AKS 群集，然后在左侧边栏选项卡中选择“部署中心(预览版)”。  选择“开始”。 

   ![设置](media/deployment-center-launcher/settings.png)

1. 选择代码位置并选择“下一步”。  然后选择当前支持的存储库之一： **[Azure Repos](https://docs.microsoft.com/azure/devops/repos/index?view=azure-devops)** 或 **GitHub**。

    Azure Repos 是可以帮助管理代码的一组版本控制工具。 不管软件项目是大还是小，都应尽早使用版本控制。

    - **Azure Repos**：从现有项目和组织中选择一个存储库。

        ![Azure Repos](media/deployment-center-launcher/azure-repos.gif)

    - **GitHub**：授权并选择 GitHub 帐户的存储库。

        ![GitHub](media/deployment-center-launcher/github.gif)


1. 部署中心将分析存储库并检测 Dockerfile。 若要更新 Dockerfile，可以编辑标识的端口号。

    ![应用程序设置](media/deployment-center-launcher/application-settings.png)

    如果存储库不包含 Dockerfile，系统会显示一条有关提交 Dockerfile 的消息。

    ![Dockerfile](media/deployment-center-launcher/dockerfile.png)

1. 选择现有的或新建一个容器注册表，然后选择“完成”。  管道会自动创建，并会在 [Azure Pipelines](https://docs.microsoft.com/azure/devops/pipelines/index?view=azure-devops) 中将某个生成排入队列。

    Azure Pipelines 是一项云服务，你可以用它来自动生成并测试代码项目，然后将该项目提供给其他用户。 Azure Pipelines 将持续集成和持续交付组合在一起，可以持续且一致地测试并生成代码，并将代码发送给任何目标。

    ![容器注册表](media/deployment-center-launcher/container-registry.png)

1. 选择用于查看当前管道的链接。

1. 部署完成后，你会看到成功日志。

    ![日志](media/deployment-center-launcher/logs.png)

## <a name="examine-the-ci-pipeline"></a>检查 CI 管道

部署中心会自动配置 Azure DevOps 组织的 CI/CD 管道。 可以浏览并自定义此管道。

1. 转到部署中心仪表板。  

1. 选择成功日志列表中的生成号，查看项目的生成管道。

1. 选择右上角的省略号 (...)。 菜单会显示多个选项，例如，将新生成排队、保留生成，以及编辑生成管道。 选择“编辑管道”。  

1. 在此窗格中，可以检查生成管道的不同任务。 该生成会执行各种任务，例如，从 Git 存储库收集源、创建映像、将映像推送到容器注册表，以及发布用于部署的输出。

1. 在管道的顶部，选择生成管道的名称。

1. 将生成管道名称更改为更具说明性的名称，选择“保存并排队”，然后选择“保存”。  

1. 在生成管道下，选择“历史记录”。  此窗格显示最近的生成更改的审核线索。 Azure DevOps 会监视对生成管道所做的任何更改，并允许进行版本比较。

1. 选择“触发器”。  可以在 CI 过程中包含或排除分支。

1. 选择“保留期”。  可以根据方案指定策略，以保留或删除多个生成。

## <a name="examine-the-cd-pipeline"></a>检查 CD 管道

部署中心会自动创建并配置 Azure DevOps 组织与 Azure 订阅之间的关系。 这些步骤涉及到设置 Azure 服务连接，以使用 Azure DevOps 对 Azure 订阅进行身份验证。 自动化过程还会创建一个发布管道，用于持续交付到 Azure。

1. 依次选择“管道”、“发布”。  

1. 若要编辑发布管道，请选择“编辑”。 

1. 从“项目”列表中选择“删除”。   在前述步骤中检查过的构造管道生成用于项目的输出。 

1. 在“删除”选项的右侧，选择“持续部署”触发器。   此发布管道有一个已启用的 CD 触发器，每次有新的生成项目可用时，此触发器就会运行部署。 也可禁用该触发器，这样，就需要手动执行部署。

1. 若要检查管道的所有任务，请选择“任务”。  发布过程将设置 Tiller 环境、配置 `imagePullSecrets` 参数、安装 Helm 工具并将 Helm 图表部署到 Kubernetes 群集。

1. 若要查看发布历史记录，请选择“查看发布”。 

1. 若要查看摘要，请选择“发布”。  选择任意阶段可浏览多个菜单，例如发布摘要、关联的工作项和测试。 

1. 选择“提交”。  此视图显示与此部署相关的代码提交。 请对发布进行比较，查看不同部署之间的提交差异。

1. 选择“日志”。  日志包含有用的部署信息，可以在部署期间和之后查看这些信息。

## <a name="clean-up-resources"></a>清理资源

不再需要创建的相关资源时，可将其删除。 请使用 DevOps Projects 仪表板上的“删除”功能。

## <a name="next-steps"></a>后续步骤

可以根据你的团队的需求修改这些生成和发布管道。 或者，可将此 CI/CD 模型用作其他管道的模板。
