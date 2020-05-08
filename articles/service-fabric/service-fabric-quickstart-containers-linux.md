---
title: 在 Azure 中的 Service Fabric 上创建 Linux 容器应用
description: 在此快速入门中，将使用你的应用程序生成 Docker 映像、将映像推送到容器注册表，然后将容器部署到 Service Fabric 群集。
ms.topic: quickstart
ms.date: 07/22/2019
ms.author: suhuruli
ms.custom: mvc
ms.openlocfilehash: eb0a83d0110002cc32998af4083d06cf6e86e16a
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/29/2020
ms.locfileid: "75372680"
---
# <a name="quickstart-deploy-linux-containers-to-service-fabric"></a>快速入门：将 Linux 容器部署到 Service Fabric

Azure Service Fabric 是一款分布式系统平台，可用于部署和管理可缩放的可靠微服务和容器。

本快速入门介绍如何在 Azure 上将 Linux 容器部署到 Service Fabric 群集。 完成后，Service Fabric 群集中会运行一个由 Python Web 前端和 Redis 后端组成的 Voting 应用程序。 此外还介绍如何对群集中的应用程序进行故障转移和缩放。

![Voting 应用网页][quickstartpic]

## <a name="prerequisites"></a>必备条件

完成本快速入门教程需要：

1. 如果还没有订阅，请在开始前创建一个[免费 Azure 帐户](https://azure.microsoft.com/free/)。

2. 安装 [Azure CLI](/cli/azure/install-azure-cli-apt?view=azure-cli-latest)

3. 安装 [Service Fabric SDK 和 CLI](service-fabric-get-started-linux.md#installation-methods)

4. 安装 [Git](https://git-scm.com/)


## <a name="get-the-application-package"></a>获取应用程序包

若要将容器部署到 Service Fabric，需要一组描述各个容器以及应用程序的清单文件（应用程序定义）。

在控制台中，使用 git 克隆一份应用程序定义，然后将目录更改为克隆中的 `Voting` 目录。

```bash
git clone https://github.com/Azure-Samples/service-fabric-containers.git

cd service-fabric-containers/Linux/container-tutorial/Voting
```

## <a name="create-a-service-fabric-cluster"></a>创建 Service Fabric 群集

若要将应用程序部署到 Azure，需要通过 Service Fabric 群集来运行该应用程序。 以下命令在 Azure 中创建一个五节点群集。  这些命令还会创建一个自签名证书，将其添加到密钥保管库，并将证书下载到本地。 新证书用来在群集进行部署时保护群集，还用来对客户端进行身份验证。

```azurecli
#!/bin/bash

# Variables
ResourceGroupName="containertestcluster" 
ClusterName="containertestcluster" 
Location="eastus" 
Password="q6D7nN%6ck@6" 
Subject="containertestcluster.eastus.cloudapp.azure.com" 
VaultName="containertestvault" 
VmPassword="Mypa$$word!321"
VmUserName="sfadminuser"

# Login to Azure and set the subscription
az login

az account set --subscription <mySubscriptionID>

# Create resource group
az group create --name $ResourceGroupName --location $Location 

# Create secure five node Linux cluster. Creates a key vault in a resource group
# and creates a certficate in the key vault. The certificate's subject name must match 
# the domain that you use to access the Service Fabric cluster.  The certificate is downloaded locally.
az sf cluster create --resource-group $ResourceGroupName --location $Location --certificate-output-folder . --certificate-password $Password --certificate-subject-name $Subject --cluster-name $ClusterName --cluster-size 5 --os UbuntuServer1604 --vault-name $VaultName --vault-resource-group $ResourceGroupName --vm-password $VmPassword --vm-user-name $VmUserName
```

> [!Note]
> Web 前端服务配置为侦听端口 80 上是否有传入流量。 默认情况下，会在群集 VM 和 Azure 负载均衡器上打开端口 80。
>

## <a name="configure-your-environment"></a>配置环境

Service Fabric 提供多种可以用来管理群集及其应用程序的工具：

- Service Fabric Explorer，一种基于浏览器的工具。
- Service Fabric 命令行界面 (CLI)，在 Azure CLI 基础上运行。 
- PowerShell 命令。

在本快速入门中，请使用 Service Fabric CLI 和 Service Fabric Explorer（基于 Web 的工具）。 若要使用 Service Fabric Explorer，需将证书 PFX 文件导入到浏览器中。 默认情况下，PFX 文件没有密码。

Mozilla Firefox 是 Ubuntu 16.04 中的默认浏览器。 若要将证书导入 Firefox，请单击浏览器右上角的菜单按钮，然后单击“选项”。  在“首选项”页上，使用搜索框搜索“证书”。  单击“查看证书”，选择“你的证书”选项卡，单击“导入”，然后按提示导入证书。   

   ![在 Firefox 上安装证书](./media/service-fabric-quickstart-containers-linux/install-cert-firefox.png)

## <a name="deploy-the-service-fabric-application"></a>部署 Service Fabric 应用程序

1. 使用 CLI 连接到 Azure 中的 Service Fabric 群集。 此终结点是群集的管理终结点。 已在上一部分创建 PEM 文件。 

    ```bash
    sfctl cluster select --endpoint https://containertestcluster.eastus.cloudapp.azure.com:19080 --pem containertestcluster22019013100.pem --no-verify
    ```

2. 使用安装脚本将 Voting 应用程序定义复制到群集，注册应用程序类型，并创建应用程序的实例。  PEM 证书应该与 *install.sh* 文件位于同一目录中。

    ```bash
    ./install.sh
    ```

3. 打开 Web 浏览器，导航到群集的 Service Fabric Explorer 终结点。 终结点的格式如下：**https://\<my-azure-service-fabric-cluster-url>:19080/Explorer**，例如 `https://containertestcluster.eastus.cloudapp.azure.com:19080/Explorer`。 </br>

4. 展开“应用程序”节点，可以看到 Voting 应用程序类型的条目以及创建的实例。 

    ![Service Fabric Explorer][sfx]

5. 若要连接到正在运行的容器，请打开 Web 浏览器，导航到群集的 URL，例如 `http://containertestcluster.eastus.cloudapp.azure.com:80`。 浏览器中应会显示该投票应用程序。

    ![Voting 应用网页][quickstartpic]

> [!NOTE]
> 也可使用 Docker Compose 来部署 Service Fabric 应用程序。 例如，可以使用 Docker Compose 通过以下命令在群集上部署和安装应用程序。
>  ```bash
> sfctl compose create --deployment-name TestApp --file-path ../docker-compose.yml
> ```

## <a name="fail-over-a-container-in-a-cluster"></a>故障转移群集中的容器

Service Fabric 可确保在发生故障时，将容器实例自动转移到群集中的其他节点。 也可以手动清空容器的节点，然后将其正常转移到群集中的其他节点。 Service Fabric 提供多种缩放服务的方式。 在以下步骤中，请使用 Service Fabric Explorer。

若要故障转移前端容器，请执行以下步骤：

1. 在群集中打开 Service Fabric Explorer，例如 `https://containertestcluster.eastus.cloudapp.azure.com:19080/Explorer`。
2. 在树视图中单击“fabric:/Voting/azurevotefront”节点，展开分区节点（以 GUID 表示）。  注意树视图中的节点名称，它显示了当前正在运行容器的节点，例如 `_nodetype_1`。
3. 在树视图中展开“节点”节点。  单击正在运行容器的节点旁边的省略号 (...)。
4. 选择“重启”  以重启该节点，并确认重启操作。 重启会导致容器故障转移到群集中的另一个节点。

    ![Service Fabric Explorer 中的“节点”视图][sfxquickstartshownodetype]

## <a name="scale-applications-and-services-in-a-cluster"></a>在群集中缩放应用程序和服务

可以跨群集轻松缩放 Service Fabric 服务，以适应服务上的负载。 可以通过更改群集中运行的实例数量来缩放服务。

若要缩放 Web 前端服务，请按照以下步骤操作：

1. 在群集中打开 Service Fabric Explorer，例如 `https://containertestcluster.eastus.cloudapp.azure.com:19080`。
2. 在树视图中单击“fabric:/Voting/azurevotefront”节点旁边的省略号（三个点），选择“缩放服务”   。

    ![Service Fabric Explorer 缩放服务开始][containersquickstartscale]

    现在可以缩放 Web 前端服务的实例数量。

3. 将数字更改为 2  ，再单击“缩放服务”  。
4. 在树视图中单击“fabric:/Voting/azurevotefront”节点，展开分区节点（以 GUID 表示）。 

    ![Service Fabric Explorer 缩放服务完成][containersquickstartscaledone]

    现在可以看到该服务有两个实例。 在树视图中，可以看到运行实例的节点。

通过这一简单的管理任务，你已让前端服务用来处理用户负载的资源数量翻了一番。 有必要了解的是，服务无需多个实例便能可靠运行。 如果服务出现故障，Service Fabric 可确保在群集中运行新的服务实例。

## <a name="clean-up-resources"></a>清理资源

使用模板中提供的卸载脚本 (uninstall.sh) 从群集中删除应用程序实例并取消注册应用程序类型。 此脚本需要一定的时间来清理实例，因此不应在运行此脚本后立即运行 install script。 可以使用 Service Fabric Explorer 来确定实例何时已删除以及应用程序类型何时已取消注册。

```bash
./uninstall.sh
```

若要删除群集及其占用的所有资源，最简单的方式是删除资源组。

登录到 Azure，选择要删除群集的订阅 ID。 可通过登录到 Azure 门户查找订阅 ID。 使用 [az group delete 命令](/cli/azure/group?view=azure-cli-latest)删除资源组和所有群集资源。

```azurecli
az login
az account set --subscription <guid>
ResourceGroupName="containertestcluster"
az group delete --name $ResourceGroupName
```

如果群集已使用完毕，则可从证书存储中删除证书。 例如：
- 在 Windows 上：使用[“证书”MMC 管理单元](https://docs.microsoft.com/dotnet/framework/wcf/feature-details/how-to-view-certificates-with-the-mmc-snap-in)。 在添加管理单元时，确保选择“我的用户帐户”。  导航到 `Certificates - Current User\Personal\Certificates`，然后删除证书。
- 在 Mac 上：使用 Keychain 应用。
- 在 Ubuntu 上：按照查看证书时所使用的步骤删除此证书。

## <a name="next-steps"></a>后续步骤

在本快速入门中，你已将 Linux 容器应用程序部署到 Azure 中的 Service Fabric 群集，在应用程序上执行了故障转移，并在群集中缩放了应用程序。 若要详细了解如何在 Service Fabric 中使用 Linux 容器，请继续学习适用于 Linux 容器应用的教程。

> [!div class="nextstepaction"]
> [创建 Linux 容器应用](./service-fabric-tutorial-create-container-images.md)

[sfx]: ./media/service-fabric-quickstart-containers-linux/containersquickstartappinstance.png
[quickstartpic]: ./media/service-fabric-quickstart-containers-linux/votingapp.png
[sfxquickstartshownodetype]:  ./media/service-fabric-quickstart-containers-linux/containersquickstartrestart.png
[containersquickstartscale]: ./media/service-fabric-quickstart-containers-linux/containersquickstartscale.png
[containersquickstartscaledone]: ./media/service-fabric-quickstart-containers-linux/containersquickstartscaledone.png
