---
title: 使用 Azure 容器服务和 Swarm 的 CI/CD
description: 使用包含 Docker Swarm 的 Azure 容器服务、Azure 容器注册表和 Visual Studio Team Services 持续交付多容器 .NET Core 应用程序
services: container-service
author: jcorioland
manager: jeconnoc
ms.service: container-service
ms.topic: article
ms.date: 12/08/2016
ms.author: jucoriol
ms.custom: mvc
ms.openlocfilehash: ac3133ac093d578c89d24bddd1cc0a7c9588c2fd
ms.sourcegitcommit: d16b7d22dddef6da8b6cfdf412b1a668ab436c1f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/08/2018
ms.locfileid: "39714992"
---
# <a name="full-cicd-pipeline-to-deploy-a-multi-container-application-on-azure-container-service-with-docker-swarm-using-visual-studio-team-services"></a>用于通过 Visual Studio Team Services 在包含 Docker Swarm 的 Azure 容器服务中部署多容器应用程序的完整 CI/CD 管道

开发适用于云环境的新型应用程序时，最大的挑战之一是持续交付这些应用程序。 本文介绍如何使用包含 Docker Swarm 的 Azure 容器服务、Azure 容器注册表和 Visual Studio Team Services 生成与发布管理，实现完整的持续集成与持续部署 (CI/CD) 管道。

