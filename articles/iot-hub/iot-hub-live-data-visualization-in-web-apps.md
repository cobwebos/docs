---
title: Web 应用中 IoT 中心数据的实时数据可视化
description: 使用 Web 应用程序将从传感器收集的并发送到 Azure IoT 中心的温度和湿度数据可视化。
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.tgt_pltfrm: arduino
ms.date: 05/31/2019
ms.author: robinsh
ms.openlocfilehash: 5e27cf51d50b3094adca6ce8d3846ef358f78482
ms.sourcegitcommit: a8ee9717531050115916dfe427f84bd531a92341
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/12/2020
ms.locfileid: "83201535"
---
# <a name="visualize-real-time-sensor-data-from-your-azure-iot-hub-in-a-web-application"></a>在 Web 应用程序中可视化 Azure IoT 中心内的实时传感器数据

![端到端关系图](./media/iot-hub-live-data-visualization-in-web-apps/1_iot-hub-end-to-end-diagram.png)

[!INCLUDE [iot-hub-get-started-note](../../includes/iot-hub-get-started-note.md)]

## <a name="what-you-learn"></a>学习内容

本教程介绍如何使用本地计算机上运行的 node.js Web 应用将 IoT 中心收到的实时传感器数据可视化。 在本地运行该 Web 应用后，可以选择性地遵循相应的步骤在 Azure 应用服务中托管该 Web 应用。 若要尝试使用 Power BI 可视化 IoT 中心的数据，请参阅[使用 Power BI 可视化 Azure IoT 中心的实时传感器数据](iot-hub-live-data-visualization-in-power-bi.md)。

## <a name="what-you-do"></a>准备工作

* 将一个使用者组添加到 Web 应用程序用来读取传感器数据的 IoT 中心
* 从 GitHub 下载 Web 应用代码
* 检查 Web 应用代码
* 配置环境变量用于保存 Web 应用所需的 IoT 中心项目
* 在开发计算机上运行该 Web 应用
* 打开一个网页，以查看 IoT 中心内的实时温度和湿度数据
* （可选）使用 Azure CLI 在 Azure 应用服务中托管 Web 应用

## <a name="what-you-need"></a>需要什么

* 完成 [Raspberry Pi 联机模拟器](iot-hub-raspberry-pi-web-simulator-get-started.md)教程或其中一个设备教程；例如[将 Raspberry Pi 与 Node.js 配合使用](iot-hub-raspberry-pi-kit-node-get-started.md)。 这包括以下要求：

  * 一个有效的 Azure 订阅
  * 已在订阅中创建一个 IoT 中心
  * 一个可向 IoT 中心发送消息的客户端应用程序

