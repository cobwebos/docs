---
title: "使用 Azure 容器服务和 DC/OS 的 CI/CD | Microsoft Docs"
description: "如何完全自动构建和部署多容器 Docker 应用到运行 DC/OS 的 Azure 容器服务群集。"
services: container-service
documentationcenter: 
author: spboyer
manager: wpickett
editor: 
tags: acs, azure-container-service
keywords: "Docker, 容器, 微服务, Mesos, Azure"
ms.assetid: b16b767a-2eaa-418a-becc-8c032713a1f2
ms.service: container-service
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/14/2016
ms.author: johnsta
translationtype: Human Translation
ms.sourcegitcommit: bb1ca3189e6c39b46eaa5151bf0c74dbf4a35228
ms.openlocfilehash: 65fc37a1fd1d1d0149b98767117f8faafb5dcd2b
ms.lasthandoff: 03/18/2017


---

# <a name="continuous-integration-and-deployment-of-multi-container-docker-applications-to-azure-container-service"></a>持续集成和部署多容器 Docker 应用程序到 Azure 容器服务 
本教程将介绍如何完全自动构建和部署多容器 Docker 应用到运行 DC/OS 的 Azure 容器服务群集。 持续集成和部署 (CI/CD) 的优点已为人熟知，但在将容器集成到工作流中时有新的注意事项。 使用新的 Azure 容器注册表和 CLI 命令，设置一个可自定义的端到端的流。

