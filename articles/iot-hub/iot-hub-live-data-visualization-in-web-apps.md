---
title: 实时可视化 Azure IoT 中心的 web 应用中的传感器数据 |Microsoft Docs
description: 使用 web 应用程序来可视化从传感器收集并发送到 Iot 中心的温度和湿度数据。
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.tgt_pltfrm: arduino
ms.date: 05/31/2019
ms.author: robinsh
ms.openlocfilehash: 22b15a95e529d4f09560e9b7e59d9f78f70651bc
ms.sourcegitcommit: cababb51721f6ab6b61dda6d18345514f074fb2e
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/04/2019
ms.locfileid: "66476005"
---
# <a name="visualize-real-time-sensor-data-from-your-azure-iot-hub-in-a-web-application"></a>可视化 Azure IoT 中心中的 web 应用程序的实时传感器数据

![端到端关系图](./media/iot-hub-live-data-visualization-in-web-apps/1_iot-hub-end-to-end-diagram.png)

[!INCLUDE [iot-hub-get-started-note](../../includes/iot-hub-get-started-note.md)]

## <a name="what-you-learn"></a>学习内容

在本教程中，您将了解如何可视化 IoT 中心接收与在本地计算机上运行的 node.js web 应用的实时传感器数据。 本地运行 web 应用之后, 可以根据需要按照托管在 Azure 应用服务 web 应用程序的步骤。 若要尝试使用 Power BI 可视化 IoT 中心的数据，请参阅[使用 Power BI 可视化 Azure IoT 中心的实时传感器数据](iot-hub-live-data-visualization-in-power-bi.md)。

## <a name="what-you-do"></a>准备工作

* 将使用者组添加到 IoT 中心的 web 应用程序将用来读取传感器数据
* 从 GitHub 下载 web 应用程序代码
* 检查 web 应用程序代码
* 配置环境变量来保存 web 应用程序所需的项目的 IoT 中心
* 在开发计算机上运行 web 应用
* 打开 web 页面，查看从 IoT 中心的实时温度和湿度数据
* （可选）使用 Azure CLI 来托管在 Azure 应用服务 web 应用

## <a name="what-you-need"></a>所需条件

* 完成 [Raspberry Pi 联机模拟器](iot-hub-raspberry-pi-web-simulator-get-started.md)教程或其中一个设备教程；例如[将 Raspberry Pi 与 Node.js 配合使用](iot-hub-raspberry-pi-kit-node-get-started.md)。 这包括以下要求：

  * 一个有效的 Azure 订阅
  * 已在订阅中创建一个 IoT 中心
  * 一个可向 IoT 中心发送消息的客户端应用程序

