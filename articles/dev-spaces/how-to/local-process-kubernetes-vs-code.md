---
title: 在 Visual Studio Code 中使用 Local Process with Kubernetes（预览版）
services: azure-dev-spaces
ms.date: 06/02/2020
ms.topic: conceptual
description: 了解如何通过 Kubernetes 使用本地进程将开发计算机连接到 Kubernetes 群集，并提供 Azure Dev Spaces
keywords: 本地过程，其中包含 Kubernetes、Azure Dev Spaces、Dev Spaces、Docker、Kubernetes、Azure、AKS、Azure Kubernetes Service、容器
ms.openlocfilehash: 23a94528ffa4e9e412f472349ea26d1a14003616
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: zh-CN
ms.lasthandoff: 07/02/2020
ms.locfileid: "84316458"
---
# <a name="use-local-process-with-kubernetes-with-visual-studio-code-preview"></a>在 Visual Studio Code 中使用 Local Process with Kubernetes（预览版）

借助 Kubernetes 的本地进程，你可以在开发计算机上运行和调试代码，同时仍与应用程序或服务的其余部分连接到 Kubernetes 群集。 例如，如果你有一个具有许多相互依赖的服务和数据库的大型微服务体系结构，则在开发计算机上复制这些依赖项可能会很困难。 此外，对于在内部循环开发期间每个代码更改，生成和部署代码到 Kubernetes 群集可能会很慢、耗时且难以与调试程序一起使用。

使用 Kubernetes 的本地进程可以通过直接在开发计算机与群集之间创建连接，来避免生成代码并将其部署到群集。 通过在调试时将开发计算机连接到群集，可以在完整应用程序的上下文中快速测试和开发服务，而无需创建任何 Docker 或 Kubernetes 配置。

具有 Kubernetes 的本地进程将重定向已连接的 Kubernetes 群集与开发计算机之间的流量。 此流量重定向允许开发计算机上的代码和在 Kubernetes 群集中运行的服务进行通信，就像它们位于同一个 Kubernetes 群集中一样。 使用 Kubernetes 的本地进程还提供了一种方法，用于将环境变量和已装载的卷复制到开发计算机的 Kubernetes 群集中的 pod。 通过提供对开发计算机上的环境变量和已装入卷的访问，可以快速处理代码，而无需手动复制这些依赖项。

在本指南中，你将了解如何在 Kubernetes 中使用本地进程来重定向 Kubernetes 群集与开发计算机上运行的代码之间的流量。 本指南还提供了一个脚本，用于在 Kubernetes 群集上部署包含多个微服务的大型示例应用程序。

> [!IMPORTANT]
> 此功能目前以预览版提供。 需同意[补充使用条款][preview-terms]才可使用预览版。 在正式版 (GA) 推出之前，此功能的某些方面可能会有所更改。

## <a name="before-you-begin"></a>开始之前

本指南使用[Azure Dev Spaces 自行车共享示例应用程序][bike-sharing-github]来演示如何将开发计算机连接到 Kubernetes 群集。 如果你已在 Kubernetes 群集上运行自己的应用程序，仍可以执行以下步骤，并使用你自己的服务的名称。

### <a name="prerequisites"></a>先决条件

