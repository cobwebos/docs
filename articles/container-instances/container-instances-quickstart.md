---
title: 快速入门 - 将 Docker 容器部署到容器实例 - Azure CLI
description: 本快速入门将使用 Azure CLI 快速部署在隔离的 Azure 容器实例中运行的容器化 Web 应用
ms.topic: quickstart
ms.date: 03/21/2019
ms.custom:
- seo-python-october2019
- seodec18
- mvc
ms.openlocfilehash: e5cad7d9141963e5062423545f7e5b94f0575152
ms.sourcegitcommit: e4c33439642cf05682af7f28db1dbdb5cf273cc6
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/03/2020
ms.locfileid: "78252192"
---
# <a name="quickstart-deploy-a-container-instance-in-azure-using-the-azure-cli"></a>快速入门：使用 Azure CLI 在 Azure 中部署容器实例

使用 Azure 容器实例在 Azure 中快速方便地运行无服务器 Docker 容器。 当你不需要像 AzureKubernetes 服务这样的完整容器业务流程平台时，可以按需将应用程序部署到容器实例。

本快速入门将使用 Azure CLI 部署一个独立的 Docker 容器，并使其应用程序可通过完全限定的域名 (FQDN) 使用。 在执行单个部署命令几秒钟之后，可以浏览到正在容器中运行的应用程序：

![在浏览器中显示已部署到 Azure 容器实例的应用][aci-app-browser]

如果没有 Azure 订阅，请在开始之前创建一个[免费帐户][azure-account]。

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

可以使用 Azure Cloud Shell 或 Azure CLI 的本地安装完成本快速入门。 如果想要在本地使用它，建议使用 2.0.55 版或更高版本。 运行 `az --version` 即可查找版本。 如果需要进行安装或升级，请参阅[安装 Azure CLI][azure-cli-install]。

## <a name="create-a-resource-group"></a>创建资源组

Azure 容器实例（例如所有 Azure 资源）都必须部署到资源组中。 使用资源组可以组织和管理相关的 Azure 资源。

首先，使用以下 [az group create][az-group-create] 命令在 eastus  位置中创建一个名为“myResourceGroup”  的资源组：

```azurecli-interactive
az group create --name myResourceGroup --location eastus
```

## <a name="create-a-container"></a>创建容器

创建资源组后，可在 Azure 中运行容器。 若要使用 Azure CLI 创建容器实例，请在 [az container create][az-container-create] 命令中提供资源组名称、容器实例名称和 Docker 容器映像。 本快速入门将使用公共 `mcr.microsoft.com/azuredocs/aci-helloworld` 映像。 此映像打包了一个用 Node.js 编写的小型 Web 应用程序，该应用程序提供静态 HTML 页面。

可以通过指定要打开的一个或多个端口、一个 DNS 名称标签（或同时指定两者）来向 Internet 公开容器。 在本快速入门中，你将部署一个具有 DNS 名称标签的容器，以便 Web 应用可供公开访问。

执行类似于以下的命令以启动容器实例。 设置在创建实例的 Azure 区域中唯一的 `--dns-name-label` 值。 如果收到“DNS 名称标签不可用”错误消息，请尝试使用一个不同的 DNS 名称标签。

```azurecli-interactive
az container create --resource-group myResourceGroup --name mycontainer --image mcr.microsoft.com/azuredocs/aci-helloworld --dns-name-label aci-demo --ports 80
```

在几秒钟内，你应当会从 Azure CLI 收到响应，它指出部署已完成。 使用 [az container show][az-container-show] 命令检查其状态：

```azurecli-interactive
az container show --resource-group myResourceGroup --name mycontainer --query "{FQDN:ipAddress.fqdn,ProvisioningState:provisioningState}" --out table
```

运行此命令时，会显示容器的完全限定域名 (FQDN) 及其预配状态。

```output
FQDN                               ProvisioningState
---------------------------------  -------------------
aci-demo.eastus.azurecontainer.io  Succeeded
```

如果容器的 `ProvisioningState` 为 **Succeeded**，则在浏览器中转到其 FQDN。 如果看到类似于下图的网页，那么恭喜你！ 现已成功将 Docker 容器中运行的应用程序部署到 Azure。

![在浏览器中显示已部署到 Azure 容器实例的应用][aci-app-browser]

如果应用程序起初未显示，你可能需要在 DNS 传播时等待几秒钟，然后刷新浏览器。

## <a name="pull-the-container-logs"></a>拉取容器日志

当需要对容器或它运行的应用程序进行故障排除时（或者只是查看其输出），请首先查看容器实例的日志。

使用 [az container logs][az-container-logs] 命令拉取容器实例日志：

```azurecli-interactive
az container logs --resource-group myResourceGroup --name mycontainer
```

此输出显示容器的日志，并应显示在浏览器中查看应用程序时生成的 HTTP GET 请求。

