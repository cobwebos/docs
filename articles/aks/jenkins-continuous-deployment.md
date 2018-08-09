---
title: Jenkins 持续部署与 Azure Kubernetes 服务中的 Kubernetes
description: 如何使用 Jenkins 自动完成持续部署过程，以便在 Azure Kubernetes 服务中的 Kubernetes 上部署和升级容器化应用
services: container-service
author: iainfoulds
manager: jeconnoc
ms.service: container-service
ms.topic: article
ms.date: 03/26/2018
ms.author: iainfou
ms.custom: mvc
ms.openlocfilehash: a1a6799bc049fea829f8e32d12705e26e3a41dc0
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/02/2018
ms.locfileid: "39425752"
---
# <a name="continuous-deployment-with-jenkins-and-azure-kubernetes-service"></a>使用 Jenkins 和 Azure Kubernetes 服务进行持续部署

本文档演示如何在 Jenkins 与 Azure Kubernetes 服务 (AKS) 群集之间设置基本的持续部署工作流。

示例工作流包括以下步骤：

> [!div class="checklist"]
> * 将 Azure 投票应用程序部署到 Kubernetes 群集。
> * 更新 Azure 投票应用程序代码并推送到 GitHub 存储库，以便启动持续部署过程。
> * Jenkins 克隆该存储库，并使用更新的代码生成新的容器映像。
> * 此映像被推送到 Azure 容器注册表 (ACR)。
> * 使用新的容器映像更新 AKS 群集中运行的应用程序。

## <a name="prerequisites"></a>先决条件

需要准备好以下各项才能完成本文中的步骤。

- 基本了解 Kubernetes、Git、CI/CD 和 Azure 容器注册表 (ACR)。
- 已在开发系统上配置了 [Azure Kubernetes 服务 (AKS) 群集][aks-quickstart]和 [AKS 凭据][aks-credentials]。
- [Azure 容器注册表 (ACR)][acr-quickstart]、ACR 登录服务器名称，以及拥有推送和提取访问权限的 [ACR 凭据][acr-authentication]。
- 已在开发系统上安装 Azure CLI。
- 已在开发系统上安装 Docker。
- GitHub 帐户、[GitHub 个人访问令牌][git-access-token]，并在开发系统上安装了 Git 客户端。

## <a name="prepare-application"></a>准备应用程序

本文档中通篇使用的 Azure 投票应用程序包含一个或多个 pod 中托管的 Web 接口，以及另一个用于托管 Redis（用作临时数据存储）的 pod。

在生成 Jenkins/AKS 集成之前，请准备好 Azure 投票应用程序并将其部署到 AKS 群集。 在应用程序的第一个版本中就应该考虑这些工作。

分叉以下 GitHub 存储库。

```
https://github.com/Azure-Samples/azure-voting-app-redis
```

创建分叉后，将其克隆到开发系统。 克隆此存储库时，请确保使用分叉的 URL。

```bash
git clone https://github.com/<your-github-account>/azure-voting-app-redis.git
```

将目录更改为克隆的目录，以供使用。

```bash
cd azure-voting-app-redis
```

运行 `docker-compose.yaml` 文件以创建 `azure-vote-front` 容器映像，并启动应用程序。

```bash
docker-compose up -d
```

完成后，使用 [docker images][docker-images] 命令查看创建的映像。

请注意，已下载或创建三个映像。 `azure-vote-front` 映像包含应用程序，并以 `nginx-flask` 映像为依据。 `redis` 映像用于启动 Redis 实例。

```console
$ docker images

REPOSITORY                   TAG        IMAGE ID            CREATED             SIZE
azure-vote-front             latest     9cc914e25834        40 seconds ago      694MB
redis                        latest     a1b99da73d05        7 days ago          106MB
tiangolo/uwsgi-nginx-flask   flask      788ca94b2313        9 months ago        694MB
```

使用 [az acr list][az-acr-list] 命令获取 ACR 登录服务器。 请确保使用托管 ACR 注册表的资源组更新资源组名称。

```azurecli
az acr list --resource-group myResourceGroup --query "[].{acrLoginServer:loginServer}" --output table
```

结合登录服务器名称和版本号 `v1` 使用 [docker tag][docker-tag] 命令来标记映像。

```bash
docker tag azure-vote-front <acrLoginServer>/azure-vote-front:v1
```

使用 ACR 登录服务器名称更新 ACR 登录服务器值，并将 `azure-vote-front` 映像推送到注册表。

```bash
docker push <acrLoginServer>/azure-vote-front:v1
```

## <a name="deploy-application-to-kubernetes"></a>将应用程序部署到 Kubernetes

可以在 Azure 投票存储库的根目录中找到 Kubernetes 清单文件，使用该文件可将应用程序部署到 Kubernetes 群集。

首先，使用 ACR 注册表的位置更新 **azure-vote-all-in-one-redis.yaml** 清单文件。 使用任何文本编辑器打开该文件，并将 `microsoft` 替换为 ACR 登录服务器名称。 此值位于清单文件的第 47 行。