* [下载 Git](https://www.git-scm.com/downloads)

* 本文中的步骤假设使用 Windows 开发计算机；但是，也可以在 Linux 系统上使用偏好的 shell 轻松执行这些步骤。

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

运行以下命令将用于 Azure CLI 的 Microsoft Azure IoT 扩展添加到 Cloud Shell 实例。 IoT 扩展会将 IoT 中心、IoT Edge 和 IoT 设备预配服务 (DPS) 特定的命令添加到 Azure CLI。

```azurecli-interactive
az extension add --name azure-iot
```

## <a name="add-a-consumer-group-to-your-iot-hub"></a>将使用者组添加到 IoT 中心

[使用者组](https://docs.microsoft.com/azure/event-hubs/event-hubs-features#event-consumers)提供事件流的独立视图，可让应用和 Azure 服务单独使用同一事件中心终结点内的数据。 在本部分，你要将一个使用者组添加到 IoT 中心的内置终结点，Web 应用将从该终结点读取数据。

运行以下命令，将使用者组添加到 IoT 中心的内置终结点：

```azurecli-interactive
az iot hub consumer-group create --hub-name YourIoTHubName --name YourConsumerGroupName
```

请记下所选的名称，因为本教程稍后需要用到。

## <a name="get-a-service-connection-string-for-your-iot-hub"></a>获取 IoT 中心的服务连接字符串

创建的 IoT 中心具有多个默认访问策略。 其中的一个策略是**服务**策略，该策略为某个服务提供足够的权限，使其能够读取和写入 IoT 中心的终结点。 运行以下命令获取符合服务策略的 IoT 中心的连接字符串：

```azurecli-interactive
az iot hub show-connection-string --hub-name YourIotHub --policy-name service
```

该连接字符串应如下所示：

```javascript
"HostName={YourIotHubName}.azure-devices.net;SharedAccessKeyName=service;SharedAccessKey={YourSharedAccessKey}"
```

请记下服务连接字符串，因为本教程稍后需要用到。

## <a name="download-the-web-app-from-github"></a>从 GitHub 下载 Web 应用

打开命令窗口，输入以下命令以从 GitHub 下载示例，然后切换到示例目录：

```cmd
git clone https://github.com/Azure-Samples/web-apps-node-iot-hub-data-visualization.git
cd web-apps-node-iot-hub-data-visualization
```

## <a name="examine-the-web-app-code"></a>检查 Web 应用代码

在 web-apps-node-iot-hub-data-visualization 目录中，使用偏好的编辑器打开 Web 应用。 下面显示了 VS Code 中显示的文件结构：

![Web 应用文件结构](./media/iot-hub-live-data-visualization-in-web-apps/web-app-files.png)

请花费片刻时间检查以下文件：

* **Server.js** 是用于初始化 Web 套接字和事件中心包装类的服务端脚本。 它提供对事件中心包装类的回调，以便将传入的消息广播到 Web 套接字。

* **Event-hub-reader.js** 是使用指定的连接字符串和使用者组连接到 IoT 中心内置终结点的服务端脚本。 它从传入消息中的元数据提取 DeviceId 和 EnqueuedTimeUtc，然后使用 server.js 注册的回调方法中继消息。

* **Chart-device-data.js** 是一个客户端脚本，可以侦听 Web 套接字，跟踪每个 DeviceId，并存储每个设备的最后 50 个传入数据点。 然后，它将选定的设备数据绑定到图表对象。

* **Index.html** 处理网页的 UI 布局，并引用客户端逻辑的所需脚本。

## <a name="configure-environment-variables-for-the-web-app"></a>配置 Web 应用的环境变量

若要从 IoT 中心读取数据，Web 应用需要获取 IoT 中心的连接字符串，以及从中读取数据的使用者组的名称。 它会从 server.js 的以下行中的进程环境获取这些字符串：

```javascript
const iotHubConnectionString = process.env.IotHubConnectionString;
const eventHubConsumerGroup = process.env.EventHubConsumerGroup;
```

在命令窗口中使用以下命令设置环境变量。 请将占位符值替换为 IoT 中心的服务连接字符串，以及前面创建的使用者组的名称。 不要使用引号括住字符串。

```cmd
set IotHubConnectionString=YourIoTHubConnectionString
set EventHubConsumerGroup=YourConsumerGroupName
```

## <a name="run-the-web-app"></a>运行 Web 应用

1. 确保设备正在运行，且正在发送数据。

2. 在命令窗口中，运行以下代码行以下载并安装引用的包，然后启动网站：

   ```cmd
   npm install
   npm start
   ```

3. 控制台中应会显示输出，指出 Web 应用已成功连接到 IoT 中心并在侦听端口 3000：

   ![Web 应用已在控制台上启动](./media/iot-hub-live-data-visualization-in-web-apps/web-app-console-start.png)

## <a name="open-a-web-page-to-see-data-from-your-iot-hub"></a>打开一个网页以查看 IoT 中心的数据

在浏览器中打开 `http://localhost:3000`。

在“选择设备”列表中选择你的设备，以查看设备发送到 IoT 中心的最后 50 个温度和湿度数据点的运行图。****

![显示实时温度和湿度的 Web 应用页](./media/iot-hub-live-data-visualization-in-web-apps/web-page-output.png)

此外，控制台中还应有输出显示 Web 应用正在广播到浏览器客户端的消息：  

![控制台上的 Web 应用广播输出](./media/iot-hub-live-data-visualization-in-web-apps/web-app-console-broadcast.png)

## <a name="host-the-web-app-in-app-service"></a>在应用服务中托管 Web 应用

[Azure 应用服务的 Web 应用功能](https://docs.microsoft.com/azure/app-service/overview)提供用于托管 Web 应用程序的平台即服务 (PAAS)。 托管在 Azure 应用服务中的 Web 应用程序可受益于强大的 Azure 功能（例如更高的安全性、负载均衡和可伸缩性）以及 Azure 和合作伙伴 DevOps 解决方案（例如持续部署、包管理等）。 Azure 应用服务支持以多种流行语言开发的、部署在 Windows 或 Linux 基础结构上的 Web 应用程序。

在本部分，你将在应用服务中预配一个 Web 应用，然后使用 Azure CLI 命令将代码部署到该应用。 可以在 [az webapp](https://docs.microsoft.com/cli/azure/webapp?view=azure-cli-latest) 文档中查找所用命令的详细信息。 在开始之前，请确保已完成[将资源组添加到 IoT 中心](#add-a-consumer-group-to-your-iot-hub)、[获取 IoT 中心的服务连接字符串](#get-a-service-connection-string-for-your-iot-hub)以及[从 GitHub下载 Web 应用](#download-the-web-app-from-github)的步骤。

1. [应用服务计划](https://docs.microsoft.com/azure/app-service/overview-hosting-plans)为应用服务中托管的应用定义一组计算资源，使其能够运行。 本教程使用开发人员/免费层来托管 Web 应用。 使用免费层时，你的 Web 应用将在与其他应用服务应用（包括其他客户的应用）共享的 Windows 资源上运行。 Azure 还提供应用服务计划用于在 Linux 计算资源上部署 Web 应用。 如果你已有可用的应用服务计划，则可以跳过此步骤。

   若要使用 Windows 免费层创建应用服务计划，请运行以下命令。 使用 IoT 中心所在的同一资源组。 服务计划名称可以包含大小写字母、数字和连字符。

   ```azurecli-interactive
   az appservice plan create --name <app service plan name> --resource-group <your resource group name> --sku FREE
   ```

2. 现在，在应用服务计划中预配一个 Web 应用。 使用 `--deployment-local-git` 参数可从本地计算机上的 Git 存储库上传和部署 Web 应用代码。 Web 应用名称必须全局唯一，可以包含大小写字母、数字和连字符。 请确保为 `--runtime` 参数指定 Node 版本 10.6 或更高版本，具体取决于所使用的 Node.js 运行时版本。 可以使用 `az webapp list-runtimes` 命令获取支持的运行时列表。

   ```azurecli-interactive
   az webapp create -n <your web app name> -g <your resource group name> -p <your app service plan name> --runtime "node|10.6" --deployment-local-git
   ```

3. 现在，为指定 IoT 中心连接字符串和事件中心使用者组的环境变量添加应用程序设置。 `-settings` 参数中的每项设置以空格分隔。 请使用 IoT 中心的服务连接字符串，以及前面在本教程中创建的使用者组。 不要将值括在引号中。

   ```azurecli-interactive
   az webapp config appsettings set -n <your web app name> -g <your resource group name> --settings EventHubConsumerGroup=<your consumer group> IotHubConnectionString="<your IoT hub connection string>"
   ```

4. 为 Web 应用启用 Web 套接字协议，并将 Web 应用设置为仅接收 HTTPS 请求（HTTP 请求将重定向到 HTTPS）。

   ```azurecli-interactive
   az webapp config set -n <your web app name> -g <your resource group name> --web-sockets-enabled true
   az webapp update -n <your web app name> -g <your resource group name> --https-only true
   ```

5. 若要将代码部署到应用服务，需使用[用户级部署凭据](https://docs.microsoft.com/azure/app-service/deploy-configure-credentials)。 用户级部署凭据不同于 Azure 凭据，用于在 Web 应用中实现 Git 本地部署和 FTP 部署。 设置后，可对 Azure 帐户中所有订阅内的所有应用服务应用使用这些凭据。 如果以前已经设置了用户级部署凭据，则可以使用这些凭据。

   如果以前尚未设置用户级部署凭据或不记得密码，请运行以下命令。 部署用户名必须在 Azure 中唯一，不得包含用于本地 Git 推送的“@”符号。 出现提示时，请输入并确认新密码。 密码必须至少为 8 个字符，且具有字母、数字和符号这三种元素中的两种。

   ```azurecli-interactive
   az webapp deployment user set --user-name <your deployment user name>
   ```

6. 获取用于将代码推送到应用服务的 Git URL。

   ```azurecli-interactive
   az webapp deployment source config-local-git -n <your web app name> -g <your resource group name>
   ```

7. 将远程库添加到引用应用服务中 Web 应用的 Git 存储库的克隆。 对于“\<Git 克隆 URL\>”，请使用上一步骤返回的 URL。 在命令窗口中运行以下命令。

   ```cmd
   git remote add webapp <Git clone URL>
   ```

8. 若要将代码部署到应用服务，请在命令窗口中输入以下命令。 系统提示输入凭据时，请输入在步骤 5 中创建的用户级部署凭据。 确保推送到应用服务远程库的主分支。

    ```cmd
    git push webapp master:master
    ```

9. 命令窗口中的部署进度将会更新。 如果部署成功，将返回类似于以下输出的行：

    ```cmd
    remote:
    remote: Finished successfully.
    remote: Running post deployment command(s)...
    remote: Deployment successful.
    To https://contoso-web-app-3.scm.azurewebsites.net/contoso-web-app-3.git
    6b132dd..7cbc994  master -> master
    ```

10. 运行以下命令，以查询 Web 应用的状态并确保它正在运行：

    ```azurecli-interactive
    az webapp show -n <your web app name> -g <your resource group name> --query state
    ```

11. 在浏览器中导航到 `https://<your web app name>.azurewebsites.net`。 此时会显示一个网页，它类似于在本地运行 Web 应用时所看到的网页。 假设你的设备正在运行且正在发送数据，则应会显示该设备最近发送的 50 个温度和湿度读数的运行图。

## <a name="troubleshooting"></a>疑难解答

如果在学习本示例期间遇到任何问题，请尝试执行以下部分所述的步骤。 如果问题仍然出现，请通过本主题末尾的链接向我们发送反馈。

### <a name="client-issues"></a>客户端问题

* 如果设备未显示在列表中，或者未绘制任何图形，请确保设备代码在设备上运行。

* 在浏览器中打开开发人员工具（在许多浏览器中，按 F12 键可以打开它），并找到控制台。 查看其中列显的任何警告或错误。

* 可以调试 /js/chat-device-data.js 中的客户端脚本。

### <a name="local-website-issues"></a>本地网站问题

* 观察启动 node 时所在的窗口中的控制输出。

* 调试服务器代码，具体而言，是 server.js 和 /scripts/event-hub-reader.js。

### <a name="azure-app-service-issues"></a>Azure 应用服务问题

* 在 Azure 门户中转到你的 Web 应用。 在左窗格中的“监视”下，选择“应用服务日志”。******** 启用“应用程序日志记录(文件系统)”，将“级别”设置为“错误”，并选择“保存”。************ 然后打开“日志流”（在“监视”下）。********

* 在 Azure 门户上的 Web 应用中，在“开发工具”下选择“控制台”，然后使用 `node -v` 和 `npm -v` 验证 node 和 npm 版本。********

* 如果看到了有关找不到包的错误，则可能表示步骤的运行顺序不当。 部署站点（使用 `git push`）时，应用服务将会根据当前配置的 node 版本运行 `npm install`。 如果以后在配置中更改此版本，则需要对代码进行无意义的更改，然后再次推送。

## <a name="next-steps"></a>后续步骤

现已成功使用 Web 应用可视化 IoT 中心的实时传感器数据。

若要从 Azure IoT 中心直观显示数据，请参阅[使用 Power BI 直观显示 IoT 中心的实时传感器数据](iot-hub-live-data-visualization-in-power-bi.md)。

[!INCLUDE [iot-hub-get-started-next-steps](../../includes/iot-hub-get-started-next-steps.md)]
