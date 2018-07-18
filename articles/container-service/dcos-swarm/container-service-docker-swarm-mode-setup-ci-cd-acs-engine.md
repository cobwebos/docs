---
title: 使用 Azure 容器服务引擎和 Swarm 模式的 CI/CD
description: 使用包含 Docker Swarm 模式的 Azure 容器服务引擎、Azure 容器注册表和 Visual Studio Team Services 持续交付多容器 .NET Core 应用程序
services: container-service
author: diegomrtnzg
manager: jeconnoc
ms.service: container-service
ms.topic: article
ms.date: 05/27/2017
ms.author: diegomrtnzg
ms.custom: mvc
ms.openlocfilehash: 01126f3eef988eb1787bafea92e7384aad1a703c
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/28/2018
ms.locfileid: "32179565"
---
# <a name="full-cicd-pipeline-to-deploy-a-multi-container-application-on-azure-container-service-with-acs-engine-and-docker-swarm-mode-using-visual-studio-team-services"></a>用于通过 Visual Studio Team Services 在使用 ACS 引擎和 Docker Swarm 模式的 Azure 容器服务中部署多容器应用程序的完整 CI/CD 管道

本文基于[用于通过 Visual Studio Team Services 在包含 Docker Swarm 的 Azure 容器服务中部署多容器应用程序的完整 CI/CD 管道](container-service-docker-swarm-setup-ci-cd.md)文档

现今开发适用于云环境的新型应用程序时，最大的挑战之一是持续交付这些应用程序。 本文将说明如何通过以下方式实现完整持续集成和部署 (CI/CD) 管道： 
* 使用 Docker Swarm 模式的 Azure 容器服务
* Azure 容器注册表
* Visual Studio Team Services

