---
title: 教程 - 使用 Jenkins 从 GitHub 部署到 Azure Kubernetes 服务 (AKS)
description: 设置 Jenkins 以便从 GitHub 持续集成 (CI) 和持续部署 (CD) 到适用于 Java Web 应用的 Azure Kubernetes 服务 (AKS)
services: container-service
author: zr-msft
ms.author: zarhoads
ms.topic: article
ms.date: 01/09/2019
ms.openlocfilehash: eb48a8558aab6c7a832efe45650686d9df0d7426
ms.sourcegitcommit: 5a71ec1a28da2d6ede03b3128126e0531ce4387d
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/26/2020
ms.locfileid: "77624743"
---
# <a name="tutorial-deploy-from-github-to-azure-kubernetes-service-aks-with-jenkins-continuous-integration-and-deployment"></a>教程：使用 Jenkins 持续集成和部署从 GitHub 部署到 Azure Kubernetes 服务 (AKS)

本教程通过设置 Jenkins 中的持续集成 (CI) 和持续部署 (CD)，将 GitHub 中的一个示例应用部署到 [Azure 容器服务 (AKS)](/azure/aks/intro-kubernetes) 群集。 这样一来，通过将提交内容推送到 GitHub 以更新应用时，Jenkins 将自动运行一个新的容器内部版本，将容器映像推送到 Azure 容器注册表 (ACR)，然后在 AKS 中运行应用。 

在本教程中，你将完成以下任务：

> [!div class="checklist"]
> * 将示例 Azure 投票应用部署到 AKS 群集。
> * 创建一个基本 Jenkins 项目。
> * 设置可供 Jenkins 与 ACR 交互的凭据。
> * 创建用于自动执行生成操作的 Jenkins 生成作业和 GitHub Webhook。
> * 测试 CI/CD 管道，基于 GitHub 代码提交来更新 AKS 中的应用程序。

## <a name="prerequisites"></a>必备条件

若要完成本教程，需要准备好以下各项：

- 基本了解 Kubernetes、Git、CI/CD 和容器映像