* Azure 订阅。 如果没有 Azure 订阅，可以创建一个[免费帐户](https://azure.microsoft.com/free)。
* [已安装 Azure CLI][azure-cli]。
* [Visual Studio Code][vs-code]在 MacOS 或 Windows 10 上运行。
* [Azure Dev Spaces][azds-vs-code]扩展版本在 Visual Studio Code 中安装2.0.220200601 或更高版本。
* [安装 AZURE DEV SPACES CLI][azds-cli]。

## <a name="create-a-kubernetes-cluster"></a>创建 Kubernetes 群集

在[受支持的区域][supported-regions]中创建 AKS 群集。 以下命令创建名为 *MyResourceGroup* 的资源组，以及名为 *MyAKS* 的 AKS 群集。

```azurecli-interactive
az group create \
    --name MyResourceGroup \
    --location eastus

az aks create \
    --resource-group MyResourceGroup \
    --name MyAKS \
    --location eastus \
    --node-count 3 \
    --generate-ssh-keys
```

## <a name="install-the-sample-application"></a>安装示例应用程序

使用提供的脚本在群集上安装示例应用程序。 可以在开发计算机上或使用[Azure Cloud Shell][azure-cloud-shell]运行此脚本。

> [!IMPORTANT]
> 若要运行脚本，你必须拥有对群集的*所有者*或*参与者*访问权限。

```azurecli-interactive
git clone https://github.com/Azure/dev-spaces
cd dev-spaces/
chmod +x ./local-process-quickstart.sh
./local-process-quickstart.sh -g MyResourceGroup -n MyAKS
```

通过打开其公共 URL （显示在安装脚本的输出中），导航到运行群集的示例应用程序。

```console
$ ./local-process-quickstart.sh -g MyResourceGroup -n MyAKS
Defaulting Dev spaces repository root to current directory : ~/dev-spaces
Setting the Kube context
...
To try out the app, open the url:
dev.bikesharingweb.EXTERNAL_IP.nip.io
```

在上面的示例中，公共 URL 是 `dev.bikesharingweb.EXTERNAL_IP.nip.io` 。

## <a name="connect-to-your-cluster-and-debug-a-service"></a>连接到群集并调试服务

在开发计算机上，下载并配置 Kubernetes CLI，使用[az aks get 凭据][az-aks-get-credentials]连接到 Kubernetes 群集。

```azurecli
az aks get-credentials --resource-group MyResourceGroup --name MyAKS
```

从 Visual Studio Code 中的[Azure Dev Spaces 自行车共享示例应用程序][bike-sharing-github]打开*开发共享空间/示例/BikeSharingApp/自行车*。 打开 Azure Kubernetes 服务扩展，并选择*MyAKS*群集中的*dev*命名空间。

![选择命名空间](../media/local-process-kubernetes-vs-code/select-namespace.png)

使用 `npm install` 命令安装应用程序的依赖项。

```console
npm install
```

选择左侧的 "*调试*" 图标，然后选择顶部的 "*本地进程 With Kubernetes （预览版）* "。

![选择具有 Kubernetes 的本地进程](../media/local-process-kubernetes-vs-code/choose-local-process.png)

单击*带有 Kubernetes （预览版）的本地进程*旁边的 "开始" 按钮。 首次运行此启动配置时，系统将提示你配置要替换的服务、从开发计算机转发的端口以及要使用的启动任务。

选择 "*自行车*" 服务。

![选择服务](../media/local-process-kubernetes-vs-code/choose-service.png)

Kubernetes 群集中的所有流量都将从*自行车*服务重定向到在开发计算机上运行的应用程序的版本。 具有 Kubernetes 的本地进程还会将应用程序的所有出站流量路由回 Kubernetes 群集。

> [!IMPORTANT]
> 只能重定向只有一个 pod 的服务。

选择服务后，系统会提示你输入本地应用程序的 TCP 端口。 对于本示例，请输入*3000*。

![连接选择端口](../media/local-process-kubernetes-vs-code/choose-port.png)

选择 "*通过 NPM 启动*" 作为启动任务。

![连接选择启动任务](../media/local-process-kubernetes-vs-code/choose-launch.png)

> [!NOTE]
> 系统将提示你允许*KubernetesDNSManager*以提升的权限运行，并修改你的 hosts 文件。

当状态栏变为橙色并且 Dev Spaces 扩展显示已连接时，开发计算机将连接。

![已连接开发计算机](../media/local-process-kubernetes-vs-code/development-computer-connected.png)

> [!NOTE]
> Subesquent 启动时，系统不会提示你输入服务名称、端口或启动任务。 这些值保存在*上的 vscode/tasks.js*中。

当开发计算机连接后，流量将开始重定向到你要替换的服务的开发计算机。

## <a name="set-a-break-point"></a>设置断点

打开[server.js][server-js-breakpoint]并单击行233上的某个位置，将光标放在此处。 通过按*F9*或单击 "*运行*"，然后单击 "*切换断点*" 来设置断点。

通过打开公共 URL 导航到示例应用程序。 选择 " *Aurelia meets Briggs （客户）* " 作为用户，然后选择要出租的自行车。 请注意，自行车的图像不会加载。 返回 Visual Studio Code 并观察第233行。 设置的断点已暂停第233行的服务。 若要恢复服务，请按*F5*或单击 "*运行*"，然后*继续*。 返回到你的浏览器并验证你是否看到了自行车的占位符图像。

通过将光标置于第233行并达到 F9 来删除断点 `server.js` 。 *F9*

### <a name="update-your-application"></a>更新应用程序

编辑 `server.js` 以删除行234和235：

```javascript
    // Hard code image url *FIX ME*
    theBike.imageUrl = "/static/logo.svg";
```

此部分现在应如下所示：

```javascript
    var theBike = result;
    theBike.id = theBike._id;
    delete theBike._id;
```

保存更改，然后单击 "*运行*"，然后*重新启动调试*。 重新连接后，请刷新浏览器并验证是否不再显示自行车的占位符图像。

单击 "*运行*"，然后单击 "*停止调试*" 以停止调试器。

> [!NOTE]
> 默认情况下，停止调试任务还会断开开发计算机与 Kubernetes 群集的连接。 可以通过以下方法来更改此行为：在 Visual Studio Code 设置中进行*调试之后，使用 Kubernetes： Disconnect 搜索本地进程*，并删除*调试结束时自动断开连接*的复选标记。 更新此设置后，在停止并启动调试后，开发计算机将保持连接状态。 若要断开开发计算机与群集的连接，请在状态栏上单击 Azure Dev Spaces 扩展，然后选择 "*断开连接当前会话*"。
>
> 如果 Visual Studio Code 突然结束与群集的连接或终止连接，则在与 Kubernetes 连接到本地进程之前，您重定向的服务可能不会还原为其原始状态。 若要解决此问题，请参阅[故障排除指南][troubleshooting]。

## <a name="using-logging-and-diagnostics"></a>使用日志记录和诊断

当开发计算机连接到 Kubernetes 群集后，日志记录输出会写入到 " *Dev 空间*" 窗口。

![输出](../media/local-process-kubernetes-vs-code/output.png)

单击 "Azure Dev Spaces" 状态栏，然后选择 "*显示连接诊断信息*"。 此命令打印日志记录输出中的当前环境变量和 DNS 条目。

![诊断输出](../media/local-process-kubernetes-vs-code/output-diagnostics.png)

此外，还可以在 `Azure Dev Spaces` [开发计算机的*TEMP*目录][azds-tmp-dir]中的目录中找到诊断日志。

## <a name="remove-the-sample-application-from-your-cluster"></a>从群集中删除示例应用程序

使用提供的脚本从群集中删除示例应用程序。

```azurecli-interactive
./local-process-quickstart.sh -c -g MyResourceGroup -n MyAKS
```

## <a name="next-steps"></a>后续步骤

了解如何在拉取请求合并到存储库的主分支之前，使用 Azure Dev Spaces 和 GitHub 操作直接在 AKS 中测试拉取请求中的更改。

> [!div class="nextstepaction"]
> [Azure Kubernetes 服务 & GitHub 操作][gh-actions]

[azds-cli]: install-dev-spaces.md#install-the-client-side-tools
[azds-tmp-dir]: ../troubleshooting.md#before-you-begin
[azds-vs-code]: https://marketplace.visualstudio.com/items?itemName=azuredevspaces.azds
[azure-cli]: /cli/azure/install-azure-cli?view=azure-cli-latest
[azure-cloud-shell]: ../../cloud-shell/overview.md
[az-aks-get-credentials]: /cli/azure/aks?view=azure-cli-latest#az-aks-get-credentials
[az-aks-vs-code]: https://marketplace.visualstudio.com/items?itemName=ms-kubernetes-tools.vscode-aks-tools
[bike-sharing-github]: https://github.com/Azure/dev-spaces/tree/master/samples/BikeSharingApp
[gh-actions]: github-actions.md
[preview-terms]: https://azure.microsoft.com/support/legal/preview-supplemental-terms/
[server-js-breakpoint]: https://github.com/Azure/dev-spaces/blob/master/samples/BikeSharingApp/Bikes/server.js#L233
[supported-regions]: https://azure.microsoft.com/global-infrastructure/services/?products=kubernetes-service
[troubleshooting]: ../troubleshooting.md#fail-to-restore-original-configuration-of-deployment-on-cluster
[vs-code]: https://code.visualstudio.com/download