```output
listening on port 80
::ffff:10.240.255.55 - - [21/Mar/2019:17:43:53 +0000] "GET / HTTP/1.1" 304 - "-" "Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/72.0.3626.121 Safari/537.36"
::ffff:10.240.255.55 - - [21/Mar/2019:17:44:36 +0000] "GET / HTTP/1.1" 304 - "-" "Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/72.0.3626.121 Safari/537.36"
::ffff:10.240.255.55 - - [21/Mar/2019:17:44:36 +0000] "GET / HTTP/1.1" 304 - "-" "Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/72.0.3626.121 Safari/537.36"
```

## <a name="attach-output-streams"></a>附加输出流

除了查看日志之外，还可将本地标准输出和标准错误流附加到容器的数据流中。

首先，执行 [az container attach][az-container-attach] 命令来将本地控制台附加到容器的输出流：

```azurecli-interactive
az container attach --resource-group myResourceGroup --name mycontainer
```

附加后，刷新浏览器数次，以生成其他一些输出。 完成后，使用 `Control+C` 分离控制台。 会得到类似于下面的输出：

```output
Container 'mycontainer' is in state 'Running'...
(count: 1) (last timestamp: 2019-03-21 17:27:20+00:00) pulling image "mcr.microsoft.com/azuredocs/aci-helloworld"
(count: 1) (last timestamp: 2019-03-21 17:27:24+00:00) Successfully pulled image "mcr.microsoft.com/azuredocs/aci-helloworld"
(count: 1) (last timestamp: 2019-03-21 17:27:27+00:00) Created container
(count: 1) (last timestamp: 2019-03-21 17:27:27+00:00) Started container

Start streaming logs:
listening on port 80

::ffff:10.240.255.55 - - [21/Mar/2019:17:43:53 +0000] "GET / HTTP/1.1" 304 - "-" "Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/72.0.3626.121 Safari/537.36"
::ffff:10.240.255.55 - - [21/Mar/2019:17:44:36 +0000] "GET / HTTP/1.1" 304 - "-" "Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/72.0.3626.121 Safari/537.36"
::ffff:10.240.255.55 - - [21/Mar/2019:17:44:36 +0000] "GET / HTTP/1.1" 304 - "-" "Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/72.0.3626.121 Safari/537.36"
::ffff:10.240.255.55 - - [21/Mar/2019:17:47:01 +0000] "GET / HTTP/1.1" 304 - "-" "Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/72.0.3626.121 Safari/537.36"
::ffff:10.240.255.56 - - [21/Mar/2019:17:47:12 +0000] "GET / HTTP/1.1" 304 - "-" "Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/72.0.3626.121 Safari/537.36"
```

## <a name="clean-up-resources"></a>清理资源

使用完容器后，可使用 [az container delete][az-container-delete] 命令将其删除：

```azurecli-interactive
az container delete --resource-group myResourceGroup --name mycontainer
```

若要验证已删除该容器，请执行 [az container list](/cli/azure/container#az-container-list) 命令：

```azurecli-interactive
az container list --resource-group myResourceGroup --output table
```

mycontainer 容器不应出现在命令的输出中  。 如果资源组中没有任何其他容器，则不会显示任何输出。

如果已使用完 *myResourceGroup* 资源组及其包含的所有资源，请使用 [az group delete][az-group-delete] 命令将其删除：

```azurecli-interactive
az group delete --name myResourceGroup
```

## <a name="next-steps"></a>后续步骤

在本快速入门中，你已使用公共 Microsoft 映像创建了一个 Azure 容器实例。 若要基于专用 Azure 容器注册表生成容器映像并部署它，请继续学习 Azure 容器实例教程。

> [!div class="nextstepaction"]
> [Azure 容器实例教程](./container-instances-tutorial-prepare-app.md)

若要试用在 Azure 上的业务流程系统中运行容器的选项，请参阅 [Azure Kubernetes Service (AKS)][container-service] 快速入门。

<!-- IMAGES -->
[aci-app-browser]: ./media/container-instances-quickstart/view-an-application-running-in-an-azure-container-instance.png

<!-- LINKS - External -->
[app-github-repo]: https://github.com/Azure-Samples/aci-helloworld.git
[azure-account]: https://azure.microsoft.com/free/
[node-js]: https://nodejs.org

<!-- LINKS - Internal -->
[az-container-attach]: /cli/azure/container#az-container-attach
[az-container-create]: /cli/azure/container#az-container-create
[az-container-delete]: /cli/azure/container#az-container-delete
[az-container-list]: /cli/azure/container#az-container-list
[az-container-logs]: /cli/azure/container#az-container-logs
[az-container-show]: /cli/azure/container#az-container-show
[az-group-create]: /cli/azure/group#az-group-create
[az-group-delete]: /cli/azure/group#az-group-delete
[azure-cli-install]: /cli/azure/install-azure-cli
[container-service]: ../aks/kubernetes-walkthrough.md