```yaml
containers:
- name: azure-vote-front
  image: microsoft/azure-vote-front:v1
```

接下来，使用 [kubectl apply][kubectl-apply] 命令运行该应用程序。 此命令分析清单文件并创建定义的 Kubernetes 对象。

```bash
kubectl apply -f azure-vote-all-in-one-redis.yaml
```

创建向 Internet 公开应用程序的 [Kubernetes 服务][kubernetes-service]。 此过程可能需要几分钟。

若要监视进度，请将 [kubectl get service][kubectl-get] 命令与 `--watch` 参数配合使用。

```bash
kubectl get service azure-vote-front --watch
```

azure-vote-front 服务的 EXTERNAL-IP 一开始显示为“挂起”。

```
azure-vote-front   10.0.34.242   <pending>     80:30676/TCP   7s
```

EXTERNAL-IP 地址从“挂起”变为 IP 地址以后，请使用 `control+c` 停止 kubectl 监视进程。

```
azure-vote-front   10.0.34.242   13.90.150.118   80:30676/TCP   2m
```

若要查看应用程序，请浏览到外部 IP 地址。

![Azure 上的 Kubernetes 群集映像](media/aks-jenkins/azure-vote-safari.png)

## <a name="deploy-jenkins-to-vm"></a>将 Jenkins 部署到 VM

我们已预先创建一个脚本用于部署虚拟机、配置网络访问，并完成 Jenkins 的基本安装。 此外，该脚本会将 Kubernetes 配置文件从开发系统复制到 Jenkins 系统。 此文件用于 Jenkins 与 AKS 群集之间的身份验证。

运行以下命令下载并运行该脚本。 以下 URL 还可用于查看脚本的内容。

> [!WARNING]
> 此示例脚本用于演示目的，以快速预配在 Azure VM 上运行的 Jenkins 环境。 它使用 Azure 自定义脚本扩展来配置 VM，然后显示所需的凭据。 *~/.kube/config* 将复制到 Jenkins VM。

```console
curl https://raw.githubusercontent.com/Azure-Samples/azure-voting-app-redis/master/jenkins-tutorial/deploy-jenkins-vm.sh > azure-jenkins.sh
sh azure-jenkins.sh
```

该脚本完成后，会输出 Jenkins 服务器的地址，以及用于解锁 Jenkins 的密钥。 浏览到该 URL、输入该密钥，然后遵照屏幕提示完成 Jenkins 配置。

```console
Open a browser to http://52.166.118.64:8080
Enter the following to Unlock Jenkins:
667e24bba78f4de6b51d330ad89ec6c6
```

## <a name="jenkins-environment-variables"></a>Jenkins 环境变量

Jenkins 环境变量用于保存 Azure 容器注册表 (ACR) 登录服务器名称。 在运行 Jenkins 持续部署作业期间会引用此变量。

在 Jenkins 管理门户中，单击“管理 Jenkins” > “配置系统”。

在“全局属性”下选择“环境变量”，并添加包含名称 `ACR_LOGINSERVER` 和 ACR 登录服务器值的变量。

![Jenkins 环境变量](media/aks-jenkins/env-variables.png)

完成后，在 Jenkins 配置页上单击“保存”。

## <a name="jenkins-credentials"></a>Jenkins 凭据

现在，将 ACR 凭据存储在 Jenkins 凭据对象中。 在运行 Jenkins 生成作业期间会引用这些凭据。

返回 Jenkins 管理门户，单击“凭据” > “Jenkins” > “全局凭据(无限制)” > “添加凭据”。

确保凭据类型为“带密码的用户名”，并输入以下项：

- **用户名** - 用于在 ACR 注册表中进行身份验证的服务主体的 ID。
- **密码** - 用于在 ACR 注册表中进行身份验证的服务主体的客户端机密。
- **ID** - 凭据标识符，例如 `acr-credentials`。

完成后，凭据窗体应如下图所示：

![ACR 凭据](media/aks-jenkins/acr-credentials.png)

单击“确定”并返回 Jenkins 管理门户。

## <a name="create-jenkins-project"></a>创建 Jenkins 项目

在 Jenkins 管理门户中，单击“新建项”。

为项目命名（例如 `azure-vote`），依次选择“自由风格项目”、“确定”。

![Jenkins 项目](media/aks-jenkins/jenkins-project.png)

在“常规”下，选择“GitHub 项目”，并输入 Azure 投票 GitHub 项目分叉的 URL。

![GitHub 项目](media/aks-jenkins/github-project.png)

在“源代码管理”下选择“Git”，输入 Azure 投票 GitHub 存储库分叉的 URL。

单击“添加” > “Jenkins”输入凭据。 在“类型”下选择“机密文本”，输入 [GitHub 个人访问令牌][git-access-token]作为机密。

完成时请选择“添加”。

![GitHub 凭据](media/aks-jenkins/github-creds.png)

在“生成触发器”下，选择“用于 GITScm 轮询的 GitHub 挂钩触发器”。

![Jenkins 生成触发器](media/aks-jenkins/build-triggers.png)