- [AKS 群集](../aks/kubernetes-walkthrough.md)和使用[AKS 群集凭据](/cli/azure/aks#az-aks-get-credentials)配置的 `kubectl`。

- [Azure 容器注册表（acr）注册表](../container-registry/container-registry-get-started-azure-cli.md)、acr 登录服务器名称和配置为[使用 ACR 注册表进行身份验证](../aks/cluster-container-registry-integration.md)的 AKS 群集。

- 已安装并配置 Azure CLI 2.0.46 或更高版本。 运行  `az --version` 即可查找版本。 如果需要安装或升级，请参阅 [安装 Azure CLI](/cli/azure/install-azure-cli)。

- 开发系统上[安装的 Docker](https://docs.docker.com/install/)

- 在开发系统上安装的 GitHub 帐户、 [github 个人访问令牌](https://help.github.com/articles/creating-a-personal-access-token-for-the-command-line/)和 Git 客户端

- 如果您提供自己的 Jenkins 实例而不是此示例脚本化的方法来部署 Jenkins，则 Jenkins 实例将需要[安装和配置 Docker](https://docs.docker.com/install/) ，并[kubectl](https://kubernetes.io/docs/tasks/tools/install-kubectl/)。

## <a name="prepare-your-app"></a>准备应用程序

本文使用示例 Azure 投票应用程序，其中包含托管在一个或多个 Pod 中的 Web 接口，以及另一个托管 Redis 的 Pod（用作临时数据存储）。 集成 Jenkins 和 AKS 以执行自动部署前，请先手动准备 Azure 投票应用程序，并将它部署到 AKS 群集。 此手动部署版本是示例应用程序的第一版，可便于了解应用程序的实际效果。

> [!NOTE]
> 示例 Azure 投票应用程序使用计划在 Linux 节点上运行的 Linux pod。 本文中概述的流程还适用于在 Windows Server 节点上计划的 Windows Server pod。

为示例应用程序的以下 GitHub 存储库创建分支：[https://github.com/Azure-Samples/azure-voting-app-redis](https://github.com/Azure-Samples/azure-voting-app-redis)。 若要将存储库分叉到自己的 GitHub 帐户，请选择右上角的“分叉”按钮。

将此分支克隆到开发系统。 克隆此存储库时，请务必使用分支 URL：

```console
git clone https://github.com/<your-github-account>/azure-voting-app-redis.git
```

更改为已克隆分支的目录：

```console
cd azure-voting-app-redis
```

若要创建示例应用程序所需的容器映像，请结合使用 docker-compose.yaml 文件和 `docker-compose`：

```console
docker-compose up -d
```

此时，系统拉取所需的基础映像，并生成应用程序容器。 然后，可以使用[docker images](https://docs.docker.com/engine/reference/commandline/images/)命令查看创建的映像。 已下载或创建三个映像。 `azure-vote-front` 映像包含应用程序，并以 `nginx-flask` 映像为依据。 `redis` 映像用于启动 Redis 实例：

```
$ docker images

REPOSITORY                   TAG        IMAGE ID            CREATED             SIZE
azure-vote-front             latest     9cc914e25834        40 seconds ago      694MB
redis                        latest     a1b99da73d05        7 days ago          106MB
tiangolo/uwsgi-nginx-flask   flask      788ca94b2313        9 months ago        694MB
```

将*azure 投票前*容器映像推送到 acr 之前，请通过[az ACR LIST](/cli/azure/acr#az-acr-list)命令获取 acr 登录服务器。 下面的示例为 myResourceGroup 资源组中的注册表获取 ACR 登录服务器地址：

```azurecli
az acr list --resource-group myResourceGroup --query "[].{acrLoginServer:loginServer}" --output table
```

使用[docker tag](https://docs.docker.com/engine/reference/commandline/tag/)命令，使用 ACR 登录服务器名称和 `v1`的版本号来标记映像。 提供在上一步中获取的你自己的 `<acrLoginServer>` 名称：

```console
docker tag azure-vote-front <acrLoginServer>/azure-vote-front:v1
```

最后，将 azure-vote-front 映像推送到 ACR 注册表。 同样，将 `<acrLoginServer>` 替换为你自己的 ACR 注册表的登录服务器名称（如 `myacrregistry.azurecr.io`）：

```console
docker push <acrLoginServer>/azure-vote-front:v1
```

## <a name="deploy-the-sample-application-to-aks"></a>将示例应用程序部署到 AKS

若要将示例应用程序部署到 AKS 群集，可使用 Azure 投票存储库根目录中的 Kubernetes 清单文件。 使用  *等编辑器打开 azure-vote-all-in-one-redis.yaml*`vi` 清单文件。 将 `microsoft` 替换为 ACR 登录服务器名称。 此值位于清单文件的第 47 行：

```yaml
containers:
- name: azure-vote-front
  image: microsoft/azure-vote-front:v1
```

接下来，使用[kubectl apply](https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#apply)命令将该应用程序部署到 AKS 群集：

```console
kubectl apply -f azure-vote-all-in-one-redis.yaml
```

此时，系统创建 Kubernetes 负载均衡器服务，向 Internet 公开应用程序。 此过程可能需要几分钟。 若要监视负载均衡器部署的进度，请将[kubectl get service](https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get)命令与 `--watch` 参数一起使用。 EXTERNAL-IP 地址从“挂起”变为 IP 地址以后，请使用 `Control + C` 停止 kubectl 监视进程。

```console
$ kubectl get service azure-vote-front --watch

NAME               TYPE           CLUSTER-IP    EXTERNAL-IP   PORT(S)        AGE
azure-vote-front   LoadBalancer   10.0.215.27   <pending>     80:30747/TCP   22s
azure-vote-front   LoadBalancer   10.0.215.27   40.117.57.239   80:30747/TCP   2m
```

若要查看应用程序的实际效果，请打开 Web 浏览器，以转到服务的外部 IP 地址。 此时，Azure 投票应用程序显示，如下面的示例所示：

![AKS 中运行的 Azure 示例投票应用程序](media/jenkins-continuous-deployment/azure-vote.png)

## <a name="deploy-jenkins-to-an-azure-vm"></a>将 Jenkins 部署到 Azure VM

若要快速部署 Jenkins 以用于本文，可运行下面的脚本，以部署 Azure 虚拟机、配置网络访问并完成 Jenkins 基本安装。 为了在 Jenkins 和 AKS 群集之间进行身份验证，此脚本将 Kubernetes 配置文件从开发系统复制到 Jenkins 系统。

> [!WARNING]
> 此示例脚本用于演示目的，以快速预配在 Azure VM 上运行的 Jenkins 环境。 它使用 Azure 自定义脚本扩展来配置 VM，然后显示所需的凭据。 *~/.kube/config* 将复制到 Jenkins VM。

运行以下命令下载并运行该脚本。 运行任意脚本前，应先检查它的内容：[https://raw.githubusercontent.com/Azure-Samples/azure-voting-app-redis/master/jenkins-tutorial/deploy-jenkins-vm.sh](https://raw.githubusercontent.com/Azure-Samples/azure-voting-app-redis/master/jenkins-tutorial/deploy-jenkins-vm.sh)。

```console
curl https://raw.githubusercontent.com/Azure-Samples/azure-voting-app-redis/master/jenkins-tutorial/deploy-jenkins-vm.sh > azure-jenkins.sh
sh azure-jenkins.sh
```

创建 VM 并部署 Docker 和 Jenkins 的必需组件，需要几分钟时间才能完成。 此脚本在完成后输出 Jenkins 服务器地址，以及用于解锁仪表板的密钥，如下面的示例输出所示：

```
Open a browser to http://40.115.43.83:8080
Enter the following to Unlock Jenkins:
667e24bba78f4de6b51d330ad89ec6c6
```

打开 Web 浏览器，以转到显示的 URL，并输入解锁密钥。 按照屏幕上的提示操作，完成 Jenkins 配置：

- 选择“安装推荐插件”
- 创建第一个管理用户。 输入用户名（如 azureuser），再提供你自己的安全密码。 最后，键入全名和电子邮件地址。
- 选择“保存并完成”
- 准备好 Jenkins 后，选择“开始使用 Jenkins”
    - 如果开始使用 Jenkins 时 Web 浏览器显示空白页，请重启 Jenkins 服务。 若要重启服务，请通过 SSH 转到 Jenkins 实例的公共 IP 地址，并键入 `sudo service jenkins restart`。 在服务重启后，立即刷新 Web 浏览器。
- 使用在安装过程中创建的用户名和密码登录 Jenkins。

## <a name="create-a-jenkins-environment-variable"></a>创建 Jenkins 环境变量

Jenkins 环境变量用于保留 ACR 登录服务器名称。 此变量在 Jenkins 生成作业运行期间进行引用。 若要创建此环境变量，请完成以下步骤：

- 在 Jenkins 门户的左侧，依次选择“管理 Jenkins” > “配置系统”
- 在“全局属性”下，选择“环境变量”。 添加包含名称 `ACR_LOGINSERVER` 和 ACR 登录服务器值的变量。

    ![Jenkins 环境变量](media/jenkins-continuous-deployment/env-variables.png)

- 完成后，单击 Jenkins 配置页底部的“保存”。

## <a name="create-a-jenkins-credential-for-acr"></a>创建 ACR 的 Jenkins 凭据

必须指定 ACR 的凭据，Jenkins 才能生成更新后的容器映像，并将它推送到 ACR。 可使用 Azure Active Directory 服务主体执行此身份验证。 在先决条件中，已为 AKS 群集配置了对 ACR 注册表拥有“读者”权限的服务主体。 借助这些权限，AKS 群集可以从 ACR 注册表中拉取映像。 在 CI/CD 流程期间，Jenkins 根据应用程序更新生成新容器映像，然后需要将这些映像推送到 ACR 注册表。 现在，为 Jenkins 配置对 ACR 注册表拥有“参与者”权限的服务主体，以区分角色和权限。

### <a name="create-a-service-principal-for-jenkins-to-use-acr"></a>创建供 Jenkins 使用 ACR 的服务主体

首先，使用[az ad sp create for rbac](/cli/azure/ad/sp#az-ad-sp-create-for-rbac)命令创建服务主体：

```azurecli
$ az ad sp create-for-rbac --skip-assignment

{
  "appId": "626dd8ea-042d-4043-a8df-4ef56273670f",
  "displayName": "azure-cli-2018-09-28-22-19-34",
  "name": "http://azure-cli-2018-09-28-22-19-34",
  "password": "1ceb4df3-c567-4fb6-955e-f95ac9460297",
  "tenant": "72f988bf-86f1-41af-91ab-2d7cd011db48"
}
```

记下输出中显示的 appId 和 password。 在后续步骤中，需要用到这些值在 Jenkins 中配置凭据资源。

使用[az ACR show](/cli/azure/acr#az-acr-show)命令获取 ACR 注册表的资源 ID，并将其存储为变量。 提供资源组名称和 ACR 名称：

```azurecli
ACR_ID=$(az acr show --resource-group myResourceGroup --name <acrLoginServer> --query "id" --output tsv)
```

现在创建角色分配，以向服务主体分配对 ACR 注册表的“参与者”权限。 在下面的示例中，提供在运行上一个用于创建服务主体的命令后输出显示的你自己 appId：

```azurecli
az role assignment create --assignee 626dd8ea-042d-4043-a8df-4ef56273670f --role Contributor --scope $ACR_ID
```

### <a name="create-a-credential-resource-in-jenkins-for-the-acr-service-principal"></a>在 Jenkins 中创建 ACR 服务主体的凭据资源

借助在 Azure 中创建的角色分配，现在将 ACR 凭据存储在 Jenkins 凭据对象中。 在运行 Jenkins 生成作业期间会引用这些凭据。

返回到 Jenkins 门户的左侧，依次单击“凭据” > “Jenkins” > “全局凭据(无限制)” > “添加凭据”

确保凭据类型为“带密码的用户名”，并输入以下项：

- **用户名** - 为采用 ACR 注册表身份验证而创建的服务主体的 appId。
- **密码** - 为采用 ACR 注册表身份验证而创建的服务主体的 password。
- **ID** - 凭据标识符，如 acr-credentials

完成后，凭据表单如下面的示例所示：

![创建包含服务主体信息的 Jenkins 凭据对象](media/jenkins-continuous-deployment/acr-credentials.png)

单击“确定”，并返回到 Jenkins 门户。

## <a name="create-a-jenkins-project"></a>创建 Jenkins 项目

在 Jenkins 门户主页的左侧，选择“新建项”：

1. 输入“azure-vote”作为作业名称。 依次选择“自由风格项目”和“确定”
1. 在 "**常规**" 部分下，选择 " **github 项目**" 并输入分叉的存储库 URL，如*https：\//github.com/\<你的 GitHub 帐户\>/azure-voting-app-redis*
1. 在 "**源代码管理**" 部分下，选择 " **git**"，输入分叉的存储库 *。 Git* URL，如*https：\//github.com/\<你的 github 帐户\>/azure-voting-app-redis.git*

1. 在“生成触发器”部分下面，选择“用于 GITscm 轮询的 GitHub 挂钩触发器”
1. 在“生成环境”下，选择“使用机密文本或文件”
1. 在“绑定”下，依次选择“添加” > “用户名和密码(已分隔)”
   - 在“用户名变量”`ACR_ID`**中输入** ，并在“密码变量”`ACR_PASSWORD`**中输入**

     ![Jenkins 绑定](media/jenkins-continuous-deployment/bindings.png)

1. 选择添加类型为“执行 shell”的“生成步骤”，并使用以下文本。 此脚本将生成新的容器映像，并将其推送到 ACR 注册表。

    ```bash
    # Build new image and push to ACR.
    WEB_IMAGE_NAME="${ACR_LOGINSERVER}/azure-vote-front:kube${BUILD_NUMBER}"
    docker build -t $WEB_IMAGE_NAME ./azure-vote
    docker login ${ACR_LOGINSERVER} -u ${ACR_ID} -p ${ACR_PASSWORD}
    docker push $WEB_IMAGE_NAME
    ```

1. 添加另一个“执行 shell”类型的“生成步骤”并使用以下文本。 此脚本使用 ACR 中的新容器映像来更新 AKS 中的应用程序部署。

    ```bash
    # Update kubernetes deployment with new image.
    WEB_IMAGE_NAME="${ACR_LOGINSERVER}/azure-vote-front:kube${BUILD_NUMBER}"
    kubectl set image deployment/azure-vote-front azure-vote-front=$WEB_IMAGE_NAME --kubeconfig /var/lib/jenkins/config
    ```

1. 完成后，单击“保存”。

## <a name="test-the-jenkins-build"></a>测试 Jenkins 生成

先手动测试 Jenkins 生成，再根据 GitHub 提交来自动执行作业。 此手动生成验证是否已正确配置生成作业、适当 Kubernetes 身份验证文件是否已就位，以及 ACR 身份验证能否正常运行。

在项目的左侧菜单中，选择“立即生成”。

![Jenkins 测试生成](media/jenkins-continuous-deployment/test-build.png)

第一个生成需要 1 或 2 分钟时间完成，因为 Docker 映像层被下拉到 Jenkins 服务器。 后续生成可使用缓存的映像层，以缩短生成时间。

在生成流程期间，GitHub 存储库克隆到 Jenkins 生成服务器。 将会生成新的容器映像并将其推送到 ACR 注册表。 最后，AKS 群集上运行的 Azure 投票应用程序会更新为使用新映像。 由于未对应用程序代码进行任何更改，因此在 Web 浏览器中查看的示例应用程序没有变化。

生成作业完成后，立即单击生成历史记录下的“生成 1”。 选择“控制台输出”，并查看生成流程的输出。 最后一行应指示生成成功。

## <a name="create-a-github-webhook"></a>创建 GitHub Webhook

成功完成手动生成后，立即将 GitHub 集成到 Jenkins 生成。 Webhook 可用于在 GitHub 中每次有代码提交时运行 Jenkins 生成作业。 若要创建 GitHub Webhook，请完成以下步骤：

1. 在 Web 浏览器中，转到 GitHub 分支存储库。
1. 选择“设置”，然后在左侧选择“Webhook”。
1. 选择“添加 Webhook”。 对于“有效负载 URL”，输入“`http://<publicIp:8080>/github-webhook/`”，其中 `<publicIp>` 是 Jenkins 服务器的 IP 地址。 请务必包含尾部的 /。 保留内容类型的其他默认值，并触发“推送”事件。
1. 选择“添加 Webhook”。

    ![为 Jenkins 创建 GitHub Webhook](media/jenkins-continuous-deployment/webhook.png)

## <a name="test-the-complete-cicd-pipeline"></a>测试整个 CI/CD 管道

现在可测试整个 CI/CD 管道。 当你将代码提交推送到 GitHub 后，系统会执行以下步骤：

1. GitHub Webhook 访问 Jenkins。
1. Jenkins 启动生成作业，并从 GitHub 拉取最新代码提交。
1. Docker 生成通过更新后的代码启动，新容器映像标记有最新生成号。
1. 新容器映像推送到 Azure 容器注册表。
1. 部署到 Azure Kubernetes 服务的应用程序使用 Azure 容器注册表中的最新容器映像进行更新。

在开发计算机上，使用代码编辑器打开克隆的应用程序。 在 /azure-vote/azure-vote 目录下，打开 config_file.cfg 文件。 将此文件中的投票值更新为，除 cats 和 dogs 以外的值，如下面的示例所示：

```
# UI Configurations
TITLE = 'Azure Voting App'
VOTE1VALUE = 'Blue'
VOTE2VALUE = 'Purple'
SHOWHOST = 'false'
```

更新后，保存此文件并提交更改，同时将更改推送到 GitHub 存储库的分支。 GitHub Webhook 触发新的 Jenkins 生成作业。 在 Jenkins Web 仪表板中，监视生成流程。 只需几秒钟，即可拉取最新代码，创建和推送更新后的映像，并部署 AKS 中更新后的应用程序。

生成完成后，立即刷新示例 Azure 投票应用程序的 Web 浏览器。 此时，更改显示，如下面的示例所示：

![AKS 中由 Jenkins 生成作业更新的示例 Azure 投票应用程序](media/jenkins-continuous-deployment/azure-vote-updated.png)

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [Azure 上的 Jenkins](/azure/jenkins)