本文基于 [GitHub](https://github.com/jcorioland/MyShop/tree/docker-linux) 中提供的使用 ASP.NET Core 开发的一个简单应用程序。 该应用程序由四个不同的服务组成：三个 Web API 和一个 Web 前端：

![MyShop 示例应用程序](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/myshop-application.png)

目的是使用 Visual Studio Team Services 在 Docker Swarm 模式群集中持续交付此应用程序。 下图详细演示了此持续交付管道：

![MyShop 示例应用程序](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/full-ci-cd-pipeline.png)

下面是步骤的简要说明：

1. 将代码更改提交到源代码存储库（在本例中为 GitHub） 
2. GitHub 在 Visual Studio Team Services 中触发生成 
3. Visual Studio Team Services 获取最新的源版本，生成构成该应用程序的所有映像 
4. Visual Studio Team Services 将每个映像推送到使用 Azure 容器注册表服务创建的 Docker 注册表 
5. Visual Studio Team Services 触发新的发布操作 
6. 该版本在 Azure 容器服务群集主节点上使用 SSH 运行一些命令 
7. 群集上的 Docker Swarm 模式提取最新的映像版本 
8. 使用 Docker 堆栈部署应用程序的新版本 

## <a name="prerequisites"></a>先决条件

在开始本教程之前，需要完成以下任务：

- [使用 ACS 引擎在 Azure 容器服务中创建 Swarm 模式群集](https://github.com/Azure/azure-quickstart-templates/tree/master/101-acsengine-swarmmode)
- [连接 Azure 容器服务中的 Swarm 群集](../container-service-connect.md)
- [创建 Azure 容器注册表](../../container-registry/container-registry-get-started-portal.md)
- [创建 Visual Studio Team Services 帐户和团队项目](https://www.visualstudio.com/en-us/docs/setup-admin/team-services/sign-up-for-visual-studio-team-services)
- [将 GitHub 存储库分叉到 GitHub 帐户](https://github.com/jcorioland/MyShop/tree/docker-linux)

>[!NOTE]
> Azure 容器服务中的 Docker Swarm Orchestrator 使用旧的独立 Swarm。 目前，集成的 [Swarm 模式](https://docs.docker.com/engine/swarm/)（位于 Docker 1.12 及更高版本中）在 Azure 容器服务中不是受支持的 Orchestrator。 因此，我们将使用 [ACS 引擎](https://github.com/Azure/acs-engine/blob/master/docs/swarmmode.md)（社区提供的[快速入门模板](https://azure.microsoft.com/resources/templates/101-acsengine-swarmmode/)）或[Azure Marketplace](https://azuremarketplace.microsoft.com) 中的 Docker 解决方案。
>

## <a name="step-1-configure-your-visual-studio-team-services-account"></a>步骤 1：配置 Visual Studio Team Services 帐户 

本部分将配置 Visual Studio Team Services 帐户。 若要配置 VSTS 服务终结点，请在 Visual Studio Team Services 项目中的工具栏上单击“设置”图标，然后选择“服务”。

![打开服务终结点](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/services-vsts.PNG)

### <a name="connect-visual-studio-team-services-and-azure-account"></a>连接 Visual Studio Team Services 和 Azure 帐户

在 VSTS 项目与 Azure 帐户之间建立连接。

1. 在左侧，单击“新建服务终结点” > “Azure Resource Manager”。
2. 若要授权 VSTS 使用你的 Azure 帐户，请选择“订阅”，然后单击“确定”。

    ![Visual Studio Team Services - 授权 Azure](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/vsts-azure.PNG)

### <a name="connect-visual-studio-team-services-and-github"></a>连接 Visual Studio Team Services 和 GitHub

在 VSTS 项目与 GitHub 帐户之间建立连接。

1. 在左侧，单击“新建服务终结点” > “GitHub”。
2. 要授权 VSTS 使用 GitHub 帐户，请单击“Authorize”，并遵循打开的窗口中所述的过程。

    ![Visual Studio Team Services - GitHub 授权](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/vsts-github.png)

### <a name="connect-vsts-to-your-azure-container-service-cluster"></a>将 VSTS 连接到 Azure 容器服务群集

在着手创建 CI/CD 管道之前的最后几个步骤是在 Azure 中配置 Docker Swarm 群集的外部连接。 

1. 对于 Docker Swarm 群集，请添加一个“SSH”类型的终结点。 然后输入 Swarm 群集的 SSH 连接信息（主节点）。

    ![Visual Studio Team Services - SSH](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/vsts-ssh.png)

现已完成所有配置。 在后续步骤中，将要创建用于生成应用程序并将其部署到 Docker Swarm 群集的 CI/CD 管道。 

## <a name="step-2-create-the-build-definition"></a>步骤 2：创建生成定义

本步骤将为 VSTS 项目设置生成定义，并为容器映像定义生成工作流

### <a name="initial-definition-setup"></a>初始定义设置

1. 要创建生成定义，请连接到 Visual Studio Team Services 项目，并单击“生成和发布”。 在“生成定义”部分中，单击“+ 新建”。 

    ![Visual Studio Team Services - 新建生成定义](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/create-build-vsts.PNG)

2. 选择“空进程”。

    ![Visual Studio Team Services - 新建空生成定义](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/create-empty-build-vsts.PNG)

4. 然后单击“变量”选项卡并创建两个新变量：RegistryURL 和 AgentURL。 粘贴注册表和群集代理 DNS 的值。

    ![Visual Studio Team Services - 生成变量配置](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/vsts-build-variables.png)

5. 在“生成定义”页上，首先打开“触发器”选项卡，将内部版本配置为使用与在先决条件部分中创建的 MyShop 项目的分叉的持续集成。 然后选择“批更改”。 请确保选择 docker-linux 作为分支规范。

    ![Visual Studio Team Services - 生成存储库配置](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/vsts-github-repo-conf.PNG)


6. 最后，单击“选项”选项卡并将默认代理队列配置为“托管 Linux 预览”。

    ![Visual Studio Team Services - 主机代理配置](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/vsts-build-agent.png)

### <a name="define-the-build-workflow"></a>定义生成工作流
后续步骤定义生成工作流。 首先，需要配置代码的源。 若要执行此操作，请选择“GitHub”、你的“存储库”和“分支”(docker-linux)。

![Visual Studio Team Services - 配置代码源](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/vsts-source-code.png)

需要为 *MyShop* 应用程序生成五个容器映像。 每个映像是使用项目文件夹中的 Dockerfile 生成的：

* ProductsApi
* 代理
* RatingsApi
* RecommandationsApi
* ShopFront

每个映像都需要两个 Docker 步骤，其中一个步骤用于生成映像，另一个用于将映像推送到 Azure 容器注册表中。 

1. 若要在生成工作流中添加步骤，请单击“+ 添加生成步骤”并选择“Docker”。

    ![Visual Studio Team Services - 添加生成步骤](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/vsts-build-add-task.png)

2. 对于每个映像，请配置一个使用 `docker build` 命令的步骤。

    ![Visual Studio Team Services - Docker 生成](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/vsts-docker-build.png)

    对于生成操作，请选择你的 Azure 容器注册表和“生成映像”操作，然后选择定义每个映像的 Dockerfile。 将“工作目录”设置为 Dockerfile 根目录，然后定义“映像名称”并选择“包括最近标记”。
    
    映像名称必须采用以下格式： ```$(RegistryURL)/[NAME]:$(Build.BuildId)```。 用映像名称替换 [NAME]：
    - ```proxy```
    - ```products-api```
    - ```ratings-api```
    - ```recommendations-api```
    - ```shopfront```

3. 对于每个映像，请配置另一个使用 `docker push` 命令的步骤。

    ![Visual Studio Team Services - Docker 推送](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/vsts-docker-push.png)

    对于推送操作，请选择你的 Azure 容器注册表和“推送映像”操作，然后输入在上一步骤中生成的映像名称并选择“包括最新标记”。

4. 为每个映像（共 5 个）配置生成和推送步骤后，请在生成工作流中另外添加 3 个步骤。

   ![Visual Studio Team Services - 添加命令行任务](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/vsts-build-command-task.png)

      1. 一个命令行任务，使用 bash 脚本将 docker-compose.yml 文件中出现的 RegistryURL 替换为 RegistryURL 变量。 
    
          ```-c "sed -i 's/RegistryUrl/$(RegistryURL)/g' src/docker-compose-v3.yml"```

          ![Visual Studio Team Services - 用注册表 URL 更新 Compose 文件](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/vsts-build-replace-registry.png)

      2. 一个命令行任务，使用 bash 脚本将 docker-compose.yml 文件中出现的 AgentURL 替换为 AgentURL 变量。
  
          ```-c "sed -i 's/AgentUrl/$(AgentURL)/g' src/docker-compose-v3.yml"```

     3. 一个可将已更新的 Compose 文件转换为生成项目，使其可在发布阶段使用的任务。 请参阅以下屏幕截图中的详细信息。

         ![Visual Studio Team Services - 发布项目](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/vsts-publish.png) 

         ![Visual Studio Team Services - 发布 Compose 文件](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/vsts-publish-compose.png) 

5. 单击“保存和排队”以测试生成定义。

   ![Visual Studio Team Services - 保存和排队](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/vsts-build-save.png) 

   ![Visual Studio Team Services - 新队列](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/vsts-build-queue.png) 

6. 如果生成正确，则一定会显示此屏幕：

  ![Visual Studio Team Services - 生成成功](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/vsts-build-succeeded.png) 

## <a name="step-3-create-the-release-definition"></a>步骤 3：创建发布定义

在 Visual Studio Team Services 中可以[跨环境管理发布](https://www.visualstudio.com/team-services/release-management/)。 可以启用持续部署，确保顺利地将应用程序部署到不同的环境（例如开发、测试、预生产和生产）。 可以创建一个代表 Azure 容器服务 Docker Swarm 模式群集的环境。

![Visual Studio Team Services - 发布到 ACS](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/vsts-release-acs.png) 

### <a name="initial-release-setup"></a>初始发布设置

1. 若要创建发布定义，请单击“版本” > “+ 发布”

2. 若要配置项目源，请单击“项目” > “链接项目源”。 在此处，请将此新发布定义链接到在上一步骤中定义的生成项。 之后便可以在发布过程中使用 docker-compose.yml 文件。

    ![Visual Studio Team Services - 发布项目](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/vsts-release-artefacts.png) 

3. 要配置发布触发器，请单击“触发器”，并选择“持续部署”。 针对同一个项目源设置触发器。 此项设置可确保在成功完成生成后，开始新的发布。

    ![Visual Studio Team Services - 发布触发器](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/vsts-release-trigger.png) 

4. 若要配置发布变量，请单击“变量”并选择“+Variable”以使用注册表的信息创建 3 个新变量：docker.username、docker.password 和 docker.registry。 粘贴注册表和群集代理 DNS 的值。

    ![Visual Studio Team Services - 生成存储库配置](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/vsts-release-variables.png)

    >[!IMPORTANT]
    > 如上一个屏幕所示，请单击 docker.password 中的“锁定”复选框。 此设置对于限制密码很重要。
    >

### <a name="define-the-release-workflow"></a>定义发布工作流

发布工作流由添加的两个任务组成。

1. 请配置一个任务，以便使用前面配置的 SSH 连接将 compose 文件安全复制到 Docker Swarm 主节点上的 *deploy* 文件夹。 请参阅以下屏幕截图中的详细信息。
    
    源文件夹：```$(System.DefaultWorkingDirectory)/MyShop-CI/drop```

    ![Visual Studio Team Services - 发布 SCP](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/vsts-release-scp.png)

2. 配置用于执行 bash 命令的另一个任务，以便在主节点上运行 `docker` 和 `docker stack deploy` 命令。 请参阅以下屏幕截图中的详细信息。

    ```docker login -u $(docker.username) -p $(docker.password) $(docker.registry) && export DOCKER_HOST=:2375 && cd deploy && docker stack deploy --compose-file docker-compose-v3.yml myshop --with-registry-auth```

    ![Visual Studio Team Services - 发布 Bash](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/vsts-release-bash.png)

    在主节点上执行的命令使用 Docker CLI 和 Docker-Compose CLI 来执行以下任务：

    - 登录到 Azure 容器注册表（使用“变量”选项卡中定义的三个生成变量）
    - 将 **DOCKER_HOST** 变量定义为使用 Swarm 终结点 (:2375)
    - 导航到前面的安全复制任务创建的、包含 docker-compose.yml 文件的 *deploy* 文件夹 
    - 执行可以拉取新映像和创建容器的 `docker stack deploy` 命令。

    >[!IMPORTANT]
    > 如之前的屏幕截图中所示，将“在 STDERR 中显示失败”复选框保持未选中状态。 此设置可让我们完成发布过程，因为 `docker-compose` 会在标准错误输出中列显一些诊断消息，例如正在停止或删除容器。 如果选中该复选框，则即使一切正常，Visual Studio Team Services 也会报告在发布期间出错。
    >
3. 保存此项新发布定义。

## <a name="step-4-test-the-cicd-pipeline"></a>步骤 4：测试 CI/CD 管道

完成配置后，便可以开始测试这个新的 CI/CD 管道。 最简单的测试方法是更新源代码，然后将更改提交到 GitHub 存储库。 推送代码后的几秒钟内，就能看到新的内部版本在 Visual Studio Team Services 中运行。 成功完成后，将触发新的发布，并在 Azure 容器服务群集上部署应用程序的新版本。

## <a name="next-steps"></a>后续步骤

* 有关使用 Visual Studio Team Services 的 CI/CD 的详细信息，请参阅 [VSTS Build overview](https://www.visualstudio.com/docs/build/overview)（VSTS 生成概述）。
* 有关 ACS 引擎的详细信息，请参阅[ACS 引擎 GitHub 存储库](https://github.com/Azure/acs-engine)。
* 有关 Docker Swarm 模式的详细信息，请参阅[Docker Swarm 模式概述](https://docs.docker.com/engine/swarm/)。
