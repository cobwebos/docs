---
title: 使用 Jenkins 和蓝/绿部署模式部署到 Azure Kubernetes 服务 (AKS)
description: 了解如何使用 Jenkins 和蓝/绿部署模式部署到 Azure Kubernetes 服务 (AKS)。
services: app-service\web
documentationcenter: ''
author: tomarcher
manager: jpconnock
editor: ''
ms.assetid: ''
ms.service: multiple
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: web
ms.date: 07/23/2018
ms.author: tarcher
ms.custom: jenkins
ms.openlocfilehash: 384681ae0ba212b485022ac81743528f96075ec8
ms.sourcegitcommit: d16b7d22dddef6da8b6cfdf412b1a668ab436c1f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/08/2018
ms.locfileid: "39716451"
---
# <a name="deploy-to-azure-kubernetes-service-aks-by-using-jenkins-and-the-bluegreen-deployment-pattern"></a>使用 Jenkins 和蓝/绿部署模式部署到 Azure Kubernetes 服务 (AKS)

Azure Kubernetes 服务 (AKS) 管理托管的 Kubernetes 环境，使用户能够快速、轻松地部署和管理容器化的应用程序。 不需要具备容器业务流程方面的专业知识。 AKS 还通过按需预配、升级和缩放资源，消除了正在进行的操作和维护的负担。 不需要使应用程序脱机。 有关 AKS 的详细信息，请参阅 [AKS 文档](/azure/aks/)。

蓝/绿部署是一种 Azure DevOps 持续交付模式，它依赖于在部署新（绿色）版本时保持现有（蓝色）版本的活动性。 通常，此模式采用负载均衡将不断增大的流量定向到绿色部署。 如果监视功能发现了事件，可将流量重新路由到仍在运行的蓝色部署。 有关持续交付的详细信息，请参阅[什么是持续交付](/azure/devops/what-is-continuous-delivery)。

本教程介绍如何执行以下任务：

> [!div class="checklist"]
> * 了解蓝/绿部署模式
> * 创建托管的 Kubernetes 群集
> * 运行示例脚本来配置 Kubernetes 群集
> * 手动配置 Kubernetes 群集
> * 创建并运行 Jenkins 作业