* [下载 Git](https://www.git-scm.com/downloads)

* 在本文中的步骤假定在 Windows 开发计算机;但是，您可以轻松地执行这些步骤在 Linux 系统上你偏好的 shell 中。

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

运行以下命令将用于 Azure CLI 的 Microsoft Azure IoT 扩展添加到 Cloud Shell 实例。 IOT 扩展会将 IoT 中心、IoT Edge 和 IoT 设备预配服务 (DPS) 特定的命令添加到 Azure CLI。

```azurecli-interactive
az extension add --name azure-cli-iot-ext
```

## <a name="add-a-consumer-group-to-your-iot-hub"></a>将使用者组添加到 IoT 中心

[使用者组](https://docs.microsoft.com/azure/event-hubs/event-hubs-features#event-consumers)到事件流，使应用和 Azure 服务单独使用同一个事件中心终结点中的数据提供独立的视图。 在本部分中，添加到 web 应用程序将用于从中读取数据的 IoT 中心的内置终结点使用者组。

运行以下命令将使用者组添加到 IoT 中心内置终结点：

```azurecli-interactive
az iot hub consumer-group create --hub-name YourIoTHubName --name YourConsumerGroupName
```

请注意下你选择的名称，你将需要它在此教程的后面。

## <a name="get-a-service-connection-string-for-your-iot-hub"></a>IoT 中心获得服务的连接字符串

使用多个默认的访问策略创建 IoT 中心。 一个此类策略是**服务**策略，它提供了足够的权限来读取和写入 IoT 中心终结点的服务。 运行以下命令以获取符合服务策略的 IoT 中心连接字符串：

```azurecli-interactive
az iot hub show-connection-string --hub-name YourIotHub --policy-name service
```

连接字符串应类似于下面：

```javascript
"HostName={YourIotHubName}.azure-devices.net;SharedAccessKeyName=service;SharedAccessKey={YourSharedAccessKey}"
```

记下的服务连接字符串，你将稍后在本教程中需要它。

## <a name="download-the-web-app-from-github"></a>从 GitHub 下载 web 应用

打开命令窗口中，并输入以下命令从 GitHub 下载示例并将更改为示例目录：

```cmd
git clone https://github.com/Azure-Samples/web-apps-node-iot-hub-data-visualization.git
cd web-apps-node-iot-hub-data-visualization
```

## <a name="examine-the-web-app-code"></a>检查 web 应用程序代码

Web-apps-node-iot-hub-data-visualization 目录中，从你偏好的编辑器中打开 web 应用。 下面显示了在 VS Code 中查看的文件结构：

![Web 应用文件结构](./media/iot-hub-live-data-visualization-in-web-apps/web-app-files.png)

请花费片刻时间来检查以下文件：

* **Server.js**初始化 web 套接字和事件中心包装器类的服务端脚本。 它提供了到事件中心包装器类，该类使用广播 web 套接字的传入消息的回调。

* **事件中心 reader.js**是连接到 IoT 中心内置终结点，使用指定的连接字符串和使用者组的服务端脚本。 它从传入消息中的元数据中提取的 DeviceId 和 EnqueuedTimeUtc，然后将中继使用 server.js 所注册的回调方法的消息。

* **图表设备 data.js** web 套接字上侦听，跟踪的每个 DeviceId，并将存储为每个设备的传入数据的最后一个 50 点的客户端脚本。 然后，它将所选的设备数据绑定到图表对象。

* **Index.html**处理 web 页面的 UI 布局和引用客户端逻辑为所需的脚本。

## <a name="configure-environment-variables-for-the-web-app"></a>配置 web 应用的环境变量

若要从 IoT 中心读取数据，web 应用需要 IoT 中心的连接字符串和应该会显示通过使用者组的名称。 它会从进程环境在 server.js 中的以下行中获取这些字符串：

```javascript
const iotHubConnectionString = process.env.IotHubConnectionString;
const eventHubConsumerGroup = process.env.EventHubConsumerGroup;
```

使用以下命令在命令窗口中设置环境变量。 将占位符值替换为 IoT 中心和你之前创建的使用者组的名称的服务连接字符串。 不字符串用引号引起。

```cmd
set IotHubConnectionString=YourIoTHubConnectionString
set EventHubConsumerGroup=YourConsumerGroupName
```

## <a name="run-the-web-app"></a>运行 Web 应用

1. 请确保你的设备是运行和发送数据。

2. 在命令窗口中，运行以下行，以下载和安装引用的包并启动网站：

   ```cmd
   npm install
   npm start
   ```

3. 应看到在控制台中，该值指示 web 应用已成功连接到 IoT 中心并侦听端口 3000 的输出：

   ![在控制台上启动 web 应用](./media/iot-hub-live-data-visualization-in-web-apps/web-app-console-start.png)

## <a name="open-a-web-page-to-see-data-from-your-iot-hub"></a>打开 web 页面以查看 IoT 中心的数据

浏览器中打开`http://localhost:3000`。

在中**选择一个设备**列表中，选择你的设备，若要查看正在运行的最后一个 50 设备发送到 IoT 中心的温度和湿度数据点。

![显示实时温度和湿度的 Web 应用页](./media/iot-hub-live-data-visualization-in-web-apps/web-page-output.png)

您还会看到你的 web 应用广播消息显示到浏览器客户端控制台输出：  

![在控制台上的 web 应用程序广播的输出](./media/iot-hub-live-data-visualization-in-web-apps/web-app-console-broadcast.png)

## <a name="host-the-web-app-in-app-service"></a>主机应用服务中的 web 应用

[的 Azure 应用服务 Web 应用功能](https://docs.microsoft.com/azure/app-service/overview)为托管 web 应用程序提供平台即服务 (PAAS)。 在 Azure 应用服务中托管的 web 应用程序可以受益于功能强大的 Azure 功能，例如提高安全性，负载平衡，和可伸缩性以及 Azure 和合作伙伴 DevOps 解决方案，如持续部署、 包管理等。 Azure 应用服务支持在多种流行的语言中开发和部署 Windows 或 Linux 基础结构上 web 应用程序。

在本部分中，将预配应用服务中的 web 应用和使用 Azure CLI 命令将代码部署到它。 您可以找到详细信息中使用的命令[az webapp](https://docs.microsoft.com/cli/azure/webapp?view=azure-cli-latest)文档。 在开始之前，请确保已完成的步骤[将资源组添加到 IoT 中心](#add-a-consumer-group-to-your-iot-hub)， [IoT 中心获得服务的连接字符串](#get-a-service-connection-string-for-your-iot-hub)，和[从 GitHub下载web应用](#download-the-web-app-from-github).

1. [应用服务计划](https://docs.microsoft.com/azure/app-service/overview-hosting-plans)定义一组运行的应用服务中托管的应用的计算资源。 在本教程中，我们可以使用开发人员/免费层来托管 web 应用。 使用免费层，在与其他应用服务应用程序，包括应用程序的其他客户共享 Windows 资源上运行你的 web 应用。 Azure 还提供应用服务计划将在 Linux 上的 web 应用部署计算资源。 如果已有想要使用的应用服务计划，可以跳过此步骤。

   若要创建使用 Windows 的免费层的应用服务计划，请运行以下命令。 使用 IoT 中心所在的同一资源组。 你的服务计划名称可以包含大写和小写字母、 数字和连字符。

   ```azurecli-interactive
   az appservice plan create --name <app service plan name> --resource-group <your resource group name> --sku FREE
   ```

2. 现在你的应用服务计划中设置的 web 应用。 `--deployment-local-git`参数使 web 应用程序代码可以将上传并从本地计算机上的 Git 存储库部署。 Web 应用名称必须全局唯一，并且可以包含大写和小写字母、 数字和连字符。

   ```azurecli-interactive
   az webapp create -n <your web app name> -g <your resource group name> -p <your app service plan name> --deployment-local-git
   ```

3. 现在添加环境变量指定的 IoT 中心连接字符串和事件中心使用者组的应用程序设置。 各项设置是以空格分隔中`-settings`参数。 使用 IoT 中心和使用者组之前在本教程中创建的服务连接字符串。 不引用值。

   ```azurecli-interactive
   az webapp config appsettings set -n <your web app name> -g <your resource group name> --settings EventHubConsumerGroup=<your consumer group> IotHubConnectionString=<your IoT hub connection string>
   ```

4. 启用 Web 套接字协议的 web 应用和 web 应用以接收 HTTPS 设置仅请求 （HTTP 请求将重定向到 HTTPS）。

   ```azurecli-interactive
   az webapp config set -n <your web app name> -g <your resource group name> --web-sockets-enabled true
   az webapp update -n <your web app name> -g <your resource group name> --https-only true
   ```

5. 若要将代码部署到应用服务，将使用你[用户级部署凭据](https://docs.microsoft.com/azure/app-service/deploy-configure-credentials)。 用户级部署凭据不同于你的 Azure 凭据和用于本地 Git 和 FTP 部署到 web 应用。 完成设置后，它们是有效的所有 Azure 帐户中的所有订阅中在应用服务应用中。 如果以前已设置用户级部署凭据，可以使用它们。

   如果以前尚未设置用户级部署凭据或不记得密码，运行以下命令。 您部署的用户名称必须是 Azure 中唯一，并且它不能包含 @ 符号将本地 Git 推送。 当系统提示输入并确认新密码。 密码必须至少为 8 个字符，且具有字母、数字和符号这三种元素中的两种。

   ```azurecli-interactive
   az webapp deployment user set --user-name <your deployment user name>
   ```

6. 获取要使用推送到应用服务代码的 Git URL。

   ```azurecli-interactive
   az webapp deployment source config-local-git -n <your web app name> -g <your resource group name>
   ```

7. 将远程添加到引用应用服务中 web 应用的 Git 存储库克隆。 有关\<Git 克隆 URL\>，使用上一步中返回的 URL。 在命令窗口中运行以下命令。

   ```cmd
   git remote add webapp <Git clone URL>
   ```

8. 将代码部署到应用服务，请在命令窗口中输入以下命令。 如果系统提示您输入凭据，输入在步骤 5 中创建的用户级部署凭据。 请确保你推送到应用服务远程主分支。

    ```cmd
    git push webapp master:master
    ```

9. 在命令窗口中将更新部署的进度。 成功的部署将结尾类似于以下输出行：

    ```cmd
    remote:
    remote: Finished successfully.
    remote: Running post deployment command(s)...
    remote: Deployment successful.
    To https://contoso-web-app-3.scm.azurewebsites.net/contoso-web-app-3.git
    6b132dd..7cbc994  master -> master
    ```

10. 运行以下命令来查询你的 web 应用的状态并确保它正在运行：

    ```azurecli-interactive
    az webapp show -n <your web app name> -g <your resource group name> --query state
    ```

11. 在浏览器中导航至 `https://<your web app name>.azurewebsites.net` 。 已经看到了类似于一个你的 web 页面，当 web 应用在本地运行显示。 假设你的设备是运行和发送数据，应会看到正在运行的设备发送的 50 最新温度和湿度读数绘图。

## <a name="troubleshooting"></a>故障排除

如果您遇到任何问题与本示例，请尝试以下各节中的步骤。 如果问题仍然存在，请向我们发送反馈在本主题的底部。

### <a name="client-issues"></a>客户端问题

* 如果设备未出现在列表中，或者所绘制任何图形，请确保设备上运行设备代码。

* 在浏览器中，打开开发人员工具 （在许多浏览器按 F12 键将打开它），并找到控制台。 查找任何警告或错误那里打印。

* 您可以调试在 /js/chat-device-data.js 中的客户端脚本。

### <a name="local-website-issues"></a>本地网站问题

* 监视在其中启动节点的控制台输出窗口中输出。

* 调试服务器代码，尤其是 server.js 和 /scripts/event-hub-reader.js。

### <a name="azure-app-service-issues"></a>Azure 应用服务问题

* 在 Azure 门户中，转到你的 web 应用。 下**监视**在左窗格中，选择**应用服务日志**。 打开**应用程序日志记录 （文件系统）** 上，设置**级别**到错误，然后选择**保存**。 然后打开**日志流**(下**监视**)。

* 从 Azure 门户中的 web 应用下**开发工具**选择**控制台**并验证与 node 和 npm 版本`node -v`和`npm -v`。

* 如果看到关于未找到包的错误，您可能已运行顺序的步骤。 当部署该网站 (与`git push`) 运行该应用程序服务`npm install`，已配置基于当前版本的节点上运行。 如果该更改配置更高版本中，将需要对代码进行无意义的更改并再次推送。

## <a name="next-steps"></a>后续步骤

现已成功使用 Web 应用可视化 IoT 中心的实时传感器数据。

另一种方法可视化 Azure IoT 中心的数据，请参阅[使用 Power BI 可视化 IoT 中心的实时传感器数据](iot-hub-live-data-visualization-in-power-bi.md)。

[!INCLUDE [iot-hub-get-started-next-steps](../../includes/iot-hub-get-started-next-steps.md)]
