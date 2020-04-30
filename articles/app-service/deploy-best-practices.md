---
title: 部署最佳做法
description: 了解部署到 Azure App Service 的关键机制。 查找特定于语言的建议和其他注意事项。
keywords: azure 应用服务，web 应用，部署，部署，管道，生成
author: jasonfreeberg
ms.assetid: bb51e565-e462-4c60-929a-2ff90121f41d
ms.topic: article
ms.date: 07/31/2019
ms.author: jafreebe
ms.openlocfilehash: 4dd959d75fd582d787e68db4a415a4a694b9cda8
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/28/2020
ms.locfileid: "81770693"
---
# <a name="deployment-best-practices"></a>部署最佳实践

每个开发团队都有独特的要求，这使得在任何云服务上都难以实现有效的部署管道。 本文介绍了部署到应用服务的三个主要组件：部署源、生成管道和部署机制。 本文还介绍了特定语言堆栈的一些最佳实践和提示。

## <a name="deployment-components"></a>部署组件

### <a name="deployment-source"></a>部署源

部署源是应用程序代码的位置。 对于生产应用，部署源通常是由版本控制软件（例如[GitHub、BitBucket 或 Azure Repos](deploy-continuous-deployment.md)）托管的存储库。 对于开发和测试方案，部署源可以是[本地计算机上的项目](deploy-local-git.md)。 应用服务还支持[OneDrive 和 Dropbox 文件夹](deploy-content-sync.md)作为部署源。 尽管云文件夹可以轻松地开始使用应用服务，但通常不建议将此源用于企业级的生产应用程序。 

### <a name="build-pipeline"></a>生成管道

确定部署源后，下一步是选择生成管道。 生成管道从部署源读取源代码，并执行一系列步骤（如编译代码、缩小 HTML 和 JavaScript、运行测试和打包组件）以使应用程序处于可运行状态。 生成管道执行的特定命令取决于你的语言堆栈。 这些操作可在 Azure Pipelines 的生成服务器上执行，或在本地执行。

### <a name="deployment-mechanism"></a>部署机制

部署机制是用于将生成的应用程序放入 web 应用程序的 */home/site/wwwroot 中*目录的操作。 */Wwwroot*目录是由 web 应用的所有实例共享的已装载存储位置。 当部署机制将应用程序放在此目录中时，实例会收到同步新文件的通知。 应用服务支持以下部署机制：

