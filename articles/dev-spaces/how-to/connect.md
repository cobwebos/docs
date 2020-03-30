---
title: 将开发计算机连接到 AKS 群集（预览版）
services: azure-dev-spaces
ms.date: 11/04/2019
ms.topic: conceptual
description: 了解如何将开发计算机连接到具有 Azure 开发空间的 AKS 群集
keywords: Azure 开发空间、开发空间、Docker、库伯奈斯、Azure、AKS、Azure 库伯奈斯服务、容器
ms.openlocfilehash: 772f221a8047a71902f36fa98ded6c24b5e02d27
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "80235002"
---
# <a name="connect-your-development-computer-to-an-aks-cluster-preview"></a>将开发计算机连接到 AKS 群集（预览版）

Azure 开发人员空间允许您在开发计算机上运行和调试具有或不带容器的代码，同时仍与应用程序或服务的其余部分连接到 Kubernetes 群集。 将开发计算机连接到群集可帮助您快速开发应用程序并执行端到端测试，而无需创建任何 Docker 或 Kubernetes 配置。 您还可以连接到 AKS 群集，而不会影响可能使用相同的群集的其他工作负载或用户。

Azure 开发空间重定向连接的 AKS 群集和开发计算机之间的流量。 此流量重定向允许开发计算机上的代码和在 AKS 群集中运行的服务进行通信，就像它们位于同一 AKS 群集中一样。 由于代码在开发计算机上运行，因此在用于运行和调试该代码的开发工具中，您也有灵活性。 Azure 开发人员空间还提供一种复制开发计算机中 AKS 群集中可用于 pod 的环境变量和装载的文件的方法。

本指南介绍如何：

* 在 Azure 中的托管 Kubernetes 群集上设置 Azure Dev Spaces。
* 将包含多个微服务的大型应用程序部署到开发空间。
* 使用 Azure 开发空间在 AKS 群集和开发计算机上运行的代码之间重定向流量。

> [!IMPORTANT]
> 此功能目前处于预览状态。 需同意[补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)才可使用预览版。 在正式版 (GA) 推出之前，此功能的某些方面可能会有所更改。

## <a name="before-you-begin"></a>开始之前

