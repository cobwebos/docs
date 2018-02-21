---
title: "在 Azure 中使用 Jenkins 创建开发管道 | Microsoft Docs"
description: "了解如何在 Azure 中创建一个 Jenkins 虚拟机，用于在每次提交代码后从 GitHub 提取数据，并生成新的 Docker 容器来运行应用"
services: virtual-machines-linux
documentationcenter: virtual-machines
author: iainfoulds
manager: jeconnoc
editor: tysonn
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 12/15/2017
ms.author: iainfou
ms.custom: mvc
ms.openlocfilehash: 8a595ead7da8dfa5544903bd698bfdff40555eb9
ms.sourcegitcommit: 059dae3d8a0e716adc95ad2296843a45745a415d
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/09/2018
---
# <a name="how-to-create-a-development-infrastructure-on-a-linux-vm-in-azure-with-jenkins-github-and-docker"></a>如何使用 Jenkins、GitHub 和 Docker 在 Azure 中的 Linux VM 上创建开发基础结构
要将应用程序开发的生成和测试阶段自动化，可以使用持续集成和部署 (CI/CD) 管道。 本教程介绍如何在 Azure VM 上创建 CI/CD 管道，包括如何：

> [!div class="checklist"]
> * 创建 Jenkins VM
> * 安装并配置 Jenkins
> * 创建 GitHub 与 Jenkins 之间的 Webhook 集成
> * 通过 GitHub 提交创建并触发 Jenkins 生成作业
> * 创建应用的 Docker 映像
> * 验证 GitHub 提交是否生成新的 Docker 映像并更新正在运行的应用