- Kudu 终结点： [Kudu](https://github.com/projectkudu/kudu/wiki)是一种开源开发人员生产力工具，它作为 Windows 应用服务中的单独进程运行，并作为 Linux 应用服务中的第二个容器运行。 Kudu 处理连续部署并提供用于部署的 HTTP 终结点，如 zipdeploy。
- FTP 和 WebDeploy：使用您的[站点或用户凭据](deploy-configure-credentials.md)，可以[通过 FTP](deploy-ftp.md)或 WebDeploy 上传文件。 这些机制不会经过 Kudu。  

部署工具（例如 Azure Pipelines、Jenkins 和编辑器插件）使用这些部署机制之一。

## <a name="use-deployment-slots"></a>使用部署槽位

请尽可能在部署新的生产版本时使用[部署槽位](deploy-staging-slots.md)。 使用标准应用服务计划层或更好的应用程序层时，可将应用部署到过渡环境，验证更改，以及执行冒烟测试。 准备就绪后，可以交换过渡和生产槽。 交换操作得到预热必要的辅助角色实例，以匹配生产规模，从而消除停机。

### <a name="continuously-deploy-code"></a>连续部署代码

如果你的项目已指定用于测试、QA 和过渡的分支，则每个分支都应持续部署到过渡槽。 （这称为[Gitflow 设计](https://www.atlassian.com/git/tutorials/comparing-workflows/gitflow-workflow)。）这使您的利益干系人可以轻松地评估和测试已部署的分支。 

永远不应为生产槽启用持续部署。 相反，应将生产分支（通常为 master）部署到非生产槽。 准备好释放基本分支后，将其交换到生产槽中。 交换到生产中（而不是部署到生产环境）可防止停机，并使你能够通过再次交换回滚更改。 

![槽使用情况视觉对象](media/app-service-deploy-best-practices/slot_flow_code_diagam.png)

### <a name="continuously-deploy-containers"></a>连续部署容器

对于 Docker 或其他容器注册表中的自定义容器，请将映像部署到过渡槽并交换到生产中，以防止停机。 自动化比代码部署更复杂，因为必须将映像推送到容器注册表，并更新 webapp 上的图像标记。

对于想要部署到槽的每个分支，设置自动化，在每次提交到分支时执行以下操作。

1. **生成并标记映像**。 作为生成管道的一部分，用 git 提交 ID、时间戳或其他可识别信息标记映像。 最好不要使用默认的 "最新" 标记。 否则，很难跟踪当前部署的代码，这使得调试变得更加困难。
1. **推送标记的映像**。 生成并标记映像后，管道会将映像推送到容器注册表。 在下一步中，部署槽位将从容器注册表拉取标记的映像。
1. **用新的图像标记更新部署槽位**。 此属性更新时，站点将自动重新启动并请求新的容器映像。

![槽使用情况视觉对象](media/app-service-deploy-best-practices/slot_flow_container_diagram.png)

下面是适用于常见自动化框架的示例。

### <a name="use-azure-devops"></a>使用 Azure DevOps

应用服务通过部署中心为容器[提供内置持续交付](deploy-continuous-deployment.md)。 导航到[Azure 门户](https://portal.azure.com/)中的应用，然后在 "**部署**" 下选择 "**部署中心**"。 按照说明选择存储库和分支。 这会配置 DevOps 生成和发布管道，以便在将新提交推送到所选分支时自动生成、标记和部署容器。

### <a name="use-github-actions"></a>使用 GitHub 操作

还可以[通过 GitHub 操作](containers/deploy-container-github-action.md)自动化容器部署。  以下工作流文件将使用提交 ID 构建并标记容器，并将其推送到容器注册表，并使用新的映像标记更新指定的站点槽。

```yaml
name: Build and deploy a container image to Azure Web Apps

on:
  push:
    branches:
    - <your-branch-name>

jobs:
  build-and-deploy:
    runs-on: ubuntu-latest
    
    steps:
    - uses: actions/checkout@master

    -name: Authenticate using a Service Principal
      uses: azure/actions/login@v1
      with:
        creds: ${{ secrets.AZURE_SP }}

    - uses: azure/container-actions/docker-login@v1
      with:
        username: ${{ secrets.DOCKER_USERNAME }}
        password: ${{ secrets.DOCKER_PASSWORD }}

    - name: Build and push the image tagged with the git commit hash
      run: |
        docker build . -t contoso/demo:${{ github.sha }}
        docker push contoso/demo:${{ github.sha }}

    - name: Update image tag on the Azure Web App
      uses: azure/webapps-container-deploy@v1
      with:
        app-name: '<your-webapp-name>'
        slot-name: '<your-slot-name>'
        images: 'contoso/demo:${{ github.sha }}'
```

### <a name="use-other-automation-providers"></a>使用其他自动化提供程序

前面列出的步骤适用于其他自动化实用程序，例如 CircleCI 或 Travis CI。 但是，您需要使用 Azure CLI 在最后一个步骤中使用新的图像标记来更新部署槽位。 若要在自动化脚本中使用 Azure CLI，请使用以下命令生成服务主体。

```shell
az ad sp create-for-rbac --name "myServicePrincipal" --role contributor \
   --scopes /subscriptions/{subscription}/resourceGroups/{resource-group} \
   --sdk-auth
```

在脚本中，使用`az login --service-principal`登录，提供主体的信息。 然后，可以使用`az webapp config container set`设置容器名称、标记、注册表 URL 和注册表密码。 下面是一些有用的链接，可用于构造容器 CI 过程。

- [如何登录到 Circle CI 上的 Azure CLI](https://circleci.com/orbs/registry/orb/circleci/azure-cli) 

## <a name="language-specific-considerations"></a>特定于语言的注意事项

### <a name="java"></a>Java

使用 Kudu [zipdeploy/](deploy-zip.md) API 部署 JAR 应用程序，并使用[WARDEPLOY/](deploy-zip.md#deploy-war-file) for WAR 应用。 如果你使用的是 Jenkins，则可以在部署阶段直接使用这些 Api。 有关详细信息，请参阅[此文章](../jenkins/execute-cli-jenkins-pipeline.md)。

### <a name="node"></a>节点

默认情况下，Kudu 执行节点应用程序（`npm install`）的生成步骤。 如果使用的是生成服务（例如 Azure DevOps），则不需要 Kudu 内部版本。 若要禁用 Kudu 生成，请创建一个应用设置`SCM_DO_BUILD_DURING_DEPLOYMENT`，并将值设置`false`为。

### <a name="net"></a>.NET 

默认情况下，Kudu 执行 .NET 应用程序（`dotnet build`）的生成步骤。 如果使用的是生成服务（例如 Azure DevOps），则不需要 Kudu 内部版本。 若要禁用 Kudu 生成，请创建一个应用设置`SCM_DO_BUILD_DURING_DEPLOYMENT`，并将值设置`false`为。

## <a name="other-deployment-considerations"></a>其他部署注意事项

### <a name="local-cache"></a>本地缓存

Azure 应用服务内容存储在 Azure 存储中，作为内容共享持续提供。 但是，某些应用程序只需要高性能的只读内容存储，其可在高可用性下运行。 这些应用程序可以受益于使用[本地缓存](overview-local-cache.md)。 对于内容管理站点（例如 WordPress），不建议使用本地缓存。

始终将本地缓存与[部署槽位](deploy-staging-slots.md)一起使用，以防止停机。 有关将这些功能一起使用的信息，请参阅[此部分](overview-local-cache.md#best-practices-for-using-app-service-local-cache)。

### <a name="high-cpu-or-memory"></a>高 CPU 或内存

如果你的应用服务计划使用超过90% 的可用 CPU 或内存，则基础虚拟机在处理你的部署时可能会遇到问题。 发生这种情况时，请暂时向上缩放实例计数以执行部署。 完成部署后，可以将实例计数返回到它以前的值。

有关最佳实践的详细信息，请访问[应用服务诊断](https://docs.microsoft.com/azure/app-service/overview-diagnostics)，以了解特定于资源的可操作最佳方案。

- 在[Azure 门户](https://portal.azure.com)中导航到 Web 应用。
- 在左侧导航栏中单击 "**诊断和解决问题**"，这将打开应用服务诊断。
- 选择**最佳方案**主页磁贴。
- 若要查看应用的当前状态，请单击 "**可用性最佳实践 & 性能**" 或 "**最佳实践**" 以查看应用的当前状态。

你还可以使用此链接为你的资源直接打开应用服务诊断： `https://ms.portal.azure.com/?websitesextension_ext=asd.featurePath%3Ddetectors%2FParentAvailabilityAndPerformance#@microsoft.onmicrosoft.com/resource/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Web/sites/{siteName}/troubleshoot`。
