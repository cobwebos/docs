---
title: 将开发计算机连接到 AKS 群集（预览版）
services: azure-dev-spaces
ms.date: 11/04/2019
ms.topic: conceptual
description: 了解如何使用 Azure Dev Spaces 将开发计算机连接到 AKS 群集
keywords: Azure Dev Spaces，Dev 空间，Docker，Kubernetes，Azure，AKS，Azure Kubernetes 服务，容器
ms.openlocfilehash: 13e6f16e66941be0ae463e8280827dc0b8183450
ms.sourcegitcommit: 225a0b8a186687154c238305607192b75f1a8163
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/29/2020
ms.locfileid: "78196087"
---
# <a name="connect-your-development-machine-to-an-aks-cluster-preview"></a>将开发计算机连接到 AKS 群集（预览版）

Azure Dev Spaces 允许在开发计算机上使用或不使用容器来运行和调试代码，同时仍与应用程序或服务的其余部分连接到 Kubernetes 群集。 将开发计算机连接到群集可帮助快速开发应用程序并执行端到端测试，而无需创建任何 Docker 或 Kubernetes 配置。 你还可以连接到 AKS 群集，而不会影响可能使用同一群集的其他工作负荷或用户。

Azure Dev Spaces 重定向已连接 AKS 群集与开发计算机之间的流量。 此流量重定向允许开发计算机上的代码和在 AKS 群集中运行的服务进行通信，就像它们位于同一个 AKS 群集中一样。 由于你的代码在你的开发计算机上运行，因此你还可以在用于运行和调试该代码的开发工具中获得灵活性。 Azure Dev Spaces 还提供了一种方法，用于将环境变量和已装载文件复制到开发计算机的 AKS 群集中的 pod。

本指南介绍如何：

* 在 Azure 中的托管 Kubernetes 群集上设置 Azure Dev Spaces。
* 将包含多个微服务的大型应用程序部署到开发空间。
* 使用 Azure Dev Spaces 重定向 AKS 群集与开发计算机上运行的代码之间的流量。

> [!IMPORTANT]
> 此功能目前处于预览状态。 需同意[补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)才可使用预览版。 在正式版 (GA) 推出之前，此功能的某些方面可能会有所更改。

## <a name="before-you-begin"></a>开始之前

