---
title: 教程 - 使用 Azure Pipelines 将 CI/CD 设置到 Azure VM
description: 本教程介绍如何使用基于 YAML 的 Azure 管道将 Node.js 应用的持续集成 (CI) 和持续部署 (CD) 设置到 Azure VM。
author: ushan
tags: azure-devops-pipelines
ms.assetid: ''
ms.service: virtual-machines-linux
ms.topic: tutorial
ms.tgt_pltfrm: azure-pipelines
ms.workload: infrastructure
ms.date: 1/3/2020
ms.author: ushan
ms.custom: devops
ms.openlocfilehash: 8fda8e3079084ad917ad5a7fcfc4f80a622e2d82
ms.sourcegitcommit: 5397b08426da7f05d8aa2e5f465b71b97a75550b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/19/2020
ms.locfileid: "76277241"
---
# <a name="tutorial-deploy-your-app-to-linux-virtual-machines-in-azure-using-azure-devops-services-and-azure-pipelines"></a>教程：使用 Azure DevOps Services 和 Azure Pipelines 将应用部署到 Azure 中的 Linux 虚拟机

持续集成 (CI) 和持续部署 (CD) 可形成一个管道，用于在每个代码提交后生成、发布和部署代码。 本文档包含与设置 CI/CD 管道相关联的步骤，以使用 Azure Pipelines 执行多计算机部署。

Azure Pipelines 提供了一组完整且功能完备的 CI/CD 自动化工具，以部署本地或任何云上的虚拟机。