## <a name="get-started"></a>入门
可在 OS X、Windows 或 Linux 上运行此演练。
- 需要一个 Azure 订阅。 如果没有，可以 [注册一个帐户](https://azure.microsoft.com/)。
- 安装 [Azure CLI 2.0](/cli/azure/install-az-cli2)。

## <a name="what-well-create"></a>要创建的内容
让我们来了解一下该应用某些关键的方面，以及我们要设置的部署流：
* **该应用程序由多个服务组成**。 要定义应用中的服务，需要在单独的容器中运行 Docker 资产、Dockerfile 和 docker-compose.yml。 这可确保应用中的各部分可独立进行扩展，且可在不同的编程语言和框架中编写每个服务。 可在一个或多个 Git 源存储库中托管该应用的代码（工具目前支持 GitHub 和 Visual Studio Team Services）。

* 应用在 **配置有 DC/OS 的 ACS 群集**中运行。 容器 Orchestrator 可管理群集的运行状况，并确保所需容器实例数保持运行。 

* **完全自动且无故障时间的容器映像构建及部署**过程。 我们希望团队中的开发人员“git 推送”到分支，这回自动触发集成过程。 也就是说，构建容器并标记容器映像，在每个容器上运行测试并将这些映像推送到 Docker 私有注册表。 新映像会从此处自动部署到 ACS 群集上的共享预生产环境，以供进一步测试。

* **将发布从一个环境升级至另一环境**，例如开发 -> 测试 -> 过渡 -> 产品。 每一次升级至下游环境时，无需重新生成容器映像来确保部署的映像与之前环境中测试的映像相同。 此过程采用了不可变服务的概念，并减小了产品中存在漏检错误的可能性。

* 为了最有效地利用 ACS 群集中的计算资源，我们使用相同的群集来运行完全容纳了构建和部署步骤的构建任务。 该群集还托管了多个开发/测试/生产环境。


## <a name="create-an-azure-container-service-cluster-configured-with-dcos"></a>创建配置有 DC/OS Azure 容器服务群集

>[!IMPORTANT]
> 调用 `az acs create` 时，创建一个传送 SSH 公共密钥文件的安全群集进行传送。 可以通过 Azure CLI 2.0 生成密钥并使用 `--generate-ssh-keys` 选项同时传送密钥，或者可使用 `--ssh-key-value` 选项将路径传送至密钥（Linux 上默认的位置为 `~/.ssh/id_rsa.pub`，Windows 上为 `%HOMEPATH%\.ssh\id_rsa.pub`，但可更改位置）。
<!---Loc Comment: What do you mean by "you pass your SSH public key file to pass"? Thank you.--->
> 若要在 Linux 上创建 SSH 公钥和私钥文件，请参阅[在 Linux 和 Mac 上创建 SSH 密钥](../virtual-machines/virtual-machines-linux-mac-create-ssh-keys.md?toc=%2fazure%2fcontainer-services%2ftoc.json)。 
> 若要在 Windows 上创建 SSH 公钥和私钥文件，请参阅[在 Windows 上创建 SSH 密钥](../virtual-machines/virtual-machines-linux-ssh-from-windows.md?toc=%2fazure%2fcontainer-services%2ftoc.json)。 

1. 首先，在终端窗口键入 [az login](/cli/azure/#login) 命令，使用 Azure CLI 登录到你的 Azure 订阅： 

    `az login`

1. 使用 [az group create](/cli/azure/group#create) 创建放置群集的资源组：
    
    `az group create --name myacs-rg --location westus`

    你可能想要指定最近的 [Azure 数据中心区域](https://azure.microsoft.com/regions)。 

1. 使用 [az acs create](/cli/azure/acs#create) 创建具有默认设置的 ACS，并将路径传送到公共 SSH 密钥文件： 

    ```azurecli
    az acs create \
    --resource-group myacs-rg 
    --name myacs \
    --dns-prefix myacs \
    --ssh-key-value ~/.ssh/id_rsa.pub
    ```
    
此步骤需要几分钟，因此你可随意继续阅读。  `acs create` 命令会返回有关新创建的群集的信息（或者可使用 `az acs list` 在订阅中列出 ACS 群集）。 有关 ACS 配置的更多选项，请参阅[了解有关创建和配置 ACS 群集的详细信息](container-service-deployment.md)。

## <a name="set-up-sample-code"></a>设置示例代码
创建群集时，可以设置要部署到 ACS 的示例代码。

1. [Fork](https://help.github.com/articles/fork-a-repo/) 示例 GitHub 存储库，以便拥有自己的副本：[https://github.com/azure-samples/container-service-dotnet-continuous-integration-multi-container.git](https://github.com/azure-samples/container-service-dotnet-continuous-integration-multi-container.git)。 该应用实质上是“hello world”的一个多容器版本。
1. 在自己的 GitHub 帐户中创建了分叉后，请在计算机上本地克隆该存储库：

    ```bash
    git clone  https://github.com/your-github-account/container-service-dotnet-continuous-integration-multi-container.git
    cd container-service-dotnet-continuous-integration-multi-container
    ```
    
下面更详细地了解每个代码：
* `/service-a` 是基于 Angular.js 的 web 应用，带有 Node.js 后端。
* `/service-b` 是一种.NET Core 服务，由 `service-a` 通过 REST 调用。
* `service-a` 和 `service-b` 的目录中都包含 `Dockerfile`其中分别描述了基于 Node.js 和基于.NET Core 的容器映像。 
* `docker-compose.yml` 声明已构建和部署的服务集。
* 除了 `service-a` 和 `service-b` 以外，名为 `cache` 的第三项服务还运行可供 `service-a` 使用的 Redis 缓存。 `cache` 与前两个服务与不同，不同之处在于源存储库中没有供其使用的代码。 相反，我们从 Docker Hub 中提取一个预制的 `redis:alpine` 映像并将它部署到 ACS。
* `/service-a/server.js` 包含代码，其中 `service-a` 调用 `service-b` 和 `cache`。 请注意，`service-a` 代码通过 `service-b` 和 `cache` 在 `docker-compose.yml` 中的命名方式对二者进行引用。 如果我们通过 `docker-compose` 在本地计算机上运行这些服务，Docker 会确保所有服务都已正确联网，可按名称相互进行查找。 与本地运行相比，通过负载均衡网络在群集环境中运行这些服务会更加复杂。 值得高兴的是，Azure CLI 设置了一个 CI/CD 流，可确保此直接服务发现代码可继续在 ACS 中按原样运行。 

    ![多容器示例应用概述](media/container-service-setup-ci-cd/multi-container-sample-app-overview.PNG)

## <a name="set-up-continuous-integration-and-deployment"></a>设置持续集成和部署
1. 请确保 ACS 群集已准备就绪：运行 [az acs list](/cli/azure/acs#list) 并确认是否已列出 ACS 群集。 （注意：ACS 必须运行 DC/OS 1.8 或更高版本。）

1. [创建 GitHub 个人访问令牌](https://help.github.com/articles/creating-an-access-token-for-command-line-use/)，至少授予它 `repo` 作用域。  不要忘记将令牌复制到剪贴板，因为会在下一个命令用到它 （它会在 GitHub 存储库上设置一个 [webhook](https://help.github.com/articles/about-webhooks/)）。 

1. 将当前目录设置为克隆的源存储库根目录，并使用刚刚创建的 _&lt;GitHubPersonalAccessToken&gt; 创建一个生成和发布管道：
    
    `cd container-service-dotnet-continuous-integration-multi-container`

    ```azurecli
    az container release create \
    --target-name myacs \
    --target-resource-group myacs-rg \
    --remote-access-token <GitHubPersonalAccessToken>
    ```

    其中 `--target-name` 是 ACS 群集的名称，`--target-resource-group` 是 ACS 群集资源组的名称。

首次运行时，此命令可能需要一分左右的时间才能完成。 完成后，会返回有关构建和发布其创建的管道的重要信息：
* `sourceRepo`：为源存储库配置了 [webhook](https://help.github.com/articles/about-webhooks/)，这样无论何时向其推送该源代码，都会自动触发生成和发布管道。  
* `vstsProject`：[Visual Studio Team Services](https://www.visualstudio.com/team-services/) (VSTS) 已配置为驱动工作流（ACS 中容器内实际运行的生成和部署任务）。 如果想要使用特定 VSTS 帐户和项目，可以使用 `--vsts-account-name` 和 `--vsts-project-name` 参数进行定义。
* `buildDefinition`：定义针对每个生成运行的任务。 会针对每个在 docker-compose.yml 中定义的服务生成容器映像，然后将其推送到 Docker 容器注册表。 
* `containerRegistry`：Azure 容器注册表是一项运行 Docker 容器注册表的托管服务。 使用默认名称创建新的 Azure 容器注册表或者通过 `--registry-name` 参数指定 Azure 容器注册表名称。
* `releaseDefinition`：定义针对每个部署运行的任务。 从容器注册表中拉取在 docker-compose.yml 中定义的服务的容器映像，并将其部署到 ACS 群集。 默认情况下，会创建三个环境：开发、测试，和生产。 每次成功完成生成时，会默认将发布定义配置为自动部署到“开发”。 可手动将一个发布升级为“测试”或“生产”，而无需重新生成。 可在 VSTS 中自定义默认流。 
* `containerService`：目标 ACS 群集（必须运行 DC/OS 1.8）。


以下代码片段为需要输入的示例命令，前提是已存在名为 `myregistry` 的 Azure 容器注册表。 使用 VSTS 帐户在 `myvstsaccount.visualstudio.com` 中创建和生成发布定义，以及现有 VSTS 项目 `myvstsproject`：
        
```azurecli
az container release create \
--target-name myacs \
--target-resource-group myacs-rg \
--registry-name myregistry \
--vsts-account-name myvstsaccount \
--vsts-project-name myvstsproject \
--remote-access-token <GitHubPersonalAccessToken>
```

## <a name="view-deployment-pipeline-progress"></a>查看部署管道进度
创建管道后，会自动启动首次生成和部署。 每次将代码推送到源存储库都会触发后续生成。 打开浏览器，转到生成定义或发布定义 URL，即可检查生成和/或发布进度。

运行此命令可以找到与 ACS 群集关联的发布定义 URL：

```azurecli
az container release list \
--target-name myacs \
--target-resource-group myacs-rg
``` 

![VSTS 生成](media/container-service-setup-ci-cd/vsts-build.PNG)

VSTS 的屏幕截图，其中显示了多容器应用的 CI 结果

![VSTS 发布](media/container-service-setup-ci-cd/vsts-release.PNG)

附有多个环境的 VSTS docker-compose 发布

## <a name="view-the-application"></a>查看应用程序
此时，应用程序已部署到共享的开发环境，且处于非公开状态。 与此同时，使用 DC/OS 仪表板查看和管理服务并[创建通向与 DC/OS 相关的终结点的 SSH 通道](https://azure.microsoft.com/documentation/articles/container-service-connect/)或运行 Azure CLI 提供的方便命令。

> [!IMPORTANT]
> 首次部署时，在进行下一步之前请确认 VSTS 发布已成功部署。

> [!NOTE]
> 仅适用于 Windows：需要设置 [Pageant](http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html) 以完成此部分。
> 
>* 启动 PuttyGen 并加载用于创建 ACS 群集的 SSH 私钥 (%homepath%\id_rsa)。
>* 在同一文件夹中将 SSH 私钥另存为 `id_rsa.ppk`。
>* 启动 Pageant - 它将开始运行并在右下角系统托盘中显示一个图标。
>* 右键单击系统托盘图标，然后选择“添加密钥”。
>* 添加 `id_rsa.ppk` 文件。
> 
> 

1. 使用 Azure CLI 方便命令打开 ACS 群集的 DC/OS 仪表板：
    
    `az acs dcos browse -g myacs-rg -n myacs`

    * `-g` 是目标 ACS 群集的资源组名称
    * `-n` 是目标 ACS 群集的名称。
    * 可能会提示输入本地帐户密码，因为此命令需要管理员权限。 该命令会创建通向 DC/OS 终结点的 SSH 隧道，将默认浏览器打开到该终结点，并暂时配置浏览器的 Web 代理。 

    > [!TIP]
    > 如果需要查找 ACS 群集的名称，则可以通过运行 `az acs list` 列出订阅中的所有 ACS 群集。 

1. 在 DC/OS 仪表板的左侧导航菜单上单击“服务”([http://localhost/#/services](http://localhost/#/services))。 在名为“开发”（根据 VSTS 发布定义中的环境命名）的根文件夹下对通过管道部署的服务进行分组。 

![Marathon UI](media/container-service-setup-ci-cd/marathon-ui.png)

可在 DC/OS 仪表板中执行许多有用的操作

* 跟踪每个服务的部署状态
* 查看 CPU 和内存要求
* 查看日志
* 扩展每个服务的实例数

**查看 service-a 的 Web 应用程序**：从“开发”根文件夹开始，然后深入文件夹层次结构直到到达 `service-a`。 此视图列出了正在运行的 `service-a` 任务（或容器实例）。

![service a](media/container-service-setup-ci-cd/service-a.png)

单击任务打开其视图，然后单击其可用的终结点之一。

![service a 任务](media/container-service-setup-ci-cd/service-a-task.PNG)

简单的 Web 应用调用 `service-a`，其调用 `service-b`，并返回一个“hello world”消息。 Redis 每次生成请求时，计数器即会递增。

![service a Web 应用](media/container-service-setup-ci-cd/service-a-web-app.PNG)

### <a name="optional-reaching-a-service-from-the-command-line"></a>（可选）从命令行访问服务
如果想要通过 curl 从命令行访问服务：

1. 运行 `az acs dcos browse --verbose -g myacs-rg -n myacs`，并在输入密码后记下“在 <ip-address> 上运行的代理：<port>”这一行。
1. 在的新终端窗口中键入：

    `export http_proxy=http://<web-proxy-service-ip>:<portnumber>`

    例如： `export http_proxy=http://127.0.0.1:55405`

1. 现在可针对服务终结点使用 curl `curl http://service-url`，从 Marathon UI 导航到服务终结点时，会看到 `service-url` 地址。 若要从命令行取消设置 http_proxy 变量，请键入 `unset http_proxy`。
 

## <a name="scale-services"></a>缩放服务
在 DC/OS 仪表板中缩放服务。
1. 导航到“开发”子文件夹中的应用程序。
1. 将鼠标悬停在 `service-b`，单击齿轮图标，然后选择“缩放”。

    ![“操作”菜单](media/container-service-setup-ci-cd/marathon-ui-action-menu.PNG)

1. 将数目增加到3，然后单击“缩放服务”。

    ![缩放服务](media/container-service-setup-ci-cd/marathon-ui-scale-service.png)

1. 导航回到正在运行的 Web 应用，并重复单击“复述”按钮。 请注意，`service-b` 调用开始跨主机名集合进行轮循，同时 `service-a` 的单个实例继续报告相同的主机。   

## <a name="promote-a-release-to-downstream-environments-without-rebuilding-container-images"></a>将发布升级到下游环境而无需重新生成容器映像
VSTS 发布管道默认设置三个环境：开发、测试，和生产。 目前我们已部署到了“开发”。 了解如何将发布升级到下一步的下游环境（测试）中，而无需重新生成容器映像。 此工作流可确保正在部署的映像与之前环境中测试的映像完全相同，采用了不可变服务的概念，并减小了产品中存在漏检错误的可能性。

1. 在 VSTS web UI 中，导航到“发布”

    ![VSTS“发布”菜单](media/container-service-setup-ci-cd/vsts-releases-menu.PNG)

1. 打开最新发布。

1. 在发布定义菜单栏上，单击“部署”，然后选择“测试”作为下一步要部署的环境，启动新部署，重新使用以前部署到“开发”的相同映像。 如果按照详细部署信息进行操作，请单击“日志”。

    ![VSTS 升级发布](media/container-service-setup-ci-cd/vsts-promote-release.PNG)

成功部署到“测试”后，Marathon UI 中会出现名为“测试”的新根文件夹，其中包含针对该环境正在运行的服务。 

![DC/OS 中每个环境的子文件夹](media/container-service-setup-ci-cd/marathon-ui-dev-test-environments.png)

## <a name="trigger-a-new-build-and-deployment"></a>触发新生成和部署
我们来模拟一下，如果团队中的开发人员向源存储库推送了代码更改，会出现什么情况。

1. 返回到代码编辑器中，打开 `service-a/public/index.html`。 
1. 修改此代码行：

    `<h2>Server Says</h2>`

    将其更改为与此类似的代码：

    `<h2>Server Says Hello</h2>`

1. 保存该文件，然后将代码更改提交并推送到源存储库。

    ```bash
    git commit -am 'updated title'
    git push
    ```

提交后，会自动启动新生成，以及要部署到“开发”的新发布。 决定将某特定发布升级到该环境前，下游环境中的服务（“测试”或“生产”）保持不变。

如果在 VSTS 中打开生成定义，会出现类似于以下的内容： 

![由 git 推送触发的新生成](media/container-service-setup-ci-cd/new-build.png)



## <a name="expose-public-endpoint-for-production"></a>公开生产的公共终结点

1. 将下面的 yaml 代码添加到源存储库根文件夹中名为 `docker-compose.env.production.yml` 的新文件。 此操作会添加一个标签，使公共终结点向 `service-a` 公开。 
    
    ```yaml
    version: "2"
    services:
      service-a:
        labels:
          com.microsoft.acs.dcos.marathon.vhost: "<FQDN, or custom domain>"
    ```

    * 对于标签的值，可指定 ACS 代理的完全限定域名 (FQDN) 的 URL，或指定自定义域（例如，app.contoso.com）的 URL。 若要查找 ACS 代理的 FQDN，请运行命令 `az acs list`，并检查 `agentPoolProfiles.fqdn` 的属性。 例如，`myacsagents.westus.cloudapp.azure.com`。
    * 示例应用默认侦听端口 80，对于那些让其 Docker 应用程序侦听其他端口（例如 `port 8080` 或 `443`）的用户，需将端口号附加到 FQDN。 例如，`myacsagents.westus.cloudapp.azure.com:8080`。 但是，尝试从外部访问应用程序时，需要在端口 80 处查询它。
    * 按照文件名的约定 docker-compose.env.*environment-name*.yml，这些设置只会影响命名的环境（在此案例中，环境名为“生产”）。 检查 VSTS 中的发布定义、将每个环境的部署任务设置为从根据此约定命名的 docker-compose 文件进行读取。

1. 提交该文件并将其推送到主源存储库，以启动另一个生成。

    ```bash
    git add .
    git commit -am "expose public port for service-a"
    git push
    ```

1. 等到更新已生成并部署到“开发”后，将其升级到“测试”，然后升级到“生产”。 （通过此教程，你可以直接部署到“生产”，但最好是练习部署到下一下游环境。）

1. （可选）对于 vhost，**如果指定了自定义域**（例如，app.contoso.com），请在域提供程序的设置中添加一条 DNS 记录。 登录到域提供程序的管理 UI，并添加一条如下 DNS 记录：

    * 类型：CNAME
    * 主机：你的自定义域，例如 app.contoso.com
    * 回答：ACS 代理 FQDN，例如，myacsagents.westus.cloudapp.azure.com
    * TTL（可选）：有时，域提程序可使你编辑 TTL。 较低的值会使 DNS 记录更新更快地传播。   

1. 发布部署到“生产”后，所有人都可访问该版本。 打开浏览器，转到为 `com.microsoft.acs.dcos.marathon.vhost` 标签指定的 URL。 （注意：预生产环境的发布仍为私有）。

## <a name="summary"></a>摘要
祝贺你！ 你已了解如何使用 DC/OS 创建 ACS 群集，以及为多容器应用设置完全自动和容器化的生成和部署管道。

了解后续步骤：
* **缩放 VSTS 代理。** 如果运行生成和发布任务时需要更大吞吐量，则可以增加 VSTS 代理的实例数。 在 DC/OS 仪表板中导航到“服务”，打开 VSTS 代理文件夹，并尝试缩放 VSTS 代理实例数。
* **集成单元测试。** 此 GitHub 存储库显示如何在容器中运行单元测试和集成测试，并将其包含在生成任务中：[https://github.com/mindaro/sample-app](https://github.com/mindaro/sample-app)。 
    * 提示：请查看储存库中的这些文件：`service-a/unit-tests.js``service-a/service-tests.js``docker-compose.ci.unit-tests.yml` 和 `docker-compose.ci.service-tests.yml`。

## <a name="clean-up"></a>清理
若要限制与此教程相关的计算费用，请运行以下命令，并记下与 ACS 群集相关的部署管道资源：

```azurecli    
az container release list --resource-name myacs --resource-group myacs-rg
```

删除 ACS 群集：
1. 登录到 [Azure 门户](https://portal.azure.com)
1. 查找包含 ACS 群集的资源组。
1. 打开资源组的边栏选项卡 UI，然后单击边栏选项卡命令栏中的“删除”。

删除 Azure 容器注册表：在 Azure 门户中，搜索 Azure 容器注册表，并将其删除。 

[Visual Studio Team Services 帐户为前五位用户提供免费的基本访问级别](https://azure.microsoft.com/en-us/pricing/details/visual-studio-team-services/)，但你可以删除生成和发布定义。

删除 VSTS 生成定义：
        
1. 在浏览器中打开生成定义 URL，然后单击“生成定义”链接（位于当前正在查看的生成定义名称旁）。
2. 单击想要删除的生成定义旁的操作菜单，然后选择“删除定义”

`![删除 VSTS 生成定义](media/container-service-setup-ci-cd/vsts-delete-build-def.png) 

删除 VSTS 发布定义：

1. 在浏览器中打开发布定义 URL。
2. 在左侧的生成定义列表中，单击想要删除的生成定义旁的下拉列表，然后选择“删除”。

`![删除 VSTS 发布定义](media/container-service-setup-ci-cd/vsts-delete-release-def.png)

