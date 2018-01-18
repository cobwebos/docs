---
title: "在 Linux 上创建 Azure Service Fabric 容器应用程序 | Microsoft Docs"
description: "在 Azure Service Fabric 上创建第一个 Linux 容器应用程序。  生成包含应用程序的 Docker 映像，将该映像推送到容器注册表，然后生成并部署 Service Fabric 容器应用程序。"
services: service-fabric
documentationcenter: linux
author: suhuruli
manager: timlt
editor: 
ms.assetid: 
ms.service: service-fabric
ms.devlang: python
ms.topic: quickstart
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 09/05/2017
ms.author: suhuruli
ms.custom: mvc
ms.openlocfilehash: 23cc9ce855eeba9e9a365e42beeee01b09f0fee3
ms.sourcegitcommit: c4cc4d76932b059f8c2657081577412e8f405478
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/11/2018
---
# <a name="deploy-an-azure-service-fabric-linux-container-application-on-azure"></a>在 Azure 上部署 Azure Service Fabric Linux 容器应用程序
Azure Service Fabric 是一款分布式系统平台，可用于部署和管理可缩放的可靠微服务和容器。 

本快速入门介绍如何将 Linux 容器部署到 Service Fabric 群集。 完成后，Service Fabric 群集中会运行一个由 Python Web 前端和 Redis 后端组成的投票应用程序。 

![quickstartpic][quickstartpic]

此快速入门介绍如何：
> [!div class="checklist"]
> * 将容器部署到 Azure Linux Service Fabric 群集
> * 在 Service Fabric 中缩放和故障转移容器

