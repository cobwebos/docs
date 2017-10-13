---
title: "Jenkins CI/CD 和 Azure 容器服务中的 Kubernetes | Microsoft Docs"
description: "如何使用 Jenkins 自动完成 CI/CD 过程，以便在 Azure 容器服务中的 Kubernetes 上部署和升级容器化应用"
services: container-service
documentationcenter: 
author: chzbrgr71
manager: johny
editor: 
tags: acs, azure-container-service, jenkins
keywords: "Docker, 容器, Kubernetes, Azure, Jenkins"
ms.assetid: 
ms.service: container-service
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/23/2017
ms.author: briar
ms.custom: mvc
ms.openlocfilehash: 2078d0694fc4dd6e83ecd2792588b4254980cd78
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/11/2017
---
# <a name="jenkins-integration-with-azure-container-service-and-kubernetes"></a>Jenkins 与 Azure 容器服务和 Kubernetes 的集成 
本教程逐步讲解使用 Jenkins 平台将多容器应用程序持续集成到 Azure 容器服务 Kubernetes 的过程。 工作流将使用部署滚动更新来更新 Docker 中心内的容器映像并升级 Kubernetes 组合。 

## <a name="high-level-process"></a>高级过程
本文中详述的基本步骤包括： 
- 在容器服务中安装 Kubernetes 群集
- 设置 Jenkins 并配置对容器服务的访问
- 创建 Jenkins 工作流
- 测试端到端 CI/CD 过程

## <a name="install-a-kubernetes-cluster"></a>安装 Kubernetes 群集
    
使用以下步骤在 Azure 容器服务中部署 Kubernetes 群集。 [此处](container-service-kubernetes-walkthrough.md)提供了完整文档。

### <a name="step-1-create-a-resource-group"></a>步骤 1：创建资源组
```azurecli
RESOURCE_GROUP=my-resource-group
LOCATION=westus

az group create --name=$RESOURCE_GROUP --location=$LOCATION
```

### <a name="step-2-deploy-the-cluster"></a>步骤 2：部署群集
> [!NOTE]
> 以下步骤需要 ~/.ssh 文件夹中存储的本地 SSH 公钥。
>

```azurecli
RESOURCE_GROUP=my-resource-group
DNS_PREFIX=some-unique-value
CLUSTER_NAME=any-acs-cluster-name

az acs create \
--orchestrator-type=kubernetes \
--resource-group $RESOURCE_GROUP \
--name=$CLUSTER_NAME \
--dns-prefix=$DNS_PREFIX \
--ssh-key-value ~/.ssh/id_rsa.pub \
--admin-username=azureuser \
--master-count=1 \
--agent-count=5 \
--agent-vm-size=Standard_D1_v2
```

## <a name="set-up-jenkins-and-configure-access-to-container-service"></a>设置 Jenkins 并配置对容器服务的访问

