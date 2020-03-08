---
title: Web 应用中 IoT 中心数据的实时数据可视化
description: 使用 web 应用程序来可视化从传感器收集的温度和湿度数据，并将其发送到 Iot 中心。
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.tgt_pltfrm: arduino
ms.date: 05/31/2019
ms.author: robinsh
ms.openlocfilehash: 138e077f7b47fa9f38a4710db95eb7208cef78e3
ms.sourcegitcommit: bc792d0525d83f00d2329bea054ac45b2495315d
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/06/2020
ms.locfileid: "78675323"
---
# <a name="visualize-real-time-sensor-data-from-your-azure-iot-hub-in-a-web-application"></a>在 web 应用程序中可视化 Azure IoT 中心的实时传感器数据

![端到端关系图](./media/iot-hub-live-data-visualization-in-web-apps/1_iot-hub-end-to-end-diagram.png)

[!INCLUDE [iot-hub-get-started-note](../../includes/iot-hub-get-started-note.md)]

## <a name="what-you-learn"></a>学习内容

在本教程中，将了解如何使用在本地计算机上运行的 node.js web 应用来可视化 IoT 中心接收的实时传感器数据。 在本地运行 web 应用后，你可以根据需要执行以下步骤，在 Azure App Service 中托管 web 应用。 若要尝试使用 Power BI 可视化 IoT 中心的数据，请参阅[使用 Power BI 可视化 Azure IoT 中心的实时传感器数据](iot-hub-live-data-visualization-in-power-bi.md)。

## <a name="what-you-do"></a>准备工作

* 将使用者组添加到 IoT 中心，供 web 应用程序用来读取传感器数据
* 从 GitHub 下载 web 应用代码
* 检查 web 应用代码
* 配置环境变量以保存 web 应用所需的 IoT 中心项目
* 在开发计算机上运行 web 应用
* 打开网页，查看 IoT 中心的实时温度和湿度数据
* 可有可无使用 Azure CLI 在 Azure App Service 中托管 web 应用

## <a name="what-you-need"></a>所需条件

* 完成[Raspberry Pi 联机模拟器](iot-hub-raspberry-pi-web-simulator-get-started.md)教程或设备教程之一;例如，[包含 node.js 的 Raspberry Pi](iot-hub-raspberry-pi-kit-node-get-started.md)。 它们涵盖以下要求：

  * 一个有效的 Azure 订阅
  * 已在订阅中创建一个 IoT 中心
  * 一个可向 IoT 中心发送消息的客户端应用程序

