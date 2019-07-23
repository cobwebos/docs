---
title: 使用 Azure 应用服务中的部署中心
description: Azure DevOps 中的部署中心简化了为应用程序设置可靠的 Azure DevOps 管道的过程
ms.author: puagarw
ms.manager: douge
ms.prod: devops
ms.technology: devops-cicd
ms.topic: tutorial
ms.date: 07/12/2019
author: pulkitaggarwl
monikerRange: vsts
ms.openlocfilehash: 8d1e467906b74c97c8b4f4e5c14af0814dd098f7
ms.sourcegitcommit: 470041c681719df2d4ee9b81c9be6104befffcea
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/12/2019
ms.locfileid: "67861511"
---
# <a name="deployment-center-launcher"></a>部署中心启动器

Azure DevOps 中的部署中心简化了为应用程序设置可靠的 DevOps 管道的过程。 默认情况下，请配置一个 DevOps 管道，将应用程序更新部署到 Kubernetes 群集。 可以扩展默认的已配置 DevOps 管道，添加更丰富的 DevOps 功能 - 在部署之前进行审批、预配其他的 Azure 资源、运行脚本、升级应用程序或者甚至运行其他验证测试。

在本教程中，将：

> [!div class="checklist"]
> * 配置一个 DevOps 管道，将应用程序更新部署到 K8s 群集
> * 检查 CI 管道
> * 检查 CD 管道
> * 清理资源

## <a name="prerequisites"></a>先决条件

* Azure 订阅。 可以通过 [Visual Studio Dev Essentials](https://visualstudio.microsoft.com/dev-essentials/) 免费获取一个

* Azure Kubernetes 服务 (AKS) 群集

## <a name="create-aks-cluster"></a>创建 AKS 群集

1. 登录到 [Azure 门户](https://portal.azure.com/)

1. 选择 Azure 门户右上角菜单上的“Cloud Shell”[](https://docs.microsoft.com/azure/cloud-shell/overview)按钮。

1. 若要创建 AKS 群集，请运行以下命令。

    ```cmd
    # The below command creates Resource Group in the south india location

    az group create --name azooaks --location southindia

    # The below command creates a cluster named azookubectl with one node. 

    az aks create --resource-group azooaks --name azookubectl --node-count 1 --enable-addons monitoring --generate-ssh-keys
    ```

## <a name="deploy-application-updates-to-k8s-cluster"></a>将应用程序更新部署到 K8s 群集

1. 导航到上面创建的资源组。

1. 选择 AKS 群集，在左边栏选项卡设置下单击“部署中心(预览)”。 单击“入门”。

   ![设置](media/deployment-center-launcher/settings.png)

1. 选择代码位置，然后单击“下一步”。 目前支持的存储库为 **[Azure Repos](https://docs.microsoft.com/azure/devops/repos/index?view=azure-devops)** 和 **GitHub**。 可以根据存储库选择情况执行以下步骤。

    Azure Repos 是一组版本控制工具，可以用来管理代码。 不管软件项目是大还是小，都应尽快使用版本控制。

    - **Azure Repos**：从现有项目和组织中选择一个存储库。

        ![Azure Repos](media/deployment-center-launcher/azure-repos.gif)

    - **GitHub**：授权并选择 GitHub 帐户的存储库。

        ![GitHub](media/deployment-center-launcher/github.gif)


1. 我们将分析存储库并检测你的 Dockerfile。 若要更新它，可以编辑标识的端口号。

    ![应用程序设置](media/deployment-center-launcher/application-settings.png)

    如果存储库不包含 Dockerfile，系统会显示一条用于提交它的消息。 

    ![Dockerfile](media/deployment-center-launcher/dockerfile.png)

1. 选择或创建现有容器注册表，然后单击“完成”。 管道会自动创建，并会在 [Azure Pipelines](https://docs.microsoft.com/azure/devops/pipelines/index?view=azure-devops) 中将一个生成排队。

    Azure Pipelines 是一项云服务，你可以用它来自动生成并测试代码项目，然后将该项目提供给其他用户。 Azure Pipelines 将持续集成 (CI) 和持续交付 (CD) 组合在一起，可以持续且一致地测试并生成代码，并将代码发送给任何目标。

    ![容器注册表](media/deployment-center-launcher/container-registry.png)

1. 单击用于查看当前管道的链接。

1. 可以看到部署完成后就会显示的成功日志。

    ![日志](media/deployment-center-launcher/logs.png)

## <a name="examine-the-ci-pipeline"></a>检查 CI 管道

部署中心自动配置 Azure DevOps 组织的 CI/CD 管道。 可以浏览并自定义此管道。 

1. 转到部署中心的仪表板。  

1. 单击成功日志列表中的生成号，查看项目的生成管道。 

1. 单击右上角的省略号 (...)。 菜单会显示多个选项，例如，将新生成排队、保留生成，以及编辑生成管道。 选择“编辑管道”。 

1. 在此窗格中，可以检查生成管道的不同任务。 该生成会执行各种任务，例如，从 Git 存储库收集源、创建映像、将映像推送到容器注册表，以及发布用于部署的输出。

1. 在生成管道的顶部，选择生成管道的名称。

1. 将生成管道名称更改为更具说明性的名称，选择“保存并排队”，然后选择“保存”。

1. 在生成管道下，选择“历史记录”。 此窗格显示最近的生成更改的审核线索。 Azure DevOps 会监视对生成管道所做的任何更改，并允许进行版本比较。

1. 选择“触发器”。 可以选择在 CI 过程中包括或排除分支。

1. 选择“保留”。 可以根据方案指定策略，保留或删除多个生成。

## <a name="examine-the-cd-pipeline"></a>检查 CD 管道

部署中心会自动创建并配置从 Azure DevOps 组织部署到 Azure 订阅所需执行的步骤。 这些步骤包括设置 Azure 服务连接，以便使用 Azure DevOps 对 Azure 订阅进行身份验证。 自动化还会创建一个发布管道，该管道提供到 Azure 的 CD。

1. 选择“管道”，然后选择“发布”。

1. 若要编辑发布管道，请单击“编辑”。

1. 从“项目”中选择“删除”。 在前述步骤中检查过的构造管道生成用于项目的输出。 

1. 在“删除”图标的右侧，选择“持续部署”触发器。 此发布管道有一个已启用的 CD 触发器，每次有新的生成项目可用时，此触发器就会运行部署。 也可选择禁用此触发器，这样一来，部署就需要手动执行。

1. 若要检查管道的所有任务，请单击“任务”。 此发布可设置 Tiller 环境、配置 imagePullSecrets、安装 Helm 工具并将 Helm 图表部署到 K8s 群集。

1. 若要查看发布历史记录，请单击“查看发布”。 

1. 若要查看摘要，请单击“发布”。 单击任意阶段即可浏览多个菜单，例如发布摘要、关联的工作项和测试。 

1. 选择“提交”。 此视图显示与此部署相关的代码提交。 请对发布进行比较，查看不同部署之间的提交差异。

1. 选择“日志”。 日志包含有用的部署信息。 可以在部署期间和之后查看日志。

## <a name="clean-up-resources"></a>清理资源

不再需要创建的相关资源时，可将其删除。 请使用 DevOps Projects 仪表板上的“删除”功能。

## <a name="next-steps"></a>后续步骤

可以选择根据所在团队的需求修改这些生成和发布管道。 也可以将此 CI/CD 模式用作其他管道的模板。 本教程介绍了如何：

> [!div class="checklist"]
> * 配置一个 DevOps 管道，将应用程序更新部署到 K8s 群集
> * 检查 CI 管道
> * 检查 CD 管道
> * 清理资源