本文基于 [GitHub](https://github.com/jcorioland/MyShop/tree/acs-docs) 中提供的使用 ASP.NET Core 开发的一个简单应用程序。 该应用程序由四个不同的服务组成：三个 Web API 和一个 Web 前端：

![MyShop 示例应用程序](./media/container-service-docker-swarm-setup-ci-cd/myshop-application.png)

目的是使用 Visual Studio Team Services 在 Docker Swarm 群集中持续交付此应用程序。 下图详细演示了此持续交付管道：

![MyShop 示例应用程序](./media/container-service-docker-swarm-setup-ci-cd/full-ci-cd-pipeline.png)

下面是步骤的简要说明：

1. 将代码更改提交到源代码存储库（在本例中为 GitHub） 
1. GitHub 在 Visual Studio Team Services 中触发生成 
1. Visual Studio Team Services 获取最新的源版本，生成构成该应用程序的所有映像 
1. Visual Studio Team Services 将每个映像推送到使用 Azure 容器注册表服务创建的 Docker 注册表 
1. Visual Studio Team Services 触发新的发布操作 
1. 该版本在 Azure 容器服务群集主节点上使用 SSH 运行一些命令 
1. 群集上的 Docker Swarm 提取最新的映像版本 
1. 使用 Docker Compose 部署应用程序的新版本 

## <a name="prerequisites"></a>先决条件

在开始本教程之前，需要完成以下任务：

- [在 Azure 容器服务中创建 Swarm 群集](container-service-deployment.md)
- [连接 Azure 容器服务中的 Swarm 群集](../container-service-connect.md)
- [创建 Azure 容器注册表](../../container-registry/container-registry-get-started-portal.md)
- [创建 Visual Studio Team Services 帐户和团队项目](https://docs.microsoft.com/vsts/organizations/accounts/create-organization-msa-or-work-student)
- [将 GitHub 存储库分叉到 GitHub 帐户](https://github.com/jcorioland/MyShop/)

[!INCLUDE [container-service-swarm-mode-note](../../../includes/container-service-swarm-mode-note.md)]

还需要准备一台装有 Docker 的 Ubuntu（14.04 或 16.04）计算机。 在生成和发布过程中，Visual Studio Team Services 将使用此计算机。 创建此计算机的方法之一是使用 [Azure 市场](https://azure.microsoft.com/marketplace/partners/canonicalandmsopentech/dockeronubuntuserver1404lts/)中提供的映像。 

## <a name="step-1-configure-your-visual-studio-team-services-account"></a>步骤 1：配置 Visual Studio Team Services 帐户 

本部分将配置 Visual Studio Team Services 帐户。

### <a name="configure-a-visual-studio-team-services-linux-build-agent"></a>配置 Visual Studio Team Services Linux 生成代理

要创建 Docker 映像并从 Visual Studio Team Services 内部版本将这些映像推送到 Azure 容器注册表，需要注册 Linux 代理。 可以使用以下安装选项：

* [在 Linux 上部署代理](https://www.visualstudio.com/docs/build/admin/agents/v2-linux)

* [使用 Docker 运行 VSTS 代理](https://hub.docker.com/r/microsoft/vsts-agent)

### <a name="install-the-docker-integration-vsts-extension"></a>安装 Docker 集成 VSTS 扩展

Microsoft 提供了一个 VSTS 扩展，用于在生成和发布过程中操作 Docker。 
  [VSTS Marketplace](https://marketplace.visualstudio.com/items?itemName=ms-vscs-rm.docker) 中提供了此扩展。 单击“安装”，将此扩展添加到 VSTS 帐户：

![安装 Docker 集成](./media/container-service-docker-swarm-setup-ci-cd/install-docker-vsts.png)

系统会要求使用凭据连接到 VSTS 帐户。 

### <a name="connect-visual-studio-team-services-and-github"></a>连接 Visual Studio Team Services 和 GitHub

在 VSTS 项目与 GitHub 帐户之间建立连接。

1. 在 Visual Studio Team Services 项目中的工具栏上单击“设置”图标，并选择“服务”。

    ![Visual Studio Team Services - 外部连接](./media/container-service-docker-swarm-setup-ci-cd/vsts-services-menu.png)

1. 在左侧，单击“新建服务终结点” > “GitHub”。

    ![Visual Studio Team Services - GitHub](./media/container-service-docker-swarm-setup-ci-cd/vsts-github.png)

1. 要授权 VSTS 使用 GitHub 帐户，请单击“Authorize”，并遵循打开的窗口中所述的过程。

    ![Visual Studio Team Services - GitHub 授权](./media/container-service-docker-swarm-setup-ci-cd/vsts-github-authorize.png)

### <a name="connect-vsts-to-your-azure-container-registry-and-azure-container-service-cluster"></a>将 VSTS 连接到 Azure 容器注册表和 Azure 容器服务群集

在着手创建 CI/CD 管道之前的最后几个步骤是在 Azure 中配置容器注册表和 Docker Swarm 群集的外部连接。 

1. 在 Visual Studio Team Services 项目的“服务”设置中，添加一个“Docker 注册表”类型的服务终结点。 

1. 在打开的弹出窗口中，输入 Azure 容器注册表的 URL 和凭据。

    ![Visual Studio Team Services - Docker 注册表](./media/container-service-docker-swarm-setup-ci-cd/vsts-registry.png)

1. 对于 Docker Swarm 群集，请添加一个“SSH”类型的终结点。 然后输入 Swarm 群集的 SSH 连接信息。

    ![Visual Studio Team Services - SSH](./media/container-service-docker-swarm-setup-ci-cd/vsts-ssh.png)

现已完成所有配置。 在后续步骤中，将要创建用于生成应用程序并将其部署到 Docker Swarm 群集的 CI/CD 管道。 

## <a name="step-2-create-the-build-definition"></a>步骤 2：创建生成定义

本步骤将为 VSTS 项目设置生成定义，并为容器映像定义生成工作流

### <a name="initial-definition-setup"></a>初始定义设置

1. 要创建生成定义，请连接到 Visual Studio Team Services 项目，并单击“生成和发布”。 

1. 在“生成定义”部分中，单击“+ 新建”。 选择“空”模板。

    ![Visual Studio Team Services - 新建生成定义](./media/container-service-docker-swarm-setup-ci-cd/create-build-vsts.png)

1. 配置包含 GitHub 存储库源代码的新内部版本，选中“持续集成”，并选择 Linux 代理注册到的代理队列。 单击“创建”创建生成定义。

    ![Visual Studio Team Services - 创建生成定义](./media/container-service-docker-swarm-setup-ci-cd/vsts-create-build-github.png)

1. 在“生成定义”页上，首先打开“存储库”选项卡，将内部版本配置为使用在先决条件部分中创建的 MyShop 项目的分叉。 请确保选择 *acs-docs* 作为**默认分支**。

    ![Visual Studio Team Services - 生成存储库配置](./media/container-service-docker-swarm-setup-ci-cd/vsts-github-repo-conf.png)

1. 在“触发器”选项卡上，配置要在每次提交后触发的生成。 选择“持续集成”和“批量更改”。

    ![Visual Studio Team Services - 生成触发器配置](./media/container-service-docker-swarm-setup-ci-cd/vsts-github-trigger-conf.png)

### <a name="define-the-build-workflow"></a>定义生成工作流
后续步骤定义生成工作流。 需要为 *MyShop* 应用程序生成五个容器映像。 每个映像是使用项目文件夹中的 Dockerfile 生成的：

* ProductsApi
* 代理
* RatingsApi
* RecommandationsApi
* ShopFront

需要为每个映像添加两个 Docker 步骤，其中一个步骤用于生成映像，另一个用于将映像推送到 Azure 容器注册表中。 

1. 若要在生成工作流中添加步骤，请单击“+ 添加生成步骤”并选择“Docker”。

    ![Visual Studio Team Services - 添加生成步骤](./media/container-service-docker-swarm-setup-ci-cd/vsts-build-add-task.png)

1. 对于每个映像，请配置一个使用 `docker build` 命令的步骤。

    ![Visual Studio Team Services - Docker 生成](./media/container-service-docker-swarm-setup-ci-cd/vsts-docker-build.png)

    对于生成操作，请选择 Azure 容器注册表和“生成映像”操作，并选择定义每个映像的 Dockerfile。 将“生成上下文”设置为 Dockerfile 根目录，然后定义“映像名称”。 
    
    如上面的屏幕截图中所示，使用 Azure 容器注册表的 URI 作为映像名称的开头。 （还可以使用生成变量来参数化映像的标记，在本示例中为生成标识符。）

1. 对于每个映像，请配置另一个使用 `docker push` 命令的步骤。

    ![Visual Studio Team Services - Docker 推送](./media/container-service-docker-swarm-setup-ci-cd/vsts-docker-push.png)

    对于推送操作，请选择你的 Azure 容器注册表和“推送映像”操作，并输入在上一步骤中生成的**映像名称**。

1. 为每个映像（共五个）配置生成和推送步骤后，请在生成工作流中另外添加两个步骤。

    a. 一个命令行任务，使用 Bash 脚本将 docker-compose.yml 文件中出现的 BuildNumber 替换为当前生成 ID。请参阅以下屏幕截图中的详细信息。

    ![Visual Studio Team Services - 更新 Compose 文件](./media/container-service-docker-swarm-setup-ci-cd/vsts-build-replace-build-number.png)

    b. 一个可将已更新的 Compose 文件转换为生成项目，使其可在发布阶段使用的任务。 请参阅以下屏幕截图中的详细信息。

    ![Visual Studio Team Services - 发布 Compose 文件](./media/container-service-docker-swarm-setup-ci-cd/vsts-publish-compose.png) 

1. 单击“保存”并为生成定义命名。

## <a name="step-3-create-the-release-definition"></a>步骤 3：创建发布定义

在 Visual Studio Team Services 中可以[跨环境管理发布](https://www.visualstudio.com/team-services/release-management/)。 可以启用持续部署，确保顺利地将应用程序部署到不同的环境（例如开发、测试、预生产和生产）。 可以创建一个代表 Azure 容器服务 Docker Swarm 群集的新环境。

![Visual Studio Team Services - 发布到 ACS](./media/container-service-docker-swarm-setup-ci-cd/vsts-release-acs.png) 

### <a name="initial-release-setup"></a>初始发布设置

1. 若要创建发布定义，请单击“版本” > “+ 发布”

1. 若要配置项目源，请单击“项目” > “链接项目源”。 在此处，请将此新发布定义链接到在上一步骤中定义的生成项。 这样，便可以在发布过程中使用 docker-compose.yml 文件。

    ![Visual Studio Team Services - 发布项目](./media/container-service-docker-swarm-setup-ci-cd/vsts-release-artefacts.png) 

1. 要配置发布触发器，请单击“触发器”，并选择“持续部署”。 针对同一个项目源设置触发器。 此项设置可确保在成功完成生成后，立即开始新的发布。

    ![Visual Studio Team Services - 发布触发器](./media/container-service-docker-swarm-setup-ci-cd/vsts-release-trigger.png) 

### <a name="define-the-release-workflow"></a>定义发布工作流

发布工作流由添加的两个任务组成。

1. 请配置一个任务，以便使用前面配置的 SSH 连接将 compose 文件安全复制到 Docker Swarm 主节点上的 *deploy* 文件夹。 请参阅以下屏幕截图中的详细信息。

    ![Visual Studio Team Services - 发布 SCP](./media/container-service-docker-swarm-setup-ci-cd/vsts-release-scp.png)

1. 配置用于执行 bash 命令的另一个任务，以便在主节点上运行 `docker` 和 `docker-compose` 命令。 请参阅以下屏幕截图中的详细信息。

    ![Visual Studio Team Services - 发布 Bash](./media/container-service-docker-swarm-setup-ci-cd/vsts-release-bash.png)

    在主节点上执行的命令使用 Docker CLI 和 Docker-Compose CLI 来执行以下任务：

    - 登录到 Azure 容器注册表（使用“变量”选项卡中定义的三个生成变量）
    - 将 **DOCKER_HOST** 变量定义为使用 Swarm 终结点 (:2375)
    - 导航到前面的安全复制任务创建的、包含 docker-compose.yml 文件的 *deploy* 文件夹 
    - 执行可以提取新映像、停止服务、删除服务和创建容器的 `docker-compose` 命令。

    >[!IMPORTANT]
    > 如上面的屏幕截图中所示，将“在 STDERR 中显示失败”复选框保持未选中状态。 这是一项重要设置，因为 `docker-compose` 会在标准错误输出中列显一些诊断消息，例如正在停止或删除容器。 如果选中该复选框，则即使一切正常，Visual Studio Team Services 也会报告在发布期间出错。
    >
1. 保存此项新发布定义。


>[!NOTE]
>这种部署会造成一段时间的停机，因为需要停止旧服务并运行新服务。 执行蓝绿部署可避免这种情况。
>

## <a name="step-4-test-the-cicd-pipeline"></a>步骤 4. 测试 CI/CD 管道

完成配置后，便可以开始测试这个新的 CI/CD 管道。 最简单的测试方法是更新源代码，然后将更改提交到 GitHub 存储库。 推送代码后的几秒钟内，就能看到新的内部版本在 Visual Studio Team Services 中运行。 成功完成后，新的发布会被触发，在 Azure 容器服务群集上部署应用程序的新版本。

## <a name="next-steps"></a>后续步骤

* 有关使用 Visual Studio Team Services 的 CI/CD 的详细信息，请参阅 [VSTS Build overview](https://www.visualstudio.com/docs/build/overview)（VSTS 生成概述）。