### <a name="step-1-install-jenkins"></a>步骤 1：安装 Jenkins
1. 创建包含 Ubuntu 16.04 LTS 的 Azure VM。  由于在后续步骤中需使用本地计算机上的 bash 连接此 VM，请将“身份验证类型”设置为“SSH 公钥”，然后粘贴存储在本地 ~/.ssh 文件夹中的 SSH 公钥。  此外，请记下指定的“用户名”，因为需使用此用户名才能查看 Jenkins 仪表板，并在后续步骤中连接 Jenkins VM。
2. 根据这些[说明](https://wiki.jenkins-ci.org/display/JENKINS/Installing+Jenkins+on+Ubuntu)安装 Jenkins。 [howtoforge.com](https://www.howtoforge.com/tutorial/how-to-install-jenkins-with-apache-on-ubuntu-16-04) 上提供了更详细的教程。
3. 若要在本地计算机上查看 Jenkins 仪表板，请添加允许访问端口 8080 的入站规则，更新 Azure 网络安全组，以允许端口 8080。  也可运行以下命令，设置端口转发：`ssh -i ~/.ssh/id_rsa -L 8080:localhost:8080 <your_jenkins_user>@<your_jenkins_public_ip`
4. 使用浏览器，导航到公共 IP (http://<your_jenkins_public_ip>:8080)，连接 Jenkins 服务器，并使用初始管理员密码对 Jenkins 仪表板进行首次解锁。  管理员密码存储在 Jenkins VM 上的 /var/lib/jenkins/secrets/initialAdminPassword 中。  通过 SSH 连接到 Jenkins VM 即可轻松获取此密码：`ssh <your_jenkins_user>@<your_jenkins_public_ip>`。  然后运行 `sudo cat /var/lib/jenkins/secrets/initialAdminPassword`。
5. 根据这些[说明](https://docs.docker.com/cs-engine/1.13/#install-on-ubuntu-1404-lts-or-1604-lts)在 Jenkins 计算机上安装 Docker。 然后，便可以在 Jenkins 作业中运行 Docker 命令。
6. 配置 Docker 权限，允许 Jenkins 访问 Docker 终结点。

    ```bash
    sudo chmod 777 /run/docker.sock
    ```
8. 在 Jenkins 上安装 `kubectl` CLI。 [安装和设置 kubectl](https://kubernetes.io/docs/tasks/kubectl/install/) 中提供了更多详细信息。  Jenkins 作业将使用“kubectl”来管理并部署到 Kubernetes 群集。

    ```bash
    curl -LO https://storage.googleapis.com/kubernetes-release/release/$(curl -s https://storage.googleapis.com/kubernetes-release/release/stable.txt)/bin/linux/amd64/kubectl

    chmod +x ./kubectl

    sudo mv ./kubectl /usr/local/bin/kubectl
    ```

### <a name="step-2-set-up-access-to-the-kubernetes-cluster"></a>步骤 2：设置对 Kubernetes 群集的访问

> [!NOTE]
> 可通过多种方法完成以下步骤。 请使用最方便的方法。
>

1. 将 `kubectl` 配置文件复制到 Jenkins 计算机，以便 Jenkins 作业可以访问 Kubernetes 群集。 这些说明假定正在使用的 bash 来自 Jenkins VM 以外的计算机，且计算机 ~/.ssh 文件夹中存储有本地 SSH 公钥。

```bash
export KUBE_MASTER=<your_cluster_master_fqdn>
export JENKINS_USER=<your_jenkins_user>
export JENKINS_SERVER=<your_jenkins_public_ip>
sudo ssh $JENKINS_USER@$JENKINS_SERVER sudo mkdir -m 777 /home/$JENKINS_USER/.kube/ \
&& sudo ssh $JENKINS_USER@$JENKINS_SERVER sudo mkdir /var/lib/jenkins/.kube/ \
&& sudo scp -3 -i ~/.ssh/id_rsa azureuser@$KUBE_MASTER:.kube/config $JENKINS_USER@$JENKINS_SERVER:~/.kube/config \
&& sudo ssh -i ~/.ssh/id_rsa $JENKINS_USER@$JENKINS_SERVER sudo cp /home/$JENKINS_USER/.kube/config /var/lib/jenkins/.kube/config \
```
        
2. 在 Jenkins 中验证是否可访问 Kubernetes 群集。  若要执行此操作，通过 SSH 连接到 Jenkins VM：`ssh <your_jenkins_user>@<your_jenkins_public_ip>`。  随后验证 Jenkins 是否可成功连接到群集：`kubectl cluster-info`。
    

## <a name="create-a-jenkins-workflow"></a>创建 Jenkins 工作流

### <a name="prerequisites"></a>先决条件

- 代码存储库的 GitHub 帐户。
- 用于存储和更新映像的 Docker 中心帐户。
- 可重新生成和更新的容器化应用程序。 可以使用 Golang 中编写的此示例容器应用程序：https://github.com/chzbrgr71/go-web 

> [!NOTE]
> 必须在自己的 GitHub 帐户中必须执行以下步骤。 可任意克隆上述存储库，但必须使用自己的帐户来配置 Webhook 和 Jenkins 访问。
>

### <a name="step-1-deploy-initial-v1-of-application"></a>步骤 1：部署应用程序的初始 v1 版本
1. 在开发人员计算机中使用以下命令生成应用。 将 `myrepo` 替换成自己的值。
    
    ```bash
    git clone https://github.com/chzbrgr71/go-web.git
    cd go-web
    docker build -t myrepo/go-web .
    ```

2. 将映像推送到 Docker 中心。

    ```bash
    docker login
    docker push myrepo/go-web
    ```

3. 部署到 Kubernetes 群集。
    
    > [!NOTE] 
    > 编辑 `go-web.yaml` 文件以更新容器映像和存储库。
    >
        
    ```bash
    kubectl create -f ./go-web.yaml --record
    ```
### <a name="step-2-configure-jenkins-system"></a>步骤 2：配置 Jenkins 系统
1. 单击“管理 Jenkins” > “配置系统”。
2. 在“GitHub”下，选择“添加 GitHub 服务器”。
3. 将“API URL”保留为默认值。
4. 在“凭据”下，使用“机密文本”添加 Jenkins 凭据。 建议使用 GitHub 用户帐户设置中配置的 GitHub 个人访问令牌。 [此处](https://help.github.com/articles/creating-a-personal-access-token-for-the-command-line/)提供了更多详细信息。
5. 单击“测试连接”，确保配置正确。
6. 在“全局属性”下，添加环境变量 `DOCKER_HUB` 并提供 Docker 中心密码。 （在本演示教程中可以使用此变量，但在生产方案中，需要采用更安全的方法。）
7. 保存。

![Jenkins GitHub 访问](./media/container-service-kubernetes-jenkins/jenkins-github-access.png)

### <a name="step-3-create-the-jenkins-workflow"></a>步骤 3：创建 Jenkins 工作流
1. 创建 Jenkins 项。
2. 提供名称（例如“go-web”），并选择“自由格式的项目”。 
3. 选中“GitHub 项目”并提供 GitHub 存储库的 URL。
4. 在“源代码管理”中，提供 GitHub 存储库 URL 和凭据。 
5. 添加“执行 shell”类型的“生成步骤”并使用以下文本：

    ```bash
    WEB_IMAGE_NAME="myrepo/go-web:kube${BUILD_NUMBER}"
    docker build -t $WEB_IMAGE_NAME .
    docker login -u <your-dockerhub-username> -p ${DOCKER_HUB}
    docker push $WEB_IMAGE_NAME
    ```

6. 添加另一个“执行 shell”类型的“生成步骤”并使用以下文本：

    ```bash
    WEB_IMAGE_NAME="myrepo/go-web:kube${BUILD_NUMBER}"
    kubectl set image deployment/go-web go-web=$WEB_IMAGE_NAME --kubeconfig /var/lib/jenkins/config
    ```

![Jenkins 生成步骤](./media/container-service-kubernetes-jenkins/jenkins-build-steps.png)
    
7. 保存 Jenkins 项，并使用“立即生成”测试。

### <a name="step-4-connect-github-webhook"></a>步骤 4：连接 GitHub Webhook
1. 在创建的 Jenkins 项中，单击“配置”。
2. 在“生成触发器”下，选择“用于 GITScm 轮询的 GitHub 挂钩触发器”，并单击“保存”。 这会自动配置 GitHub Webhook。
3. 在 go-web 的 GitHub 存储库中，单击“设置”>“Webhook”。
4. 验证是否已成功添加 Jenkins Webhook URL。 URL 应以“github-webhook”结尾。

![Jenkins Webhook 配置](./media/container-service-kubernetes-jenkins/jenkins-webhook.png)

## <a name="test-the-cicd-process-end-to-end"></a>测试端到端 CI/CD 过程

1. 更新存储库的代码并将其推送/同步到 GitHub 存储库。
2. 在 Jenkins 控制台中，选中“生成历史记录”并验证作业是否已运行。 在控制台输出中查看详细信息。
3. 在 Kubernetes 中，查看升级后的部署详细信息：

    ```bash
    kubectl rollout history deployment/go-web
    ```

## <a name="next-steps"></a>后续步骤

- 部署 Azure 容器注册表并在安全存储库中存储映像。 请参阅 [Azure 容器注册表文档](https://docs.microsoft.com/azure/container-registry)。
- 在 Jenkins 中生成包含并列部署和自动化测试的复杂工作流。
- 有关使用 Jenkins 和 Kubernetes 进行持续集成/持续部署 (CI/CD) 的详细信息，请参阅 [Jenkins 博客](https://jenkins.io/blog/2015/07/24/integrating-kubernetes-and-jenkins/)。