在本教程中，你将设置基于 YAML 的 CI/CD 管道，以便将应用部署到 Linux 虚拟机作为资源的 Azure Pipelines [环境](https://docs.microsoft.com/azure/devops/pipelines/process/environments?view=azure-devops)中，其中每个虚拟机都充当 Web 服务器来运行应用。

学习如何：

> [!div class="checklist"]
> * 获取示例应用。
> * 创建基于 YAML 的 Azure Pipelines CI 管道，以生成示例应用。
> * 为 Azure 虚拟机创建 Azure Pipelines 环境
> * 创建 Azure Pipelines CD 管道。
> * 执行手动和 CI 触发的部署。

## <a name="before-you-begin"></a>开始之前

* 登录到 Azure DevOps Services 组织 (https://dev.azure.com/ )  。 
  你可以获取[免费 Azure DevOps Services 组织](https://go.microsoft.com/fwlink/?LinkId=307137&clcid=0x409&wt.mc_id=o~msft~vscom~home-vsts-hero~27308&campaign=o~msft~vscom~home-vsts-hero~27308)。

  > [!NOTE]
  > 有关详细信息，请参阅[连接到 Azure DevOps Services](https://docs.microsoft.com/azure/devops/organizations/projects/connect-to-projects?view=vsts)。

*  部署目标需用到 Linux 虚拟机。  有关详细信息，请参阅[使用 Azure CLI 创建和管理 Linux VM](https://docs.microsoft.com/azure/virtual-machines/linux/tutorial-manage-vm)。

*  为虚拟机开启入站端口 80。 有关详细信息，请参阅[使用 Azure 门户创建网络安全组](https://docs.microsoft.com/azure/virtual-network/tutorial-filter-network-traffic)。

## <a name="get-your-sample-app-code"></a>获取示例应用代码

如果 GitHub 中已有要部署的应用，则可以尝试为该代码创建管道。

但是，如果你是新用户，则可以使用我们的示例代码来获得更好的入门体验。 在这种情况下，请在 GitHub 中对此存储库创建分支：

#### <a name="javatabjava"></a>[Java](#tab/java)

```
https://github.com/spring-projects/spring-petclinic
```

> [!NOTE]
> Petclinic 是使用 [Maven](https://spring.io/guides/gs/maven/) 生成的 [Java Spring Boot](https://spring.io/guides/gs/spring-boot) 应用程序。

#### <a name="javascripttabjava-script"></a>[JavaScript](#tab/java-script)

```
https://github.com/azure-devops/fabrikam-node
```

> [!NOTE]
> Node.js 应用是通过 [Yeoman](https://yeoman.io/learning/index.html) 生成的。 它使用 Express、bower 和 grunt。 它还有一些 npm 包作为依赖项。
> 示例还包含一个用来设置 Nginx 并部署应用的脚本。 它在虚拟机上执行。 具体而言，脚本将执行以下操作：
> 1. 安装 Node、Nginx 和 PM2。
> 2. 配置 Nginx 和 PM2。
> 3. 启动 Node 应用。

* * * 

## <a name="prerequisites-for-the-linux-vm"></a>Linux VM 先决条件

上面提到的示例应用已在 Ubuntu 16.04 上经过测试，我们建议你将相同版本的 Linux VM 用于此快速入门。
根据用于应用的运行时堆栈，遵循下面所述的其他步骤。

#### <a name="javatabjava"></a>[Java](#tab/java)

- 若要部署基于 Java Spring Boot 和基于 Spring Cloud 的应用，请使用[此](https://azuremarketplace.microsoft.com/marketplace/apps/azul.azul-zulu8-ubuntu-1804)模板在 Azure 中创建 Linux VM，此模板可提供完全受支持的基于 OpenJDK 的运行时。
- 若要在 Tomcat 服务器上部署 Java servlet，请使用[此](https://azuremarketplace.microsoft.com/marketplace/apps/azul.azul-zulu8-ubuntu-1804) Azure 模板通过 Java 8 创建 Linux VM，并[将 Tomcat 9.x 配置为服务](https://tomcat.apache.org/tomcat-9.0-doc/setup.html)。
- 若要部署基于 Java EE 的应用，请使用 Azure 模板创建 [Linux VM + Java + WebSphere 9.x](https://azuremarketplace.microsoft.com/marketplace/apps/midvision.websphere-application-server-nde-90) 或 [Linux VM + Java + WebLogic 12.x](https://azuremarketplace.microsoft.com/marketplace/apps/Oracle.OracleWebLogicServer12cEnterprise) 或 [Linux VM + Java](https://azuremarketplace.microsoft.com/marketplace/apps/azul.azul-zulu8-ubuntu-1804) + WildFly/JBoss 14 

#### <a name="javascripttabjava-script"></a>[JavaScript](#tab/java-script)

若要安装 javascript 应用或 Node.js 应用，需要使用包含 Nginx Web 服务器的 Linux VM 来部署应用。
如果你没有包含 Nginx 的 Linux VM，请使用[此示例](/azure/virtual-machines/linux/quick-create-cli)中的步骤在 Azure 中创建一个。

* * * 

## <a name="create-an-azure-pipelines-environment-with-azure-virtual-machines"></a>通过 Azure 虚拟机创建 Azure Pipelines 环境

虚拟机可作为资源添加到[环境](https://docs.microsoft.com/azure/devops/pipelines/process/environments)中，并可面向多计算机部署。 环境中的部署历史记录视图提供从 VM 到管道再到提交的可跟踪性。

可以在“Pipelines”部分中的“环境”中心创建环境   。
1.  登录到 Azure DevOps 组织，并导航到你的项目。
2.  在项目中，导航到“Pipelines”页面  。 然后选择“环境”并单击“创建环境”   。 为环境指定“名称”（必需）和“说明”   。
3.  选择“虚拟机”作为要添加到环境中的“资源”，并单击“下一步”    。
4.  选择“操作系统 (Windows/Linux)”以及“复制 PS 注册脚本”  。 
5.  现在，在向此环境注册的每个目标 VM 上，从管理员 PowerShell 命令提示符运行复制的脚本。
    > [!NOTE]
    > - 已登录用户的“个人访问令牌”预先插入到脚本中，该脚本在当天到期，导致复制的脚本无法使用。
    > - 如果 VM 中已有正在运行的代理，请为“代理”提供一个唯一名称，以向环境注册。
6.  注册 VM 后，它将作为环境资源显示在环境的“资源”选项卡下。

    ![VMcreation](media/tutorial-deploy-vms-azure-pipelines/vm-creation.png)

7.  若要添加更多 VM，可以单击“添加资源”，然后将“虚拟机”选为资源，再次查看并复制该脚本。 对于要添加到此环境中的所有 VM，此脚本将保持不变。 
8.  每台计算机都与 Azure Pipelines 进行交互，以协调应用部署。

    ![VMresource_view](media/tutorial-deploy-vms-azure-pipelines/vm-resourceview.png)

9. 可以在交互式 PS 注册脚本中将标记添加到 VM，（或者）也可以单击“资源”视图中每个 VM 资源末尾的三个点，在资源视图中添加/删除相同的标记。

   借助分配的标记，可在部署作业中使用环境时限制特定虚拟机的部署。 每个标记最多为 256 个字符，但你可以使用的标记数量没有限制。

   ![VMtags](media/tutorial-deploy-vms-azure-pipelines/vm-tags.png)

* * * 

## <a name="define-your-ci-build-pipeline"></a>定义 CI 生成管道

你需要一个持续集成 (CI) 生成管道，用于发布 Web 应用程序，以及可在 Ubuntu 服务器上本地运行的部署脚本。 根据要使用的运行时设置 CI 生成管道。 

1. 登录到 Azure DevOps 组织，并导航到你的项目。

1. 在项目中，导航到“Pipelines”页面  。 然后选择“操作”以创建新的管道。

1. 首先选择“GitHub”作为源代码位置，完成向导的各个步骤  。

1. 可能会重定向到 GitHub 进行登录。 如果是这样，请输入 GitHub 凭据。

1. 存储库列表显示时，请选择所需的示例应用存储库。

1. Azure Pipelines 将分析存储库，并建议使用合适的管道模板。

#### <a name="javatabjava"></a>[Java](#tab/java)

选择“入门”模板，并复制下面的 YAML 代码片段，该代码片段可生成 Java 项目并使用 Apache Maven 运行测试  ：

```YAML
- job: Build
  displayName: Build Maven Project
  steps:
  - task: Maven@3
    displayName: 'Maven Package'
    inputs:
      mavenPomFile: 'pom.xml'
  - task: CopyFiles@2
    displayName: 'Copy Files to artifact staging directory'
    inputs:
      SourceFolder: '$(System.DefaultWorkingDirectory)'
      Contents: '**/target/*.?(war|jar)'
      TargetFolder: $(Build.ArtifactStagingDirectory)
  - upload: $(Build.ArtifactStagingDirectory)
    artifact: drop
```

有关更多指导，请按照[使用 Maven 生成 Java 应用](https://docs.microsoft.com/azure/devops/pipelines/ecosystems/java)中所述的步骤进行操作。

#### <a name="javascripttabjava-script"></a>[JavaScript](#tab/java-script)

选择“入门”模板，然后复制以下 YAML 代码片段，该代码片段使用 npm 生成常规 Node.js 项目  。

```YAML
- stage: Build
  displayName: Build stage
  jobs:  
  - job: Build
    displayName: Build
    - task: NodeTool@0
      inputs:
        versionSpec: '10.x'
      displayName: 'Install Node.js'
    - script: |
        npm install
        npm run build --if-present
        npm run test --if-present
      displayName: 'npm install, build and test'
    - task: ArchiveFiles@2
      displayName: 'Archive files'
      inputs:
        rootFolderOrFile: '$(System.DefaultWorkingDirectory)'
        includeRootFolder: false
        archiveType: zip
        archiveFile: $(Build.ArtifactStagingDirectory)/$(Build.BuildId).zip
        replaceExistingArchive: true
    - upload: $(Build.ArtifactStagingDirectory)/$(Build.BuildId).zip
      artifact: drop
```

有关更多指导，请按照[使用 gulp 生成 Node.js 应用](https://docs.microsoft.com/azure/devops/pipelines/ecosystems/javascript)中的步骤进行操作。

- 查看管道以了解其功能。 请确保所有默认输入都适用于你的代码。

- 选择“保存并运行”，再选择“直接提交到主分支”，然后再次选择“保存并运行”    。

- 开始新运行。 请等待运行完成。

* * * 

## <a name="define-cd-steps-to-deploy-to-the-linux-vm"></a>定义要部署到 Linux VM 的 CD 步骤

1. 编辑上面的管道，并使用以下 YAML 语法引用之前获取的环境和 VM 资源来包含[部署作业](https://docs.microsoft.com/azure/devops/pipelines/process/deployment-jobs)：

   ```YAML
   jobs:  
   - deployment: VMDeploy
     displayName: web
     environment:
       name:  <environment name>
       resourceType: VirtualMachine
       tags: web1
     strategy:
   ```
2. 可以从环境中选择特定的虚拟机集来接收部署，方法是指定为环境中的每个虚拟机定义的“标记”  。
[此处](https://docs.microsoft.com/azure/devops/pipelines/yaml-schema?view=azure-devops&tabs=schema#deployment-job) 是部署作业的完整 YAML 架构。

3. 可以指定`runOnce` 或 `rolling` 作为部署策略。 

   `runOnce` 是最简单的部署策略，其中所有生命周期挂钩（即 `preDeploy` `deploy`、`routeTraffic` 和 `postRouteTraffic`）都执行一次。 然后，执行 `on:` `success` 或 `on:` `failure`。

   下面是 `runOnce` 的 YAML 代码片段示例：
   ```YAML
   jobs:
   - deployment: VMDeploy
     displayName: web
     pool:
       vmImage: 'Ubuntu-16.04'
     environment:
       name: <environment name>
       resourceType: VirtualMachine
     strategy:
         runOnce:
           deploy:
             steps:
             - script: echo my first deployment
   ```

4. 下面是一个 YAML 代码片段示例，可用于定义每次迭代中最多更新 5 个目标的虚拟机的滚动策略。 `maxParallel` 将确定可以并行部署的目标数。 选择包括在任何时候必须保持可用的目标的绝对数量或百分比，不包括正在部署的目标。 它还用于确定部署过程中的成功和失败条件。

   ```YAML
   jobs: 
   - deployment: VMDeploy
     displayName: web
     environment:
       name: <environment name>
       resourceType: VirtualMachine
     strategy:
         rolling:
           maxParallel: 5  #for percentages, mention as x%
           preDeploy:
             steps:
             - download: current
               artifact: drop
             - script: echo initialize, cleanup, backup, install certs
           deploy:
             steps:
             - task: Bash@3
               inputs:
                 targetType: 'inline'
                 script: |
                   # Modify deployment script based on the app type
                   echo "Starting deployment script run"
                   sudo java -jar '$(Pipeline.Workspace)/drop/**/target/*.jar'
           routeTraffic:
             steps:
             - script: echo routing traffic
           postRouteTraffic:
             steps:
             - script: echo health check post-route traffic
           on:
             failure:
               steps:
               - script: echo Restore from backup! This is on failure
             success:
               steps:
               - script: echo Notify! This is on success
   ```

   每次运行此作业时，系统会根据你创建和注册 VM 的 `<environment name>` 环境记录部署历史记录。

## <a name="run-your-pipeline-and-get-traceability-views-in-environment"></a>运行管道并获取环境中的可跟踪性视图
环境的部署视图提供完整的提交和工作项可跟踪性，以及每个环境/资源的跨管道部署历史记录。

![VMDeployments_view](media/tutorial-deploy-vms-azure-pipelines/vm-deployments.png)
  
![VMjobs_view](media/tutorial-deploy-vms-azure-pipelines/vm-jobsview.png)

## <a name="next-steps"></a>后续步骤
- 可继续[自定义刚刚创建的管道](https://docs.microsoft.com/azure/devops/pipelines/customize-pipeline)。
- 若要了解可在 YAML 管道中执行的其他操作，请参阅 [YAML 架构参考](https://docs.microsoft.com/azure/devops/pipelines/yaml-schema)。
- 若要了解如何部署 LAMP（Linux、Apache、MySQL 和 PHP）堆栈，请继续学习下一个教程。

> [!div class="nextstepaction"]
> [部署 LAMP 堆栈](tutorial-lamp-stack.md)