## <a name="prerequisites"></a>先决条件
- [GitHub 帐户](https://github.com)：需要使用一个 GitHub 帐户来克隆示例存储库。
- [Azure CLI 2.0](https://docs.microsoft.com/en-us/cli/azure/install-azure-cli?view=azure-cli-latest)：使用 Azure CLI 2.0 创建 Kubernetes 群集。
- [Chocolatey](https://chocolatey.org)：用于安装 kubectl 的包管理器。
- [kubectl](https://kubernetes.io/docs/tasks/tools/install-kubectl/)：用于针对 Kubernetes 群集运行命令的命令行接口。
- [jq](https://stedolan.github.io/jq/download/)：轻量命令行 JSON 处理程序。

## <a name="clone-the-sample-app-from-github"></a>从 GitHub 克隆示例应用。

GitHub 上的 Microsoft 存储库中提供了一个演示如何使用 Jenkins 和蓝/绿模式部署到 AKS 的示例应用。 在本部分，我们将创建 GitHub 中该存储库的分叉，并将应用克隆到本地系统。

1. 浏览到 [todo-app-java-on-azure](https://github.com/microsoft/todo-app-java-on-azure.git) 示例应用所在的 GitHub 存储库。

    ![Microsoft GitHub 存储库中的示例应用屏幕截图](./media/jenkins-aks-blue-green-deployment/github-sample-msft.png)

1. 选择页面右上角的“分叉”来创建存储库的分叉，然后遵照说明在 GitHub 帐户中创建该存储库的分叉。

    ![用于分叉的 GitHub 选项屏幕截图](./media/jenkins-aks-blue-green-deployment/github-sample-msft-fork.png)

1. 创建存储库的分叉后，会看到帐户名已更改为你的帐户名，同时有一条注释指出了存储库分叉的源位置 (Microsoft)。

    ![GitHub 帐户名称和注释的屏幕截图](./media/jenkins-aks-blue-green-deployment/github-sample-msft-forked.png)

1. 选择“克隆或下载”。

    ![用于克隆或下载存储库的 GitHub 选项屏幕截图](./media/jenkins-aks-blue-green-deployment/github-sample-clone.png)

1. 在“使用 HTTPS 克隆”窗口中，选择“复制”图标。

    ![用于将克隆 URL 复制到剪贴板的 GitHub 选项屏幕截图](./media/jenkins-aks-blue-green-deployment/github-sample-copy.png)

1. 打开终端或 Git Bash 窗口。

1. 将目录切换到用于存储存储库本地副本（复本）的所需位置。

1. 使用 `git clone` 命令克隆前面复制的 URL。

    ![Git Bash git clone 命令的屏幕截图](./media/jenkins-aks-blue-green-deployment/git-clone-command.png)

1. 按 Enter 键开始克隆过程。

    ![Git Bash git clone 命令结果的屏幕截图](./media/jenkins-aks-blue-green-deployment/git-clone-results.png)

1. 将目录切换到包含应用源复本的新建目录。

## <a name="create-and-configure-a-managed-kubernetes-cluster"></a>创建并配置托管的 Kubernetes 群集

在本部分执行以下步骤：

- 使用 Azure CLI 2.0 创建托管的 Kubernetes 群集。
- 了解如何使用设置脚本或通过手动方式设置群集。
- 创建 Azure 容器注册表服务的实例。

> [!NOTE]   
> AKS 目前以预览版提供。 有关为 Azure 订阅启用该预览版的详细信息，请参阅[快速入门：部署 Azure Kubernetes 服务 (AKS) 群集](/azure/aks/kubernetes-walkthrough#enabling-aks-preview-for-your-azure-subscription)。

### <a name="use-the-azure-cli-20-to-create-a-managed-kubernetes-cluster"></a>使用 Azure CLI 2.0 创建托管的 Kubernetes 群集
若要使用 [Azure CLI 2.0](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) 创建托管的 Kubernetes 群集，请务必使用 Azure CLI 2.0.25 或更高版本。

1. 登录到 Azure 帐户。 输入以下命令后，会收到有关如何完成登录的说明。 
    
    ```bash
    az login
    ```

1. 在上一步骤中运行 `az login` 命令时，会显示所有 Azure 订阅的列表（及其订阅 ID）。 此步骤设置默认的 Azure 订阅。 请将 &lt;your-subscription-id> 占位符替换为所需的 Azure 订阅 ID。 

    ```bash
    az account set -s <your-subscription-id>
    ```

1. 创建资源组。 请将 &lt;your-resource-group-name> 占位符替换为新资源组的名称，将 &lt;your-location> 占位符替换为位置。 命令 `az account list-locations` 显示所有 Azure 位置。 在 AKS 预览版中，并非所有位置都可用。 如果输入目前无效的位置，错误消息会列出可用的位置。

    ```bash
    az group create -n <your-resource-group-name> -l <your-location>
    ```

1. 创建 Kubernetes 群集。 请将 &lt;your-resource-group-name> 替换为在上一步骤中创建的资源组的名称，将 &lt;your-kubernetes-cluster-name> 替换为新群集的名称。 （此过程可能需要几分钟才能完成。）

    ```bash
    az aks create -g <your-resource-group-name> -n <your-kubernetes-cluster-name> --generate-ssh-keys --node-count 2
    ```

### <a name="set-up-the-kubernetes-cluster"></a>设置 Kubernetes 群集

可以使用前面克隆的示例中提供的设置脚本或者通过手动方式在 AKS 中设置蓝/绿部署。 本部分将介绍这两种方法。

#### <a name="set-up-the-kubernetes-cluster-via-the-sample-setup-script"></a>通过示例设置脚本设置 Kubernetes 群集
1. 编辑 **deploy/aks/setup/setup.sh** 文件，将以下占位符替换为环境的相应值： 

    - **&lt;your-resource-group-name>**
    - **&lt;your-kubernetes-cluster-name>**
    - **&lt;your-location>**
    - **&lt;your-dns-name-suffix>**

    ![bash 中 setup.sh 脚本的屏幕截图，其中突出显示了多个占位符](./media/jenkins-aks-blue-green-deployment/edit-setup-script.png)

1. 运行设置脚本。

    ```bash
    sh setup.sh
    ```

#### <a name="set-up-a-kubernetes-cluster-manually"></a>手动设置 Kubernetes 群集 
1. 将 Kubernetes 配置下载到 profile 文件夹。

    ```bash
    az aks get-credentials -g <your-resource-group-name> -n <your-kubernetes-cluster-name> --admin
    ```

1. 将目录切换到 **deploy/aks/setup** 目录。 

1. 运行以下 **kubectl** 命令，为公共终结点和两个测试终结点设置服务。

    ```bash
    kubectl apply -f  service-green.yml
    kubectl apply -f  test-endpoint-blue.yml
    kubectl apply -f  test-endpoint-green.yml
    ```

1. 更新公共和测试终结点的 DNS 名称。 创建 Kubernetes 群集时，还会创建采用 **MC_&lt;your-resource-group-name>_&lt;your-kubernetes-cluster-name>_&lt;your-location>** 命名模式的[附加资源组](https://github.com/Azure/AKS/issues/3)。

    在资源组中查找公共 IP。

    ![资源组中公共 IP 的屏幕截图](./media/jenkins-aks-blue-green-deployment/publicip.png)

    对于每个服务，请运行以下命令找到外部 IP 地址：
    
    ```bash
    kubectl get service todoapp-service
    ```
    
    使用以下命令更新相应 IP 地址的 DNS 名称：

    ```bash
    az network public-ip update --dns-name aks-todoapp --ids /subscriptions/<your-subscription-id>/resourceGroups/MC_<resourcegroup>_<aks>_<location>/providers/Microsoft.Network/publicIPAddresses/kubernetes-<ip-address>
    ```

    针对 `todoapp-test-blue` 和 `todoapp-test-green` 重复调用：

    ```bash
    az network public-ip update --dns-name todoapp-blue --ids /subscriptions/<your-subscription-id>/resourceGroups/MC_<resourcegroup>_<aks>_<location>/providers/Microsoft.Network/publicIPAddresses/kubernetes-<ip-address>

    az network public-ip update --dns-name todoapp-green --ids /subscriptions/<your-subscription-id>/resourceGroups/MC_<resourcegroup>_<aks>_<location>/providers/Microsoft.Network/publicIPAddresses/kubernetes-<ip-address>
    ```

    DNS 名称需在订阅中保持唯一。 为确保唯一性，可以使用 `<your-dns-name-suffix>`。

### <a name="create-an-instance-of-container-registry"></a>创建容器注册表的实例

1. 运行 `az acr create` 命令创建容器注册表的实例。 在下一部分，可以使用 `login server` 作为 Docker 注册表 URL。

    ```bash
    az acr create -n <your-registry-name> -g <your-resource-group-name>
    ```

1. 运行 `az acr credential` 命令显示容器注册表凭据。 记下 Docker 注册表用户名和密码，因为在下一部分需要用到。

    ```bash
    az acr credential show -n <your-registry-name>
    ```

## <a name="prepare-the-jenkins-server"></a>准备 Jenkins 服务器

本部分介绍如何使 Jenkins 服务器准备好运行一个适合用于测试的生成项目。 但是，应该使用 [Azure VM 代理](https://plugins.jenkins.io/azure-vm-agents)或 [Azure 容器代理](https://plugins.jenkins.io/azure-container-agents)运转 Azure 中的代理来运行生成项目。 有关详细信息，请参阅有关[在主节点上进行生成所造成的安全影响](https://wiki.jenkins.io/display/JENKINS/Security+implication+of+building+on+master)的 Jenkins 文章。

1. 部署 [Azure 上的 Jenkins 主节点](https://aka.ms/jenkins-on-azure)。

1. 通过 SSH 连接到服务器，并在运行生成项目的服务器上安装生成工具。
   
   ```bash
   sudo apt-get install git maven 
   ```
   
1. [安装 Docker](https://docs.docker.com/install/linux/docker-ce/ubuntu/#install-docker-ce)。 确保用户 `jenkins` 有权运行 `docker` 命令。

1. [安装 kubectl](https://kubernetes.io/docs/tasks/tools/install-kubectl/)。

1. [下载 jq](https://stedolan.github.io/jq/download/)。

1. 使用以下命令安装 jq：

   ```bash
   sudo apt-get install jq
   ```
   
1. 在 Jenkins 仪表板中执行以下步骤，以在 Jenkins 中安装插件：

    1. 选择“管理 Jenkins”>“管理插件”>“可用”。
    1. 搜索并安装 Azure 容器服务插件。

1. 添加用于管理 Azure 中的资源的凭据。 如果尚未安装“Azure 凭据”插件，请安装该插件。

1. 将 Azure 服务主体凭据添加为“Microsoft Azure 服务主体”类型。

1. 将 Azure Docker 注册表用户名和密码（已在“创建容器注册表的实例”部分中获取）添加为“用户名和密码”类型。

## <a name="edit-the-jenkinsfile"></a>编辑 Jenkinsfile

1. 在自己的存储库中转到 `/deploy/aks/`，并打开 `Jenkinsfile`。

2. 按如下所示更新该文件：

    ```groovy
    def servicePrincipalId = '<your-service-principal>'
    def resourceGroup = '<your-resource-group-name>'
    def aks = '<your-kubernetes-cluster-name>'

    def cosmosResourceGroup = '<your-cosmodb-resource-group>'
    def cosmosDbName = '<your-cosmodb-name>'
    def dbName = '<your-dbname>'

    def dockerRegistry = '<your-acr-name>.azurecr.io'
    ```
    
    更新容器注册表凭据 ID：
    
    ```groovy
    def dockerCredentialId = '<your-acr-credential-id>'
    ```

## <a name="create-the-job"></a>创建作业
1. 在“管道”类型中添加一个新作业。

1. 选择“管道” > “定义” > “来自 SCM 的管道脚本”。

1. 输入包含 &lt;your-forked-repo> 的 SCM 存储库 URL。

1. 输入 `deploy/aks/Jenkinsfile` 作为脚本路径。

## <a name="run-the-job"></a>运行作业

1. 验证是否可以成功在本地环境中运行项目。 请参阅[在本地计算机上运行项目](https://github.com/Microsoft/todo-app-java-on-azure/blob/master/README.md#run-it)。

1. 运行 Jenkins 作业。 首次运行该作业时，Jenkins 会将待办事项应用部署到蓝色环境（默认的非活动环境）。 

1. 若要验证该作业是否已运行，请转到以下 URL：
    - 公共终结点：`http://aks-todoapp<your-dns-name-suffix>.<your-location>.cloudapp.azure.com`
    - 蓝色终结点 - `http://aks-todoapp-blue<your-dns-name-suffix>.<your-location>.cloudapp.azure.com`
    - 绿色终结点 - `http://aks-todoapp-green<your-dns-name-suffix>.<your-location>.cloudapp.azure.com`

公共和蓝色测试终结点具有相同的更新内容，而绿色终结点显示默认的 tomcat 映像。 

如果多次运行生成项目，会循环使用蓝色和绿色部署。 换而言之，如果当前环境为蓝色，则作业将部署到绿色环境并对其进行测试。 如果测试时一切正常，则作业会更新应用程序的公共终结点，以将流量路由到绿色环境。

## <a name="additional-information"></a>其他信息

有关零停机时间部署的详细信息，请参阅此[快速入门模板](https://github.com/Azure/azure-quickstart-templates/tree/master/301-jenkins-aks-zero-downtime-deployment)。 

## <a name="clean-up-resources"></a>清理资源

不再需要本教程中创建的资源时，可将其删除。

```bash
az group delete -y --no-wait -n <your-resource-group-name>
```

## <a name="troubleshooting"></a>故障排除

如果 Jenkins 插件出现任何 bug，请在 [Jenkins JIRA](https://issues.jenkins-ci.org/) 中提出特定组件的问题。

## <a name="next-steps"></a>后续步骤

本教程已介绍如何使用 Jenkins 和蓝/绿部署模式部署到 AKS。 若要详细了解 Azure Jenkins 提供程序，请参阅“Azure 上的 Jenkins”站点。

> [!div class="nextstepaction"]
> [Azure 上的 Jenkins](/azure/jenkins/)