在“生成环境”下，选择“使用机密文本或文件”。

![Jenkins 生成环境](media/aks-jenkins/build-environment.png)

在“绑定”下，选择“添加” > “用户名和密码(分隔)”。

在“用户名变量”中输入 `ACR_ID`，在“密码变量”中输入 `ACR_PASSWORD`。

![Jenkins 绑定](media/aks-jenkins/bindings.png)

添加“执行 shell”类型的“生成步骤”并使用以下文本。 此脚本将生成新的容器映像，并将其推送到 ACR 注册表。

```bash
# Build new image and push to ACR.
WEB_IMAGE_NAME="${ACR_LOGINSERVER}/azure-vote-front:kube${BUILD_NUMBER}"
docker build -t $WEB_IMAGE_NAME ./azure-vote
docker login ${ACR_LOGINSERVER} -u ${ACR_ID} -p ${ACR_PASSWORD}
docker push $WEB_IMAGE_NAME
```

添加另一个“执行 shell”类型的“生成步骤”并使用以下文本。 此脚本更新 Kubernetes 部署。

```bash
# Update kubernetes deployment with new image.
WEB_IMAGE_NAME="${ACR_LOGINSERVER}/azure-vote-front:kube${BUILD_NUMBER}"
kubectl set image deployment/azure-vote-front azure-vote-front=$WEB_IMAGE_NAME --kubeconfig /var/lib/jenkins/config
```

完成后，单击“保存”。

## <a name="test-the-jenkins-build"></a>测试 Jenkins 生成

在继续之前，请测试 Jenkins 生成。 这会验证是否已正确配置生成作业、准备好适当的 Kubernetes 身份验证文件，并提供了适当的 ACR 凭据。

在项目的左侧菜单中单击“立即生成”。

![Jenkins 测试生成](media/aks-jenkins/test-build.png)

在此过程中，GitHub 存储库将克隆到 Jenkins 生成服务器。 将会生成新的容器映像并将其推送到 ACR 注册表。 最后，AKS 群集上运行的 Azure 投票应用程序会更新为使用新映像。 由于未对应用程序代码进行任何更改，因此应用程序不会更改。

完成该过程后，可以单击生成历史记录下的“生成 #1”并选择“控制台输出”来查看生成过程的所有输出。 最后一行应指示生成成功。

## <a name="create-github-webhook"></a>创建 GitHub Webhook

接下来，将应用程序存储库挂接到 Jenkins 生成服务器，以便在提交任何内容时，都会触发新的生成。

1. 浏览到分叉的 GitHub 存储库。
2. 选择“设置”，然后在左侧选择“Webhook”。
3. 选择“添加 Webhook”。 对于“有效负载 URL”，请输入 `http://<publicIp:8080>/github-webhook/`，其中 `publicIp` 是 Jenkins 服务器的 IP 地址。 请务必包含尾部的 /。 保留内容类型的其他默认值，并触发“推送”事件。
4. 选择“添加 Webhook”。

    ![GitHub Webhook](media/aks-jenkins/webhook.png)

## <a name="test-cicd-process-end-to-end"></a>测试 CI/CD 端到端过程

在开发计算机上，使用代码编辑器打开克隆的应用程序。

在 **/azure-vote/azure-vote** 目录下，找到名为 **config_file.cfg** 的文件。 将此文件中的投票值更新为除 cats 和 dogs 以外的值。

以下示例显示了更新的 **config_file.cfg** 文件。

```bash
# UI Configurations
TITLE = 'Azure Voting App'
VOTE1VALUE = 'Blue'
VOTE2VALUE = 'Purple'
SHOWHOST = 'false'
```

完成后，保存该文件、提交更改，并将更改推送到 GitHub 存储库的分叉。 完成提交后，GitHub Webhook 会触发新的 Jenkins 生成，从而更新容器映像和 AKS 部署。 可在 Jenkins 管理控制台上监视生成过程。

完成生成后，再次浏览到应用程序终结点以观察所做的更改。

![已更新 Azure 投票](media/aks-jenkins/azure-vote-updated-safari.png)

现已完成一个简单的持续部署过程。 可以使用本示例中演示的步骤和配置，生成更可靠的、随时可用于生产的持续生成自动化过程。

<!-- LINKS - external -->
[docker-images]: https://docs.docker.com/engine/reference/commandline/images/
[docker-tag]: https://docs.docker.com/engine/reference/commandline/tag/
[git-access-token]: https://help.github.com/articles/creating-a-personal-access-token-for-the-command-line/
[kubectl-apply]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#apply
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[kubernetes-service]: https://kubernetes.io/docs/concepts/services-networking/service/

<!-- LINKS - internal -->
[az-acr-list]: /cli/azure/acr#az-acr-list
[acr-authentication]: ../container-registry/container-registry-auth-aks.md
[acr-quickstart]: ../container-registry/container-registry-get-started-azure-cli.md
[aks-credentials]: /cli/azure/aks#az-aks-get-credentials
[aks-quickstart]: kubernetes-walkthrough.md
[azure-cli-install]: /cli/azure/install-azure-cli