本指南使用[Azure 开发空间自行车共享示例应用程序](https://github.com/Azure/dev-spaces/tree/master/samples/BikeSharingApp)来演示将开发计算机连接到 AKS 群集。 按照[Azure 开发人员空间自行车共享示例应用程序 README](https://github.com/Azure/dev-spaces/blob/master/samples/BikeSharingApp/README.md)中的说明运行示例应用程序。 或者，如果您在 AKS 群集上拥有自己的应用程序，您仍然可以按照以下步骤操作并使用您自己的服务和 pod 的名称。

### <a name="limitations"></a>限制

* 目前不支持 UDP。

### <a name="prerequisites"></a>先决条件

* Azure 订阅。 如果没有 Azure 订阅，可以创建一个[免费帐户](https://azure.microsoft.com/free)。
* [已安装 Azure CLI][azure-cli]。
* 在 MacOS 或 Windows 10 上安装并运行[Azure 开发空间][azds-vs-code]扩展的[可视化工作室代码][vs-code]。
* [Azure 开发空间自行车共享示例应用程序](https://github.com/Azure/dev-spaces/tree/master/samples/BikeSharingApp)或您自己的应用程序在 AKS 群集上运行。

## <a name="connect-your-development-computer"></a>连接开发计算机

在可视化工作室代码中打开*开发空间/示例/自行车共享应用/自行车*，并使用 Azure 开发空间扩展将开发计算机连接到 AKS 群集。

要使用 Azure 开发人员空间扩展，请单击"*查看*然后*命令调色板*"，在可视化工作室代码中打开命令调色板。 开始键入`Azure Dev Spaces: Redirect`并单击`Azure Dev Spaces: Redirect an existing Kubernetes service to my machine [Preview]`。，`Azure Dev Spaces: Redirect an existing Kubernetes pod to my machine [Preview]`或`Azure Dev Spaces: Redirect a new Kubernetes pod to my machine [Preview]`。

![命令](../media/how-to-connect/connect-commands.png)

### <a name="select-a-redirection-option"></a>选择重定向选项

如果运行`Azure Dev Spaces: Redirect an existing Kubernetes service to my machine [Preview]`，系统将要求您选择现有的 Kubernetes 服务：

![选择服务](../media/how-to-connect/connect-choose-service.png)

此选项将此服务的 AKS 群集中的所有流量重定向到开发计算机上运行的应用程序的版本。 如果此服务在 AKS 群集中运行多个 pod，则此服务的所有流量将仅路由到开发计算机。 Azure 开发空间还会将应用程序的所有出站流量路由回 AKS 群集。

如果运行`Azure Dev Spaces: Redirect an existing Kubernetes pod to my machine [Preview]`，系统将要求您选择特定的窗格：

![选择 Pod](../media/how-to-connect/connect-choose-pod.png)

此选项连接到特定窗格。 此选项可用于与不发送或接收流量的 pod 进行交互以及复制已终止的 pod。 如果 Pod 确实发送和接收流量，则此选项以与 AKS 群集`Azure Dev Spaces: Redirect an existing Kubernetes service to my machine [Preview]`中的所有流量相同的方式重定向与所选窗格的服务相关的所有 pod。

如果运行`Azure Dev Spaces: Redirect a new Kubernetes pod to my machine [Preview]`，系统不会提示您选择现有的 Pod 或服务。 此选项将所有出站流量从开发计算机上运行的应用程序重定向到 AKS 群集。

在此示例中，选择`Azure Dev Spaces: Redirect an existing Kubernetes service to my machine [Preview]`并选择*自行车*服务。

### <a name="select-a-connection-mode"></a>选择连接模式

选择重定向选项后，系统将提示您选择 *"替换*"或 *"克隆"* 连接模式。

![替换或克隆](../media/how-to-connect/connect-replace-clone.png)

*"替换*"选项将替换 AKS 群集中的当前 pod 或服务，并将该服务的所有流量重定向到开发计算机。 此选项可能会破坏 AKS 群集中与您重定向的服务交互的其他服务，在开发计算机上启动应用程序之前，这些服务可能无法正常工作。 *"克隆"* 选项允许您选择现有的子开发空间或创建新的子开发空间，以便将 Pod 或服务的流量重定向到开发计算机。 此选项允许您单独工作，并且不会中断其他服务，因为只有到该子开发空间的流量才会重定向到开发计算机。 "*克隆"* 选项要求 AKS 群集启用 Azure 开发空间。

在此示例中，选择 *"替换*"。

> [!NOTE]
> 如果现有服务的 pod 有多个容器，系统也会提示您选择应用程序的容器。

### <a name="select-a-port-for-your-application"></a>为应用程序选择端口

选择连接模式后，系统将提示您输入本地应用程序的 TCP 端口。 如果应用程序打开多个端口，请用逗号（例如*80，81）* 分隔它们。 如果应用程序不接受任何网络请求，请输入*0*。 在此示例中，输入*3000*。

![连接选择端口](../media/how-to-connect/connect-choose-port.png)

### <a name="confirm-you-are-connected"></a>确认您已连接

选择应用程序的 TCP 端口后，Azure 开发空间将建立到 AKS 群集的连接。 Azure 开发空间将代理注入 AKS 群集，以重定向 AKS 群集和开发计算机之间的流量。 建立此连接可能需要几分钟时间。 Azure 开发空间还将请求管理员访问权限，以便修改开发计算机上的*主机*文件。

> [!IMPORTANT]
> Azure 开发空间建立到 AKS 群集的连接后，如果选择 *"替换*连接模式"，则 AKS 群集中的其他服务可能无法正常运行，直到您在开发计算机中启动服务。 您可以选择*克隆*连接模式，以创建用于重定向的子开发空间，并避免对父空间造成任何中断。 此外，如果服务具有开发计算机上不可用的依赖项，则可能需要修改应用程序或提供[其他配置](#additional-configuration)

Azure 开发空间在建立与 AKS 群集的连接后，将打开名为 *"AZDS 连接 - 自行车"* 的终端窗口。 此终端窗口具有从 AKS 群集配置的所有环境变量和 DNS 条目。 在此终端窗口中运行或使用 Visual Studio 代码调试器的任何代码都连接到 AKS 群集。

![终端](../media/how-to-connect/connect-terminal.png)

此外，Azure 开发空间会创建一个名为 *"开发空间连接*其所有输出"的窗口。

![输出](../media/how-to-connect/connect-output.png)

Azure 开发空间还具有显示连接状态的状态栏项。

![状态](../media/how-to-connect/connect-status.png)

验证状态栏显示*开发人员空间：连接到本地端口 3000 上的 dev/自行车*。

### <a name="configure-your-application-on-your-development-computer"></a>在开发计算机上配置应用程序

打开*AZDS 连接 - 自行车*终端`npm install`窗口并运行 ：

```console
$ npm install

> fsevents@1.2.9 install ./dev-spaces/samples/BikeSharingApp/Bikes/node_modules/fsevents
> node install
...
```

单击 *"调试"* 然后*打开配置*。 如果提示选择环境，请选择*Node.js*。这将创建一`.vscode/launch.json`个文件。 将该文件的内容替换为以下内容：

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

打开[包.json](https://github.com/Azure/dev-spaces/blob/master/samples/BikeSharingApp/Bikes/package.json)并添加调试脚本：

```json
  "devDependencies": {
    "nodemon": "^1.18.10"
  },
  "scripts": {
    "debug": "node --nolazy --inspect-brk=9229 server.js"
  }
```

### <a name="start-your-application-on-your-development-computer"></a>在开发计算机上启动应用程序

单击左侧的*调试*图标，然后单击顶部的 *"通过 NPM 通过启动"旁边的启动*按钮。

![通过 NPM 启动](../media/how-to-connect/launch-npm.png)

应用程序将启动，Azure 开发人员空间将重定向 AKS 群集和开发计算机之间的流量。 您将在*调试控制台*中看到类似于以下内容的消息：

```console
/usr/local/bin/npm run-script debug 
...
Debugger attached.
Collection: bikes
MongoDB connection string: mongodb://databases-mongo
Connected to MongoDB
Listening on port 3000
```

通过单击 Azure 开发人员空间状态栏并选择应用程序的公共 URL 导航到*自行车共享 Web*服务。 您还可以从之前运行的命令`azds list-uris`中找到公共 URL。 如果在群集上不使用 Azure 开发空间，请使用正在使用的命名空间的应用程序的 IP 或 URL。 在以上示例中，*bikesharingweb* 服务的公共 URL 为 `http://dev.bikesharingweb.fedcab0987.eus.azds.io/`。 选择*奥雷利亚布里格斯（客户）* 作为用户，然后选择自行车出租。

### <a name="set-a-break-point"></a>设置断点

打开[服务器.js，](https://github.com/Azure/dev-spaces/blob/master/samples/BikeSharingApp/Bikes/server.js#L233)然后单击第233行的某处，将光标放在那里。 通过点击*F9*或单击 *"调试*"然后*切换断点来设置断点*。

通过打开公共 URL 导航到*自行车共享 Web*服务。 选择*奥雷利亚布里格斯（客户）* 作为用户，然后选择自行车出租。 请注意，自行车的图像未加载。 返回到可视化工作室代码并突出显示观察行 233。 您设置的断点已暂停第 233 行的服务。 若要恢复服务，请按 *F5*，或者依次单击“调试”、“继续”。**** 返回到您的浏览器并验证您看到自行车的占位符图像。

通过将光标放在第 233 行中`server.js`并击到*F9*来移除断点。

### <a name="update-your-application"></a>更新应用程序

编辑`server.js`以删除行 232 和 233：

```javascript
    // Hard code image url *FIX ME*
    theBike.imageUrl = "/static/logo.svg";
```

该部分现在应如下所示：

```javascript
    var theBike = result;
    theBike.id = theBike._id;
    delete theBike._id;
```

保存更改，然后单击 *"调试"* 然后*重新启动调试*。 刷新浏览器并验证您是否不再看到自行车的占位符图像。

依次单击“调试”、“停止调试”以停止调试器。**** 单击 Azure 开发空间状态栏以断开与 AKS 群集的连接。

## <a name="additional-configuration"></a>其他配置

Azure 开发人员空间可以处理路由流量和复制环境变量，而无需任何其他配置。 如果需要下载安装在 AKS 群集中容器的任何文件（如 ConfigMap 文件），则可以创建 将`azds-local.env`这些文件下载到开发计算机。

下面是一个示例`azds-local.env`：

```
# This downloads the "whitelist" volume from the container,
# saves it to a temporary directory on your development computer,
# and sets the full path to an environment variable called WHITELIST_PATH.

WHITELIST_PATH=${volumes.whitelist}/whitelist

# This downloads a file from the container's 'default-token-<any>' mount directory 
# to /var/run/secrets/kubernetes.io/serviceaccount on your development computer.

KUBERNETES_IN_CLUSTER_CONFIG_OVERWRITE=${volumes.default-token-*|/var/run/secrets/kubernetes.io/serviceaccount}


# This makes the myapp1 service available to your development computer
# regardless of the AKS cluster you are connected to and
# sets the local IP to an environment variable called MYAPP1_SERVICE_HOST.

# If the myapp1 service is made available in this way, 
# you can also access it using "myapp1" and "myapp1.svc.cluster.local"
# in addition to the IP in the MYAPP1_SERVICE_HOST environment variable.

MYAPP1_SERVICE_HOST=${services.myapp1}

# This makes the service myapp2 in namespace mynamespace available to your 
# development computer regardless of the AKS cluster you are connected to and
# sets the local IP to an environment variable called MYAPP2_SERVICE_HOST.

MYAPP2_SERVICE_HOST=${services.mynamespace.myapp2}
```

## <a name="using-logging-and-diagnostics"></a>使用日志记录和诊断

将开发计算机连接到 AKS 群集后，日志记录输出将写入*开发空间连接*窗口。

![输出](../media/how-to-connect/connect-output.png)

单击 Azure 开发空间状态栏并选择 *"显示诊断信息*"。 此命令在日志记录输出中打印当前环境变量和 DNS 整个。

![带诊断的输出](../media/how-to-connect/connect-output-diagnostics.png)

此外，您还可以在`Azure Dev Spaces`[开发计算机的*TEMP*目录中][azds-tmp-dir]找到目录中的诊断日志。

## <a name="next-steps"></a>后续步骤

了解如何在拉取请求合并到存储库的主分支之前，使用 Azure 开发空间和 GitHub 操作直接在 AKS 中测试从拉取请求中的更改。

> [!div class="nextstepaction"]
> [GitHub 操作& Azure 库伯奈斯服务][gh-actions]

[azds-tmp-dir]: ../troubleshooting.md#before-you-begin
[azds-vs-code]: https://marketplace.visualstudio.com/items?itemName=azuredevspaces.azds
[azure-cli]: /cli/azure/install-azure-cli?view=azure-cli-latest
[bike-sharing-github]: https://github.com/Azure/dev-spaces/tree/master/samples/BikeSharingApp
[gh-actions]: github-actions.md
[supported-regions]: https://azure.microsoft.com/global-infrastructure/services/?products=kubernetes-service
[team-quickstart]: ../quickstart-team-development.md
[vs-code]: https://code.visualstudio.com/download