* [下载 Git](https://www.git-scm.com/downloads)

* 本文中的步骤假定为 Windows 开发计算机;但是，你可以在首选 shell 中轻松地在 Linux 系统上执行这些步骤。

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

运行以下命令将用于 Azure CLI 的 Microsoft Azure IoT 扩展添加到 Cloud Shell 实例。 IOT 扩展会将 IoT 中心、IoT Edge 和 IoT 设备预配服务 (DPS) 特定的命令添加到 Azure CLI。

```azurecli-interactive
az extension add --name azure-iot
```

## <a name="add-a-consumer-group-to-your-iot-hub"></a>将使用者组添加到 IoT 中心

[使用者组](https://docs.microsoft.com/azure/event-hubs/event-hubs-features#event-consumers)提供独立的事件流视图，使应用和 Azure 服务能够独立地使用来自同一事件中心终结点的数据。 在本部分中，会将使用者组添加到 IoT 中心的内置终结点，web 应用将使用此终结点从中读取数据。

运行以下命令，将使用者组添加到 IoT 中心的内置终结点：

```azurecli-interactive
az iot hub consumer-group create --hub-name YourIoTHubName --name YourConsumerGroupName
```

记下所选的名称，本教程后面将需要用到它。

## <a name="get-a-service-connection-string-for-your-iot-hub"></a>获取 IoT 中心的服务连接字符串

使用几个默认访问策略创建 IoT 中心。 这种策略是**服务**策略，为服务提供足够的权限来读取和写入 IoT 中心的终结点。 运行以下命令，获取符合服务策略的 IoT 中心的连接字符串：

```azurecli-interactive
az iot hub show-connection-string --hub-name YourIotHub --policy-name service
```

连接字符串应如下所示：

```javascript
"HostName={YourIotHubName}.azure-devices.net;SharedAccessKeyName=service;SharedAccessKey={YourSharedAccessKey}"
```

记下服务连接字符串，稍后在本教程中需要用到它。

## <a name="download-the-web-app-from-github"></a>从 GitHub 下载 web 应用

打开命令窗口，然后输入以下命令，从 GitHub 下载示例并更改为示例目录：

```cmd
git clone https://github.com/Azure-Samples/web-apps-node-iot-hub-data-visualization.git
cd web-apps-node-iot-hub-data-visualization
```

## <a name="examine-the-web-app-code"></a>检查 web 应用代码

在 "web 应用-节点-iot-中心数据" 目录中，在你喜爱的编辑器中打开 web 应用。 下面显示了在 VS Code 中查看的文件结构：

![Web 应用文件结构](./media/iot-hub-live-data-visualization-in-web-apps/web-app-files.png)

请花点时间检查以下文件：

* **Node.js**是用于初始化 web 套接字和事件中心包装类的服务端脚本。 它提供对事件中心包装程序类的回调，该类使用该包装器类将传入消息广播到 web 套接字。

* **Event-hub-reader**是一个服务端脚本，它使用指定的连接字符串和使用者组连接到 IoT 中心的内置终结点。 它从传入消息的元数据提取 DeviceId 和 EnqueuedTimeUtc，然后使用 node.js 注册的回调方法中继消息。

* **Chart-device-data**是一种客户端脚本，它侦听 web 套接字，跟踪每个 DeviceId，并为每个设备存储最后50点的传入数据。 然后，它将所选设备数据绑定到 chart 对象。

* **Index. html**处理网页的 UI 布局，并引用客户端逻辑所需的脚本。

## <a name="configure-environment-variables-for-the-web-app"></a>配置 web 应用的环境变量

若要从 IoT 中心读取数据，web 应用需要你的 IoT 中心的连接字符串和应通过读取的使用者组的名称。 它从进程环境中获取 node.js 中的以下行：

```javascript
const iotHubConnectionString = process.env.IotHubConnectionString;
const eventHubConsumerGroup = process.env.EventHubConsumerGroup;
```

在命令窗口中，用以下命令设置环境变量。 将占位符值替换为 IoT 中心的服务连接字符串以及之前创建的使用者组的名称。 不要将字符串括起来。

```cmd
set IotHubConnectionString=YourIoTHubConnectionString
set EventHubConsumerGroup=YourConsumerGroupName
```

## <a name="run-the-web-app"></a>运行 Web 应用

1. 请确保设备正在运行并发送数据。

2. 在命令窗口中，运行以下行以下载和安装引用的包，并启动网站：

   ```cmd
   npm install
   npm start
   ```

3. 你应在控制台中看到输出，指示 web 应用已成功连接到 IoT 中心并在端口3000上侦听：

   ![已在控制台上启动 Web 应用](./media/iot-hub-live-data-visualization-in-web-apps/web-app-console-start.png)

## <a name="open-a-web-page-to-see-data-from-your-iot-hub"></a>打开网页，查看 IoT 中心的数据

打开浏览器以 `http://localhost:3000`。

在 "**选择设备**" 列表中，选择设备以查看由设备发送到 IoT 中心的最近50温度和湿度数据点的正在运行的绘图。

![显示实时温度和湿度的 Web 应用页](./media/iot-hub-live-data-visualization-in-web-apps/web-page-output.png)

还应在控制台中看到输出，其中显示了 web 应用正在广播到浏览器客户端的消息：  

![控制台上的 Web 应用广播输出](./media/iot-hub-live-data-visualization-in-web-apps/web-app-console-broadcast.png)

## <a name="host-the-web-app-in-app-service"></a>在应用服务中托管 web 应用

[Azure App Service 的 Web 应用功能](https://docs.microsoft.com/azure/app-service/overview)提供了用于托管 Web 应用程序的平台即服务（PAAS）。 Azure App Service 中托管的 Web 应用程序可以从功能强大的 Azure 功能（如附加安全性、负载平衡和可伸缩性以及 Azure 和合作伙伴 DevOps 解决方案，如持续部署、包管理等）中受益。 Azure App Service 支持多种常用语言开发并部署在 Windows 或 Linux 基础结构上的 web 应用程序。

在本部分中，将在应用服务中设置 web 应用，并使用 Azure CLI 命令将代码部署到该应用。 可以在[az webapp](https://docs.microsoft.com/cli/azure/webapp?view=azure-cli-latest)文档中找到所用命令的详细信息。 在开始之前，请确保已完成[将资源组添加到 iot 中心](#add-a-consumer-group-to-your-iot-hub)的步骤，[获取 iot 中心的服务连接字符串](#get-a-service-connection-string-for-your-iot-hub)，并[从 GitHub 下载 web 应用](#download-the-web-app-from-github)。

1. [应用服务计划](https://docs.microsoft.com/azure/app-service/overview-hosting-plans)为要运行的应用服务中承载的应用定义一组计算资源。 在本教程中，我们将使用开发人员/免费层来托管 web 应用。 对于免费层，web 应用在与其他应用服务应用（包括其他客户的应用）的共享 Windows 资源上运行。 Azure 还提供了用于在 Linux 计算资源上部署 web 应用的应用服务计划。 如果你已经有想要使用的应用服务计划，则可以跳过此步骤。

   若要使用 Windows 免费层创建应用服务计划，请运行以下命令。 使用 IoT 中心所在的同一资源组。 服务计划名称可以包含大写字母、小写字母、数字和连字符。

   ```azurecli-interactive
   az appservice plan create --name <app service plan name> --resource-group <your resource group name> --sku FREE
   ```

2. 现在，在应用服务计划中预配 web 应用。 使用 `--deployment-local-git` 参数，可以从本地计算机上的 Git 存储库上传和部署 web 应用代码。 Web 应用名称必须是全局唯一的，并且可包含大写字母、数字和连字符。 请确保为 `--runtime` 参数指定节点版本10.6 或更高版本，具体取决于所使用的 node.js 运行时的版本。 您可以使用 `az webapp list-runtimes` 命令获取支持的运行时的列表。

   ```azurecli-interactive
   az webapp create -n <your web app name> -g <your resource group name> -p <your app service plan name> --runtime "node|10.6" --deployment-local-git
   ```

3. 现在为指定 IoT 中心连接字符串和事件中心使用者组的环境变量添加应用程序设置。 单个设置在 `-settings` 参数中以空格分隔。 使用您在本教程前面创建的 IoT 中心和使用者组的服务连接字符串。 不要将值括起来。

   ```azurecli-interactive
   az webapp config appsettings set -n <your web app name> -g <your resource group name> --settings EventHubConsumerGroup=<your consumer group> IotHubConnectionString=<your IoT hub connection string>
   ```

4. 为 web 应用启用 Web 套接字协议，并将 web 应用设置为仅接收 HTTPS 请求（将 HTTP 请求重定向到 HTTPS）。

   ```azurecli-interactive
   az webapp config set -n <your web app name> -g <your resource group name> --web-sockets-enabled true
   az webapp update -n <your web app name> -g <your resource group name> --https-only true
   ```

5. 若要将代码部署到应用服务，你将使用你的[用户级别部署凭据](https://docs.microsoft.com/azure/app-service/deploy-configure-credentials)。 你的用户级部署凭据不同于 Azure 凭据，用于 Git 本地和 FTP 部署到 web 应用。 设置后，它们将在你的 Azure 帐户中的所有订阅中的所有应用服务应用之间有效。 如果你以前设置了用户级部署凭据，则可以使用它们。

   如果以前未设置过用户级部署凭据，或者无法记住密码，请运行以下命令。 部署用户名在 Azure 中必须唯一，并且不能包含本地 Git 推送的 "@" 符号。 出现提示时，输入并确认新密码。 密码必须至少为 8 个字符，且具有字母、数字和符号这三种元素中的两种。

   ```azurecli-interactive
   az webapp deployment user set --user-name <your deployment user name>
   ```

6. 获取用于将代码推送到应用服务的 Git URL。

   ```azurecli-interactive
   az webapp deployment source config-local-git -n <your web app name> -g <your resource group name>
   ```

7. 在应用服务中，将远程应用到引用 web 应用的 Git 存储库的克隆。 对于 "\<Git 克隆 URL"\>，请使用上一步中返回的 URL。 在命令窗口中运行以下命令。

   ```cmd
   git remote add webapp <Git clone URL>
   ```

8. 若要将代码部署到应用服务，请在命令窗口中输入以下命令。 如果系统提示你输入凭据，请输入你在步骤5中创建的用户级部署凭据。 请确保推送到应用服务远程的主分支。

    ```cmd
    git push webapp master:master
    ```

9. 部署进度将在命令窗口中更新。 成功的部署将以类似于以下输出的行结束：

    ```cmd
    remote:
    remote: Finished successfully.
    remote: Running post deployment command(s)...
    remote: Deployment successful.
    To https://contoso-web-app-3.scm.azurewebsites.net/contoso-web-app-3.git
    6b132dd..7cbc994  master -> master
    ```

10. 运行以下命令以查询 web 应用的状态，并确保它正在运行：

    ```azurecli-interactive
    az webapp show -n <your web app name> -g <your resource group name> --query state
    ```

11. 在浏览器中导航至 `https://<your web app name>.azurewebsites.net` 。 与在本地运行 web 应用程序时看到的网页类似。 假设你的设备正在运行并发送数据，则你应看到50的正在运行的绘图，该设备将发送最近的温度和湿度读数。

## <a name="troubleshooting"></a>故障排除

如果在此示例中遇到任何问题，请尝试以下部分中的步骤。 如果仍有问题，请向我们发送反馈。

### <a name="client-issues"></a>客户端问题

* 如果设备未显示在列表中，或者没有绘制图形，请确保设备代码正在设备上运行。

* 在浏览器中，打开开发人员工具（在多个浏览器中，F12 键将打开该工具），然后查找控制台。 查找在该处打印的任何警告或错误。

* 可以在/js/chat-device-data.js. 中调试客户端脚本。

### <a name="local-website-issues"></a>本地网站问题

* 在已启动控制台输出节点的窗口中查看输出。

* 调试服务器代码，特别是 node.js 和/scripts/event-hub-reader.js。

### <a name="azure-app-service-issues"></a>Azure App Service 问题

* 在 Azure 门户中转到你的 web 应用。 在左侧窗格中的 "**监视**" 下，选择 "**应用服务日志**"。 将**应用程序日志记录（文件系统）** 转换为打开，将**级别**设置为 "错误"，然后选择 "**保存**"。 然后打开 "**日志流**（**监视**）"。

* 在 Azure 门户中的 web 应用中，在 "**开发工具**" 下选择 "**控制台**"，并通过 `node -v` 和 `npm -v`验证节点和 npm 版本。

* 如果你看到有关找不到包的错误，则可能是按顺序运行这些步骤。 部署站点时（`git push`），应用服务将运行 `npm install`，该服务基于其配置的节点的当前版本运行。 如果以后在配置中更改，则需要对代码进行无意义的更改，然后再次推送。

## <a name="next-steps"></a>后续步骤

现已成功使用 Web 应用可视化 IoT 中心的实时传感器数据。

若要从 Azure IoT 中心直观显示数据，请参阅[使用 Power BI 直观显示 IoT 中心的实时传感器数据](iot-hub-live-data-visualization-in-power-bi.md)。

[!INCLUDE [iot-hub-get-started-next-steps](../../includes/iot-hub-get-started-next-steps.md)]