## <a name="prerequisite"></a>先决条件
如果你还没有 Azure 订阅，可以在开始前创建一个 [免费帐户](https://azure.microsoft.com/free/)。
  
[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

如果选择在本地安装并使用命令行接口 (CLI)，请确保运行 Azure CLI 2.0.4 或更高版本。 若要查找版本，请运行 az --version。 如果需要进行安装或升级，请参阅[安装 Azure CLI 2.0](https://docs.microsoft.com/cli/azure/install-azure-cli)。

## <a name="get-application-package"></a>获取应用程序包
若要将容器部署到 Service Fabric，需要一组描述各个容器以及应用程序的清单文件（应用程序定义）。

在 Cloud Shell 中，使用 git 克隆一份应用程序定义。

```azurecli-interactive
git clone https://github.com/Azure-Samples/service-fabric-containers.git

cd service-fabric-containers/Linux/container-tutorial/Voting
```

## <a name="deploy-the-containers-to-a-service-fabric-cluster-in-azure"></a>将容器部署到 Azure 中的 Service Fabric 群集
若要将应用程序部署到 Azure 中的群集，可以使用自己的群集，或使用合作群集。

> [!Note]
> 必须将应用程序部署到 Azure 中的群集，而不是本地开发计算机上的 Service Fabric 群集。 
>

合作群集是 Azure 上托管的免费限时 Service Fabric 群集。 这些群集由 Service Fabric 团队维护，任何人都可以在其中部署应用程序和了解平台。 若要使用合作群集，请[遵照说明](http://aka.ms/tryservicefabric)。 

若要了解如何创建自己的群集，请参阅[在 Azure 上创建 Service Fabric 群集](service-fabric-tutorial-create-vnet-and-linux-cluster.md)。

> [!Note]
> Web 前端服务配置为侦听端口 80 上是否有传入流量。 请确保此端口在群集中处于打开状态。 如果使用的是合作群集，此端口已处于打开状态。
>

### <a name="install-service-fabric-command-line-interface-and-connect-to-your-cluster"></a>安装 Service Fabric 命令行界面，然后连接到群集
在 CLI 环境中安装 [Service Fabric CLI (sfctl)](service-fabric-cli.md)

```azurecli-interactive
pip3 install --user sfctl 
export PATH=$PATH:~/.local/bin
```

使用 Azure CLI 连接到 Azure 中的 Service Fabric 群集。 终结点是群集的管理终结点 - 例如 `http://linh1x87d1d.westus.cloudapp.azure.com:19080`。

```azurecli-interactive
sfctl cluster select --endpoint http://linh1x87d1d.westus.cloudapp.azure.com:19080
```

### <a name="deploy-the-service-fabric-application"></a>部署 Service Fabric 应用程序 
Service Fabric 容器应用程序可以使用所述的 Service Fabric 应用程序包或 Docker Compose 进行部署。 

#### <a name="deploy-using-service-fabric-application-package"></a>使用 Service Fabric 应用程序包进行部署
使用提供的安装脚本将投票应用程序定义复制到群集，注册应用程序类型，并创建应用程序的实例。

```azurecli-interactive
./install.sh
```

#### <a name="deploy-the-application-using-docker-compose"></a>使用 Docker Compose 部署应用程序
使用 Docker Compose 和以下命令在 Service Fabric 群集上部署和安装应用程序。
```azurecli-interactive
sfctl compose create --deployment-name TestApp --file-path docker-compose.yml
```

打开浏览器并导航到 Service Fabric Explorer，其网址为 http://\<Azure Service Fabric 群集 URL>:19080/Explorer - 例如 `http://linh1x87d1d.westus.cloudapp.azure.com:19080/Explorer`。 展开“应用程序”节点，可以看到投票应用程序类型的条目以及创建的实例。

![Service Fabric Explorer][sfx]

连接到正在运行的容器。  打开 Web 浏览器并指向群集的 URL - 例如 `http://linh1x87d1d.westus.cloudapp.azure.com:80`。 浏览器中应会显示该投票应用程序。

![quickstartpic][quickstartpic]

## <a name="fail-over-a-container-in-a-cluster"></a>故障转移群集中的容器
Service Fabric 可确保在发生故障时，将容器实例自动转移到群集中的其他节点。 也可以手动清空容器的节点，然后将其正常转移到群集中的其他节点。 可通过多种方式缩放服务，在本示例中，我们将使用 Service Fabric Explorer。

若要故障转移前端容器，请执行以下步骤：

1. 在群集中打开 Service Fabric Explorer - 例如 `http://linh1x87d1d.westus.cloudapp.azure.com:19080/Explorer`。
2. 在树视图中单击“fabric:/Voting/azurevotefront”节点，展开分区节点（由 GUID 表示）。 注意树视图中的节点名称，它显示了当前正在运行容器的节点 - 例如 `_nodetype_4`
3. 在树视图中展开“节点”节点。 单击正在运行容器的节点旁边的省略号（三个点）。
4. 选择“重启”以重启该节点，并确认重启操作。 重启会导致容器故障转移到群集中的另一个节点。

![sfxquickstartshownodetype][sfxquickstartshownodetype]

## <a name="scale-applications-and-services-in-a-cluster"></a>在群集中缩放应用程序和服务
可以跨群集轻松缩放 Service Fabric 服务，以适应服务上的负载。 可以通过更改群集中运行的实例数量来缩放服务。

若要缩放 Web 前端服务，请按照以下步骤操作：

1. 在群集中打开 Service Fabric Explorer（例如，`http://linh1x87d1d.westus.cloudapp.azure.com:19080`）。
2. 在树视图中单击“fabric:/Voting/azurevotefront”节点旁边的省略号（三个点），选择“缩放服务”。

    ![containersquickstartscale][containersquickstartscale]

  现在可以缩放 Web 前端服务的实例数量。

3. 将数字更改为 2，再单击“缩放服务”。
4. 在树视图中单击“fabric:/Voting/azurevotefront”节点，展开分区节点（由 GUID 表示）。

    ![containersquickstartscaledone][containersquickstartscaledone]

    现在可以看到该服务有两个实例。 在树视图中，可以看到运行实例的节点。

通过这一简单的管理任务，我们让前端服务用来处理用户负载的资源数量翻了一番。 有必要了解的是，服务无需有多个实例，便能可靠运行。 如果服务出现故障，Service Fabric 可确保在群集中运行新的服务实例。

## <a name="clean-up"></a>清理
使用模板中提供的卸载脚本从群集中删除应用程序实例并取消注册应用程序类型。 此命令会花费一段时间来清理实例，完成此脚本后，不应紧接着运行 'install'sh' 命令。 

```bash
./uninstall.sh
```

## <a name="next-steps"></a>后续步骤
在此快速入门中，读者学习了如何：
> [!div class="checklist"]
> * 将 Linux 容器应用程序部署到 Azure
> * 故障转移 Service Fabric 群集中的容器
> * 缩放 Service Fabric 群集中的容器

* 详细了解如何运行 [Service Fabric 上的容器](service-fabric-containers-overview.md)。
* 了解 Service Fabric [应用程序生命周期](service-fabric-application-lifecycle.md)。
* 查看 GitHub 上的 [Service Fabric 容器代码示例](https://github.com/Azure-Samples/service-fabric-containers)。

[sfx]: ./media/service-fabric-quickstart-containers-linux/containersquickstartappinstance.png
[quickstartpic]: ./media/service-fabric-quickstart-containers-linux/votingapp.png
[sfxquickstartshownodetype]:  ./media/service-fabric-quickstart-containers-linux/containersquickstartrestart.png
[containersquickstartscale]: ./media/service-fabric-quickstart-containers-linux/containersquickstartscale.png
[containersquickstartscaledone]: ./media/service-fabric-quickstart-containers-linux/containersquickstartscaledone.png