[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

如果选择在本地安装并使用 CLI，本教程要求运行 Azure CLI 2.0.22 或更高版本。 运行 `az --version` 即可查找版本。 如果需要进行安装或升级，请参阅[安装 Azure CLI 2.0]( /cli/azure/install-azure-cli)。 

## <a name="create-jenkins-instance"></a>创建 Jenkins 实例
在有关[如何在首次启动时自定义 Linux 虚拟机](tutorial-automate-vm-deployment.md)的上一个教程中，已了解如何使用 cloud-init 自动执行 VM 自定义。 本教程使用 cloud-init 文件在 VM 上安装 Jenkins 和 Docker。 Jenkins 是一种常用的开放源代码自动化服务器，它与 Azure 无缝集成以支持持续集成 (CI) 和持续交付 (CD)。 有关如何使用 Jenkins 的更多教程，请参阅[Azure 中心中的 Jenkins](https://docs.microsoft.com/azure/jenkins/)。

在当前 shell 中，创建名为 cloud-init.txt 的文件并粘贴下面的配置。 例如，在不处于本地计算机上的 Cloud Shell 中创建文件。 输入 `sensible-editor cloud-init-jenkins.txt` 以创建文件并查看可用编辑器的列表。 请确保已正确复制整个 cloud-init 文件，尤其是第一行：

```yaml
#cloud-config
package_upgrade: true
write_files:
  - path: /etc/systemd/system/docker.service.d/docker.conf
    content: |
      [Service]
        ExecStart=
        ExecStart=/usr/bin/dockerd
  - path: /etc/docker/daemon.json
    content: |
      {
        "hosts": ["fd://","tcp://127.0.0.1:2375"]
      }
runcmd:
  - wget -q -O - https://jenkins-ci.org/debian/jenkins-ci.org.key | apt-key add -
  - sh -c 'echo deb http://pkg.jenkins-ci.org/debian-stable binary/ > /etc/apt/sources.list.d/jenkins.list'
  - apt-get update && apt-get install jenkins -y
  - curl -sSL https://get.docker.com/ | sh
  - usermod -aG docker azureuser
  - usermod -aG docker jenkins
  - touch /var/lib/jenkins/jenkins.install.InstallUtil.lastExecVersion
  - service jenkins restart
```

使用 [az group create](/cli/azure/group#az_group_create) 创建资源组，才能创建 VM。 以下示例在 *eastus* 位置创建名为 *myResourceGroupJenkins* 的资源组。

```azurecli-interactive 
az group create --name myResourceGroupJenkins --location eastus
```

现使用 [az vm create](/cli/azure/vm#az_vm_create) 创建 VM。 使用 `--custom-data` 参数传递到 cloud-init 配置文件中。 如果已将 *cloud-init-jenkins.txt* 文件保存在现有工作目录的外部，请提供该文件的完整路径。

```azurecli-interactive 
az vm create --resource-group myResourceGroupJenkins \
    --name myVM \
    --image UbuntuLTS \
    --admin-username azureuser \
    --generate-ssh-keys \
    --custom-data cloud-init-jenkins.txt
```

创建并配置 VM 需要几分钟的时间。

若要允许 Web 流量抵达 VM，请使用 [az vm open-port](/cli/azure/vm#az_vm_open_port) 为 Jenkins 流量打开端口 *8080*，并为用于运行示例应用的 Node.js 应用打开端口 *1337*：

```azurecli-interactive 
az vm open-port --resource-group myResourceGroupJenkins --name myVM --port 8080 --priority 1001
az vm open-port --resource-group myResourceGroupJenkins --name myVM --port 1337 --priority 1002
```


## <a name="configure-jenkins"></a>配置 Jenkins
若要访问 Jenkins 实例，请获取 VM 的公共 IP 地址：

```azurecli-interactive 
az vm show --resource-group myResourceGroupJenkins --name myVM -d --query [publicIps] --o tsv
```

出于安全考虑，需要输入 VM 上某个文本文件中存储的初始管理员密码来启动 Jenkins 安装。 使用上一步骤中获取的公共 IP 地址通过 SSH 连接到 VM：

```bash
ssh azureuser@<publicIps>
```

查看并复制 Jenkins 安装程序的 `initialAdminPassword`：

```bash
sudo cat /var/lib/jenkins/secrets/initialAdminPassword
```

如果文件尚不可用，请再等待几分钟，以便 cloud-init 完成 Jenkins 和 Docker 安装。

现在，请打开 Web 浏览器并转到 `http://<publicIps>:8080`。 按如下所示完成初始 Jenkins 安装：

- 输入用户名“admin”，然后提供在上一步骤从 VM 获取的 initialAdminPassword。
- 依次选择“管理 Jenkins”、“管理插件”。
- 选择“可用”，然后在顶部文本框中搜索 GitHub。 选中“GitHub 插件”框，然后选择“立即下载并在重启后安装”。
- 选中“安装完成并且没有作业运行时重启 Jenkins”框，然后等待插件安装过程完成。


## <a name="create-github-webhook"></a>创建 GitHub Webhook
若要配置与 GitHub 的集成，请从 Azure 示例存储库中打开 [Node.js Hello World 示例应用](https://github.com/Azure-Samples/nodejs-docs-hello-world)。 若要将存储库分叉到自己的 GitHub 帐户，请选择右上角的“分叉”按钮。

在创建的分叉中创建 Webhook：

- 选择“设置”，然后在左侧选择“集成和服务”。
- 选择“添加服务”，并在筛选框中输入 Jenkins。
- 选择“Jenkins (GitHub 插件)”
- 对于“Jenkins 挂钩 URL”，请输入 `http://<publicIps>:8080/github-webhook/`。 请确保包含尾部的 /
- 选择“添加服务”

![将 GitHub Webhook 添加到分叉的存储库](media/tutorial-jenkins-github-docker-cicd/github_webhook.png)


## <a name="create-jenkins-job"></a>创建 Jenkins 作业
若要让 Jenkins 对 GitHub 中的事件（例如提交代码）做出响应，请创建 Jenkins 作业。 

在 Jenkins 网站中的主页上，选择“创建新作业”：

- 输入 *HelloWorld* 作为作业名称。 选择“自由风格项目”，然后选择“确定”。
- 在“常规”部分下面，选择“GitHub”项目并输入分叉的存储库的 URL，例如 *https://github.com/iainfoulds/nodejs-docs-hello-world*
- 在“源代码管理”部分下面，选择“Git”并输入分叉的存储库 *.git* 的 URL，例如 *https://github.com/iainfoulds/nodejs-docs-hello-world.git*
- 在“生成触发器”部分下面，选择“用于 GITscm 轮询的 GitHub 挂钩触发器”。
- 在“生成”部分下面，选择“添加生成步骤”。 选择“执行 shell”，并在命令窗口中输入 `echo "Testing"`。
- 选择作业窗口底部的“保存”。


## <a name="test-github-integration"></a>测试 GitHub 集成
若要测试 GitHub 与 Jenkins 的集成，请提交分叉中的更改。 

返回到 GitHub Web UI，选择分叉的存储库，然后选择“index.js”文件。 选择铅笔图标编辑该文件，使第 6 行的内容如下：

```nodejs
response.end("Hello World!");
```

若要提交更改，请选择底部的“提交更改”按钮。

在 Jenkins 中，作业页左下角的“生成历史记录”部分下面启动了一个新的生成。 选择生成号链接，并选择左侧的“控制台输出”。 从 GitHub 提取代码以及生成操作将消息 `Testing` 输出到控制台时，可以查看 Jenkins 执行的步骤。 每次在 GitHub 中提交内容时，Webhook 都以此方式访问 Jenkins 并触发新的生成。


## <a name="define-docker-build-image"></a>定义 Docker 生成映像
为了查看基于 GitHub 提交内容运行的 Node.js 应用，让我们生成一个 Docker 映像用于运行该应用。 该映像是从定义如何配置运行应用的容器的 Dockerfile 生成的。 

通过 SSH 连接到 VM 后，请切换到根据上一步骤创建的作业命名的 Jenkins 工作区目录。 在本示例中，该目录名为 HelloWorld。

```bash
cd /var/lib/jenkins/workspace/HelloWorld
```

在此工作区目录中创建包含 `sudo sensible-editor Dockerfile` 的文件并粘贴以下内容。 请确保已正确复制整个 Dockerfile，尤其是第一行：

```yaml
FROM node:alpine

EXPOSE 1337

WORKDIR /var/www
COPY package.json /var/www/
RUN npm install
COPY index.js /var/www/
```

此 Dockerfile 使用基本 Node.js 映像（该映像使用 Alpine Linux），公开运行 Hello World 应用的端口 1337，复制应用文件并初始化应用。


## <a name="create-jenkins-build-rules"></a>创建 Jenkins 生成规则
在上一步骤中，已创建一个可将消息输出到控制台的基本 Jenkins 生成规则。 让我们创建生成步骤以使用 Dockerfile 并运行应用。

返回到 Jenkins 实例，选择上一步骤创建的作业。 选择左侧的“配置”，并向下滚动到“生成”部分：

- 删除现有的 `echo "Test"` 生成步骤。 选择现有生成步骤框右上角的红叉。
- 选择“添加生成步骤”，然后选择“执行 shell”
- 在“命令”框中输入以下 Docker 命令，然后选择“保存”：

  ```bash
  docker build --tag helloworld:$BUILD_NUMBER .
  docker stop helloworld && docker rm helloworld
  docker run --name helloworld -p 1337:1337 helloworld:$BUILD_NUMBER node /var/www/index.js &
  ```

Docker 生成步骤将创建一个映像，并使用 Jenkins 生成编号对其进行标记，以便可以维护映像的历史记录。 运行应用的任何现有容器会停止，随后被删除。 然后，将使用该映像启动新的容器，并基于 GitHub 中的最新提交内容运行 Node.js 应用。


## <a name="test-your-pipeline"></a>测试管道
若要查看整个管道的工作状况，请再次在分叉的 GitHub 存储库中编辑 index.js 文件，并选择“提交更改”。 会基于 GitHub 的 Webhook 在 Jenkins 中启动新作业。 创建 Docker 映像并在新容器中启动应用需要几秒钟时间。

如果需要，请再次获取 VM 的公共 IP 地址：

```azurecli-interactive 
az vm show --resource-group myResourceGroupJenkins --name myVM -d --query [publicIps] --o tsv
```

打开 Web 浏览器并输入 `http://<publicIps>:1337`。 将显示 Node.js 应用，其中反映了 GitHub 分叉中的最新提交内容，如下所示：

![运行 Node.js 应用](media/tutorial-jenkins-github-docker-cicd/running_nodejs_app.png)

现在，请再次在 GitHub 中编辑 *index.js*，并提交更改。 等待几秒钟让作业在 Jenkins 中完成，并刷新 Web 浏览器查看新容器中运行的应用的更新版本，如下所示：

![在 GitHub 中再次提交更改后运行 Node.js 应用](media/tutorial-jenkins-github-docker-cicd/another_running_nodejs_app.png)


## <a name="next-steps"></a>后续步骤
在本教程中，已将 GitHub 配置为每次提交代码后运行 Jenkins 生成作业，然后部署了一个 Docker 容器用于测试应用。 你已了解如何：

> [!div class="checklist"]
> * 创建 Jenkins VM
> * 安装并配置 Jenkins
> * 创建 GitHub 与 Jenkins 之间的 Webhook 集成
> * 通过 GitHub 提交创建并触发 Jenkins 生成作业
> * 创建应用的 Docker 映像
> * 验证 GitHub 提交是否生成新的 Docker 映像并更新正在运行的应用

转到下一教程，详细了解如何将 Jenkins 与 Visual Studio Team Services 集成。

> [!div class="nextstepaction"]
> [使用 Jenkins 和 Team Services 部署应用](tutorial-build-deploy-jenkins.md)