本指南使用[Azure Dev Spaces 自行车共享示例应用程序](https://github.com/Azure/dev-spaces/tree/master/samples/BikeSharingApp)来演示如何将开发计算机连接到 AKS 群集。 按照[Azure Dev Spaces 自行车共享示例应用程序自述文件](https://github.com/Azure/dev-spaces/blob/master/samples/BikeSharingApp/README.md)中的说明运行示例应用程序。 或者，如果你在 AKS 群集上拥有自己的应用程序，仍可以执行以下步骤，并使用你自己的服务和 pod 的名称。

### <a name="limitations"></a>限制

* 此时不支持 UDP。

### <a name="prerequisites"></a>必备条件

* Azure 订阅。 如果没有 Azure 订阅，可以创建一个[免费帐户](https://azure.microsoft.com/free)。
* [已安装 Azure CLI][azure-cli]。
* [Visual Studio Code][vs-code]在 MacOS 或 Windows 10 上安装和运行[Azure Dev Spaces][azds-vs-code]扩展。
* [Azure Dev Spaces 自行车共享示例应用程序](https://github.com/Azure/dev-spaces/tree/master/samples/BikeSharingApp)或你自己在 AKS 群集上运行的应用程序。

## <a name="connect-your-development-machine"></a>连接开发计算机

在 Visual Studio Code 中打开*dev/samples/BikeSharingApp/自行车*，并使用 Azure Dev Spaces 扩展将开发计算机连接到 AKS 群集。

若要使用 Azure Dev Spaces 扩展，请单击 "*查看*"，然后单击 "*命令面板*"，打开 Visual Studio Code 中的命令面板。 开始键入 "`Azure Dev Spaces: Redirect`"，然后单击 "`Azure Dev Spaces: Redirect an existing Kubernetes service to my machine [Preview]`"、"`Azure Dev Spaces: Redirect an existing Kubernetes pod to my machine [Preview]`" 或 "`Azure Dev Spaces: Redirect a new Kubernetes pod to my machine [Preview]`"。

![命令](../media/how-to-connect/connect-commands.png)

### <a name="select-a-redirection-option"></a>选择重定向选项

如果运行 `Azure Dev Spaces: Redirect an existing Kubernetes service to my machine [Preview]`，系统将要求你选择现有的 Kubernetes 服务：

![选择服务](../media/how-to-connect/connect-choose-service.png)

此选项将此服务的 AKS 群集中的所有流量重定向到你的开发计算机中运行的应用程序的版本。 如果此服务在 AKS 群集中运行多个 pod，则此服务的所有流量仅路由到您的开发计算机。 Azure Dev Spaces 还会将应用程序的所有出站流量路由回 AKS 群集。

如果运行 `Azure Dev Spaces: Redirect an existing Kubernetes pod to my machine [Preview]`，系统将要求你选择特定的 pod：

![选择 Pod](../media/how-to-connect/connect-choose-pod.png)

此选项连接到特定的 pod。 此选项可用于与不发送或接收流量以及终止复制盒的 pod 交互。 如果 pod 发送和接收流量，此选项的行为方式与 `Azure Dev Spaces: Redirect an existing Kubernetes service to my machine [Preview]` 类似，并将重定向 AKS 群集中所有与所选 pod 服务相关的所有 pod 的流量。

如果运行 `Azure Dev Spaces: Redirect a new Kubernetes pod to my machine [Preview]`，系统不会提示选择现有的 pod 或服务。 此选项将所有出站流量从开发计算机上运行的应用程序重定向到 AKS 群集。

对于本示例，请选择 "`Azure Dev Spaces: Redirect an existing Kubernetes service to my machine [Preview]`"，然后选择 "*自行车*" 服务。

### <a name="select-a-connection-mode"></a>选择连接模式

选择重定向选项后，系统会提示选择 "*替换*" 或 "*克隆*" 连接模式。

![替换或克隆](../media/how-to-connect/connect-replace-clone.png)

*Replace*选项将替换 AKS 群集中的当前 pod 或服务，并将该服务的所有流量重定向到你的开发计算机。 如果你在开发计算机上启动应用程序，则此选项可能会破坏 AKS 群集中与要重定向的服务进行交互的其他服务。 *Clone*选项允许选择现有的子开发人员空间，或创建新的子 dev 空间，以将 pod 或服务的流量重定向到开发计算机。 此选项可让你隔离，而不会中断其他服务，因为只有流向该子开发人员空间的流量将重定向到你的开发计算机。 *Clone*选项要求 AKS 群集启用 Azure Dev Spaces。

对于本示例，请选择 "*替换*"。

> [!NOTE]
> 如果现有服务的 pod 有多个容器，则系统还会提示你选择应用程序的容器。

### <a name="select-a-port-for-your-application"></a>选择应用程序的端口

选择连接模式后，系统会提示你输入本地应用程序的 TCP 端口。 如果你的应用程序打开多个端口，请用逗号分隔这些端口，例如*80，81*。 如果你的应用程序不接受任何网络请求，请输入*0*。 对于本示例，请输入*3000*。

![连接选择端口](../media/how-to-connect/connect-choose-port.png)

### <a name="confirm-you-are-connected"></a>确认已连接

选择应用程序的 TCP 端口后，Azure Dev Spaces 将建立与 AKS 群集的连接。 Azure Dev Spaces 将代理注入 AKS 群集，以便在 AKS 群集与开发计算机之间重定向流量。 建立此连接可能需要几分钟的时间。 Azure Dev Spaces 还将请求管理员访问权限，以便修改开发计算机中的*主机*文件。

> [!IMPORTANT]
> Azure Dev Spaces 与 AKS 群集建立连接后，AKS 群集中的其他服务可能无法正常工作，除非你选择 "*替换*连接模式"。 您可以改为选择*克隆*连接模式来创建重定向的子 dev 空间，并避免对父空间造成任何中断。 此外，如果你的服务具有在你的开发计算机中不可用的依赖项，则可能需要修改应用程序或提供[其他配置](#additional-configuration)

Azure Dev Spaces 在建立与 AKS 群集的连接后，将打开一个名为*AZDS*的终端窗口。 此终端窗口包含从 AKS 群集配置的所有环境变量和 DNS 条目。 在此终端窗口中或使用 Visual Studio Code 调试程序运行的任何代码都将连接到 AKS 群集。

![终端](../media/how-to-connect/connect-terminal.png)

此外，Azure Dev Spaces 会创建一个名为 "*开发共享空间*" 的窗口，并将其全部输出。

![输出](../media/how-to-connect/connect-output.png)

Azure Dev Spaces 还有一个显示连接状态的状态栏项目。

![状态](../media/how-to-connect/connect-status.png)

验证状态栏是否显示*Dev Spaces：连接到本地端口3000上的开发/自行车*。

### <a name="configure-your-application-on-your-development-machine"></a>在开发计算机上配置应用程序

打开*AZDS 连接-自行车*终端窗口并运行 `npm install`：

```console
$ npm install

> fsevents@1.2.9 install ./dev-spaces/samples/BikeSharingApp/Bikes/node_modules/fsevents
> node install
...
```

单击 "*调试*"，然后*打开 "配置*"。 如果系统提示选择环境，请选择 *"node.js"。* 这将创建一个 `.vscode/launch.json` 文件。 将该文件的内容替换为以下内容：

```json
{
    "configurations": [
        {
            "type": "node",
            "request": "launch",
            "name": "Launch via NPM",
            "runtimeExecutable": "npm",
            "runtimeArgs": [
                "run-script",
                "debug"
            ],
            "port": 9229
        }
    ]
}
```

打开[package](https://github.com/Azure/dev-spaces/blob/master/samples/BikeSharingApp/Bikes/package.json)并添加调试脚本：

```json
  "devDependencies": {
    "nodemon": "^1.18.10"
  },
  "scripts": {
    "debug": "node --nolazy --inspect-brk=9229 server.js"
  }
```

### <a name="start-your-application-on-your-development-machine"></a>在开发计算机上启动应用程序

单击左侧的 "*调试*" 图标，然后单击顶部的 "*通过 NPM 启动*" 旁边的 "开始" 按钮。

![通过 NPM 启动](../media/how-to-connect/launch-npm.png)

你的应用程序将启动并 Azure Dev Spaces 重定向 AKS 群集与开发计算机之间的流量。 在*调试控制台*中，你将看到如下所示的消息：

```console
/usr/local/bin/npm run-script debug 
...
Debugger attached.
Collection: bikes
MongoDB connection string: mongodb://databases-mongo
Connected to MongoDB
Listening on port 3000
```

单击 "Azure Dev Spaces" 状态栏，然后选择应用程序的 "公共 URL"，导航到 " *bikesharingweb* " 服务。 你还可以从前面运行的 `azds list-uris` 命令查找公共 URL。 如果未在群集上使用 Azure Dev Spaces，请使用应用程序的 IP 或 URL 作为正在使用的命名空间。 在以上示例中，*bikesharingweb* 服务的公共 URL 为 `http://dev.bikesharingweb.fedcab0987.eus.azds.io/`。 选择 " *Aurelia meets Briggs （客户）* " 作为用户，然后选择要出租的自行车。

### <a name="set-a-break-point"></a>设置断点

打开[node.js](https://github.com/Azure/dev-spaces/blob/master/samples/BikeSharingApp/Bikes/server.js#L233)并单击行233上的某个位置，将光标放在此处。 通过按*F9*或单击 "*调试*"，然后单击 "*切换断点*" 来设置断点。

通过打开公共 URL 导航到*bikesharingweb*服务。 选择 " *Aurelia meets Briggs （客户）* " 作为用户，然后选择要出租的自行车。 请注意，自行车的图像不会加载。 返回 Visual Studio Code 并观察第233行。 设置的断点已暂停第233行的服务。 若要恢复服务，请按 *F5*，或者依次单击“调试”、“继续”。 返回到你的浏览器并验证你是否看到了自行车的占位符图像。

将光标置于第233行 `server.js` 上，并命中*F9*，以删除断点。

### <a name="update-your-application"></a>更新应用程序

编辑 `server.js` 以删除行232和233：

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

保存更改，然后单击 "*调试*"，然后*重新启动调试*。 刷新您的浏览器，并验证您是否不再看到自行车的占位符图像。

依次单击“调试”、“停止调试”以停止调试器。 单击 "Azure Dev Spaces" 状态栏以断开与 AKS 群集的连接。

## <a name="additional-configuration"></a>其他配置

Azure Dev Spaces 无需任何其他配置即可处理路由流量和复制环境变量。 如果需要将装载到 AKS 群集中的容器的所有文件（例如 ConfigMap 文件）下载，可以创建 `azds-local.env` 将这些文件下载到开发计算机。

下面是一个示例 `azds-local.env`：

```
# This downloads the "whitelist" volume from the container,
# saves it to a temporary directory on your development machine,
# and sets the full path to an environment variable called WHITELIST_PATH.

WHITELIST_PATH=${volumes.whitelist}/whitelist

# This downloads a file from the container's 'default-token-<any>' mount directory 
# to /var/run/secrets/kubernetes.io/serviceaccount on your development machine.

KUBERNETES_IN_CLUSTER_CONFIG_OVERWRITE=${volumes.default-token-*|/var/run/secrets/kubernetes.io/serviceaccount}


# This makes the myapp1 service available to your development machine
# regardless of the AKS cluster you are connected to and
# sets the local IP to an environment variable called MYAPP1_SERVICE_HOST.

MYAPP1_SERVICE_HOST=${services.myapp1}

# If the myapp1 service is made available in this way, 
# you can also access it using "myapp1" and "myapp1.svc.cluster.local"
# in addition to the IP in the MYAPP1_SERVICE_HOST environment variable.
```

## <a name="using-logging-and-diagnostics"></a>使用日志记录和诊断

将开发计算机连接到 AKS 群集后，将日志记录输出写入 " *Dev Spaces 连接*" 窗口。

![输出](../media/how-to-connect/connect-output.png)

单击 "Azure Dev Spaces" 状态栏，然后选择 "*显示诊断信息*"。 此命令打印日志记录输出中的当前环境变量和 DNS 条目。

![诊断输出](../media/how-to-connect/connect-output-diagnostics.png)

此外，还可以在[开发计算机的*TEMP*目录][azds-tmp-dir]中的 `Azure Dev Spaces` 目录中查找诊断日志。

## <a name="next-steps"></a>后续步骤

了解如何在拉取请求合并到存储库的主分支之前，使用 Azure Dev Spaces 和 GitHub 操作直接在 AKS 中测试拉取请求中的更改。

> [!div class="nextstepaction"]
> [Azure Kubernetes 服务 & GitHub 操作][gh-actions]

[azds-tmp-dir]: ../troubleshooting.md#before-you-begin
[azds-vs-code]: https://marketplace.visualstudio.com/items?itemName=azuredevspaces.azds
[azure-cli]: /cli/azure/install-azure-cli?view=azure-cli-latest
[bike-sharing-github]: https://github.com/Azure/dev-spaces/tree/master/samples/BikeSharingApp
[gh-actions]: github-actions.md
[supported-regions]: https://azure.microsoft.com/global-infrastructure/services/?products=kubernetes-service
[team-quickstart]: ../quickstart-team-development.md
[vs-code]: https://code.visualstudio.com/download