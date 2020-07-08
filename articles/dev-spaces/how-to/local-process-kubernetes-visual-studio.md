---
title: 在 Visual Studio 中使用 Local Process with Kubernetes（预览版）
services: azure-dev-spaces
ms.date: 06/02/2020
ms.topic: conceptual
description: 了解如何通过 Visual Studio 使用带有 Kubernetes 的本地进程，将开发计算机连接到 Kubernetes 群集并 Azure Dev Spaces
keywords: 本地过程，其中包含 Kubernetes、Azure Dev Spaces、Dev Spaces、Docker、Kubernetes、Azure、AKS、Azure Kubernetes Service、容器
ms.openlocfilehash: 8e89baeac8d9042a87685ed1268ed694db8d9455
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: zh-CN
ms.lasthandoff: 07/02/2020
ms.locfileid: "84316548"
---
# <a name="use-local-process-with-kubernetes-with-visual-studio-preview"></a>在 Visual Studio 中使用 Local Process with Kubernetes（预览版）

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
* [Visual Studio 2019][visual-studio]版本 16.7 Preview 2 或更高版本在 Windows 10 上运行，安装了*ASP.NET 和 Web 开发*以及*Azure 开发*工作负荷。
* [安装 AZURE DEV SPACES CLI][azds-cli]。

### <a name="enable-the-local-process-with-kubernetes-preview-feature-in-visual-studio"></a>使用 Visual Studio 中的 Kubernetes 预览功能启用本地进程

若要在 Visual Studio 中使用 Kubernetes 启用本地进程，请单击 "*工具*" "选项" "  >  *Options*  >  *环境*  >  *预览功能*"。 选择 "*为 Kubernetes 服务启用本地调试*"。

此外，对于 .NET 控制台应用程序，请安装*VisualStudio* NuGet 包。

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

从 Visual Studio 中的[Azure Dev Spaces 自行车共享示例应用程序][bike-sharing-github]打开*dev-spaces/samples/BikeSharingApp/ReservationEngine///* 。

在项目中，从 "启动设置" 下拉列表中选择 "*具有 Kubernetes 的本地进程*"，如下所示。

![选择具有 Kubernetes 的本地进程](../media/local-process-kubernetes-visual-studio/choose-local-process.png)

单击*带有 Kubernetes 的本地进程*旁边的 "开始" 按钮。 在 "*具有 Kubernetes 的本地进程*" 对话框中：

* 选择订阅。
* 选择群集的*MyAKS* 。
* 选择命名空间的*dev* 。
* 选择要重定向的服务的*reservationengine* 。
* 选择用于启动配置文件的*应用程序*。
* 选择 `http://dev.bikesharingweb.EXTERNAL_IP.nip.io` 用于启动浏览器的 URL。

![选择具有 Kubernetes 群集的本地进程](../media/local-process-kubernetes-visual-studio/choose-local-process-cluster.png)

> [!IMPORTANT]
> 只能重定向只有一个 pod 的服务。

单击 "*保存并启动调试*"。

Kubernetes 群集中的所有流量都将*reservationengine*服务重定向到你的开发计算机中运行的应用程序的版本。 具有 Kubernetes 的本地进程还会将应用程序的所有出站流量路由回 Kubernetes 群集。

> [!NOTE]
> 系统将提示你允许*KubernetesDNSManager*以提升的权限运行，并修改你的 hosts 文件。

当状态栏显示您已连接到*reservationengine*服务时，您的开发计算机已连接。

![已连接开发计算机](../media/local-process-kubernetes-visual-studio/development-computer-connected.png)

> [!NOTE]
> 在 subesquent 上，将不会提示您通过 Kubernetes 对话框进行*本地过程*。 可在项目属性的 "*调试*" 窗格中更新这些设置。

当开发计算机连接后，流量将开始重定向到你要替换的服务的开发计算机。

## <a name="set-a-break-point"></a>设置断点

打开[BikesHelper.cs][bikeshelper-cs-breakpoint]并单击第26行的某个位置，将光标放在该处。 通过按*F9*或单击 "*调试*"，然后单击 "*切换断点*" 来设置断点。

通过打开公共 URL 导航到示例应用程序。 选择 " *Aurelia meets Briggs （客户）* " 作为用户，然后选择要出租的自行车。 单击 "*出租自行车*"。 返回到 Visual Studio 并突出显示第26行。 设置的断点已暂停第26行的服务。 若要恢复服务，请按 *F5*，或者依次单击“调试”、“继续”。  返回到你的浏览器并验证该页是否显示你已为自行车租赁。

通过将光标置于第26行 `BikesHelper.cs` 并达到*F9*，删除断点。

> [!NOTE]
> 默认情况下，停止调试任务还会断开开发计算机与 Kubernetes 群集的连接。 您可以更改此行为，方法是在调试选项的 " *Kubernetes 调试工具*" 部分中将 "*调试后断开连接*" 更改为*false* 。 更新此设置后，在停止并启动调试后，开发计算机将保持连接状态。 若要断开开发计算机与群集的连接，请单击工具栏上的 "*断开连接*" 按钮。
>
> 如果 Visual Studio 突然结束与群集的连接或终止连接，则在通过 Kubernetes 连接到本地进程之前，你正在重定向的服务可能不会还原为其原始状态。 若要解决此问题，请参阅[故障排除指南][troubleshooting]。

## <a name="using-logging-and-diagnostics"></a>使用日志记录和诊断

可以在 `Azure Dev Spaces` [开发计算机的*TEMP*目录][azds-tmp-dir]中的目录中找到诊断日志。

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
[bikeshelper-cs-breakpoint]: https://github.com/Azure/dev-spaces/blob/master/samples/BikeSharingApp/ReservationEngine/BikesHelper.cs#L26
[supported-regions]: https://azure.microsoft.com/global-infrastructure/services/?products=kubernetes-service
[troubleshooting]: ../troubleshooting.md#fail-to-restore-original-configuration-of-deployment-on-cluster
[visual-studio]: https://www.visualstudio.com/vs/