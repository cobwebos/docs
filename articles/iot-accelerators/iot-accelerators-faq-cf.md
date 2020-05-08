---
title: 连接的工厂解决方案常见问题解答 - Azure | Microsoft Docs
description: 本文解答了连接工厂解决方案加速器的常见问题。 它包括指向 GitHub 存储库的链接。
author: dominicbetts
manager: timlt
ms.service: iot-accelerators
services: iot-accelerators
ms.topic: conceptual
ms.date: 12/12/2017
ms.author: dobett
ms.openlocfilehash: 0c8739dff39490f14b613af483f769ac031c1bd9
ms.sourcegitcommit: e0330ef620103256d39ca1426f09dd5bb39cd075
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/05/2020
ms.locfileid: "82792371"
---
# <a name="frequently-asked-questions-for-connected-factory-solution-accelerator"></a>连接的工厂解决方案加速器的常见问题解答

另请参阅 IoT 解决方案加速器的一般[常见问题解答](iot-accelerators-faq.md)。

### <a name="where-can-i-find-the-source-code-for-the-solution-accelerator"></a>在哪里可以找到此解决方案加速器的源代码？

源代码存储在以下 GitHub 存储库中：

* [连接工厂解决方案加速器](https://github.com/Azure/azure-iot-connected-factory)

### <a name="what-is-opc-ua"></a>OPC UA 是什么？

OPC 统一体系结构 (UA) 发布于 2008 年，是一种独立于平台、以服务为导向的互操作性标准。 OPC UA 为工业电脑、PLC 和传感器等多种行业体系和设备所采用。 OPC UA 将 OPC 经典规范的功能集成到一种具有内置安全性的可扩展框架。 它是一种由 OPC 基金会倡导的标准。 [OPC 基金会](https://opcfoundation.org/)是一个拥有 440 多个成员的非营利性组织。 组织的目标是使用 OPC 规范通过以下方面促进多供应商、多平台的安全可靠互操作性：

* 基础结构
* 规格
* 技术
* 进程

### <a name="why-did-microsoft-choose-opc-ua-for-the-connected-factory-solution-accelerator"></a>Microsoft 为何为连接的工厂解决方案加速器选择 OPC UA？

Microsoft 选择 OPC UA 的原因是，OPC UA 是一种开放、非专有、独立于平台、已获得行业认可和已经过验证的标准。 它是 Industrie 4.0 (RAMI4.0) 参考体系结构解决方案的必要条件，可确保大量制造流程和设备之间的互操作性。 Microsoft 看到了其客户对于构建工业 4.0 解决方案的需求。 OPC UA 支持有助于降低客户实现其目标的障碍，并为其提供即时业务价值。

### <a name="how-do-i-add-a-public-ip-address-to-the-simulation-vm"></a>如何将公共 IP 地址添加到模拟 VM？

可通过两种方式添加 IP 地址：

* 使用[存储库](https://github.com/Azure/azure-iot-connected-factory)中的 PowerShell 脚本 `Simulation/Factory/Add-SimulationPublicIp.ps1`。 传入部署名称作为参数。 对于本地部署，请使用 `<your username>ConnFactoryLocal`。 该脚本会列显 VM 的 IP 地址。

* 在 Azure 门户中，找到部署的资源组。 资源组名称即为你指定为解决方案名称或部署名称的名称，本地部署除外。 对于使用 build 脚本的本地部署，资源组名称为 `<your username>ConnFactoryLocal`。 现在向资源组添加新的公共 IP 地址****。

> [!NOTE]
> 在任意情况下，请按照 [Ubuntu 网站](https://wiki.ubuntu.com/Security/Upgrades)上的说明保安装最新修补程序。 保持安装处于最新状态，确保可通过公共 IP 地址访问 VM。

### <a name="how-do-i-remove-the-public-ip-address-to-the-simulation-vm"></a>如何将模拟 VM 的公共 IP 地址删除？

可通过两种方式删除 IP 地址：

* 使用[存储库](https://github.com/Azure/azure-iot-connected-factory)的 PowerShell 脚本 Simulation/Factory/Remove-SimulationPublicIp.ps1。 传入部署名称作为参数。 对于本地部署，请使用 `<your username>ConnFactoryLocal`。 该脚本会列显 VM 的 IP 地址。

* 在 Azure 门户中，找到部署的资源组。 资源组名称即为你指定为解决方案名称或部署名称的名称，本地部署除外。 对于使用 build 脚本的本地部署，资源组名称为 `<your username>ConnFactoryLocal`。 现将公共 IP 地址从资源组删除****。

### <a name="how-do-i-sign-in-to-the-simulation-vm"></a>如何登录到模拟 VM？

只有使用[存储库](https://github.com/Azure/azure-iot-connected-factory)中的 PowerShell 脚本 `build.ps1` 部署解决方案才支持登录到模拟 VM。

如果从 www.azureiotsolutions.com 部署解决方案，则无法登录到 VM。 无法登录，因为密码随机生成且无法重置。

1. 将公共 IP 地址添加到 VM。 请参阅[如何将公共 IP 地址添加到模拟 VM？](#how-do-i-remove-the-public-ip-address-to-the-simulation-vm)
1. 使用 VM IP 地址创建 VM 的 SSH 会话。
1. 要使用的用户名是：`docker`。
1. 要使用的密码取决于部署所用的版本：
    * 对于 2017 年 6 月 1 日前使用 build.ps1 脚本部署的解决方案，密码为：`Passw0rd`。
    * 对于 2017 年 6 月 1 日之后使用 build.ps1 脚本部署的解决方案，密码为：`<name of your deployment>.config.user`。 此密码存储在 VmAdminPassword 设置中****。 除非使用 `build.ps1` 脚本参数 `-VmAdminPassword` 指定密码，否则密码会在部署时随机生成。

### <a name="how-do-i-stop-and-start-all-docker-processes-in-the-simulation-vm"></a>如何在模拟 VM 中停止和启动所有 docker 进程？

1. 登录到模拟 VM。 请参阅[如何登录到模拟 VM？](#how-do-i-sign-in-to-the-simulation-vm)
1. 若要查看处于活动状态的容器，请运行 `docker ps`。
1. 若要停止所有模拟容器，请运行 `./stopsimulation`。
1. 若要启动所有模拟容器：
    * 导出名称为 IOTHUB_CONNECTIONSTRING 的 shell 变量****。 使用 `<name of your deployment>.config.user` 文件中 IotHubOwnerConnectionString 设置的值****。 例如：

        ```sh
        export IOTHUB_CONNECTIONSTRING="HostName={yourdeployment}.azure-devices.net;SharedAccessKeyName=iothubowner;SharedAccessKey={your key}"
        ```

    * 运行 `./startsimulation`。

### <a name="how-do-i-update-the-simulation-in-the-vm"></a>如何更新 VM 中的模拟？

如已对模拟作出更改，可通过 `updatedimulation` 命令使用[存储库](https://github.com/Azure/azure-iot-connected-factory)中的 PowerShell 脚本 `build.ps1`。 此脚本会生成所有模拟组件，停止 VM 中的模拟，上传、安装并启动模拟。

### <a name="how-do-i-find-out-the-connection-string-of-the-iot-hub-used-by-my-solution"></a>如何查找解决方案所使用的 IoT 中心的连接字符串？

如果已使用[存储库](https://github.com/Azure/azure-iot-connected-factory)中的 `build.ps1` 脚本部署解决方案，此连接字符串是 `<name of your deployment>.config.user` 文件中 IotHubOwnerConnectionString 的值****。

还可使用 Azure 门户查找此连接字符串。 在部署资源组中的 IoT 中心找到连接字符串设置。

### <a name="which-iot-hub-devices-does-the-connected-factory-simulation-use"></a>连接的工厂模拟使用哪些 IoT 中心设备？

模拟自己注册以下设备：

* proxy.beijing.corp.contoso
* proxy.capetown.corp.contoso
* proxy.mumbai.corp.contoso
* proxy.munich0.corp.contoso
* proxy.rio.corp.contoso
* proxy.seattle.corp.contoso
* publisher.beijing.corp.contoso
* publisher.capetown.corp.contoso
* publisher.mumbai.corp.contoso
* publisher.munich0.corp.contoso
* publisher.rio.corp.contoso
* publisher.seattle.corp.contoso

使用 [DeviceExplorer](https://github.com/Azure/azure-iot-sdk-csharp/tree/master/tools/) 或[适用于 Azure CLI 的 IoT 扩展](https://github.com/Azure/azure-iot-cli-extension)工具，可以查看解决方案当前所用的 IoT 中心中注册了哪些设备。 若要使用设备资源管理器，则部署中需要 IoT 中心的连接字符串。 若要使用适用于 Azure CLI 的 IoT 扩展，则需要 IoT 中心的名称。

### <a name="how-can-i-get-log-data-from-the-simulation-components"></a>如何从模拟组件获取日志数据？

模拟中的所有组件会将信息记录到日志文件。 可在 VM 中的文件夹 `home/docker/Logs` 中找到这些文件。 若要检索日志，可使用[存储库](https://github.com/Azure/azure-iot-connected-factory)中的 PowerShell 脚本 `Simulation/Factory/Get-SimulationLogs.ps1`。

此脚本需要登录到 VM。 可能需要提供登录凭据。 有关查找凭据的信息，请参阅[如何登录到模拟 VM？](#how-do-i-sign-in-to-the-simulation-vm)

如果 VM 尚无公共 IP 地址或将其删除，该脚本会添加/删除该地址。 该脚本将所有日志文件放入存档，并将存档下载至开发工作站。

也可通过 SSH 登录到 VM 并在运行时检查日志文件。

### <a name="how-can-i-check-if-the-simulation-is-sending-data-to-the-cloud"></a>如何检查模拟是否正向云发送数据？

通过[Azure Iot 浏览器](https://github.com/Azure/azure-iot-explorer)或 " [Azure iot CLI 扩展监视器-事件](https://docs.microsoft.com/cli/azure/ext/azure-iot/iot/hub?view=azure-cli-latest#ext-azure-iot-az-iot-hub-monitor-events)" 命令，你可以检查从特定设备发送到 IoT 中心的数据。 若要使用这些工具，需要知道部署中的 IoT 中心连接字符串。 请参阅[如何查找解决方案所使用的 IoT 中心的连接字符串？](#how-do-i-find-out-the-connection-string-of-the-iot-hub-used-by-my-solution)

检查由以下某个发布者设备发送的数据：

* publisher.beijing.corp.contoso
* publisher.capetown.corp.contoso
* publisher.mumbai.corp.contoso
* publisher.munich0.corp.contoso
* publisher.rio.corp.contoso
* publisher.seattle.corp.contoso

如未看见发送至 IoT 中心的数据，则该模拟存在问题。 首先应分析模拟组件的日志文件。 请参阅[如何从模拟组件获取日志数据？](#how-can-i-get-log-data-from-the-simulation-components) 接下来尝试停止和启动模拟。如果仍无发送的数据，请彻底更新模拟。 请参阅[如何更新 VM 中的模拟？](#how-do-i-update-the-simulation-in-the-vm)

### <a name="how-do-i-enable-an-interactive-map-in-my-connected-factory-solution"></a>如何在连接的工厂解决方案中启用互动地图？

若要在连接的工厂解决方案中启用互动地图，必须具有 Azure Maps 帐户。

从 [www.azureiotsolutions.com](https://www.azureiotsolutions.com) 部署时，部署过程会将 Azure Maps 帐户添加到包含解决方案加速器服务的资源组。

在连接的工厂 GitHub 存储库中使用 `build.ps1` 脚本进行部署时，请将生成窗口中的环境变量 `$env:MapApiQueryKey` 设置为 [Azure Maps 帐户的密钥](../azure-maps/how-to-manage-account-keys.md)。 互动地图随后会自动启用。

此外，还可以在部署后向解决方案加速器添加 Azure Maps 帐户密钥。 导航到 Azure 门户并访问连接的工厂部署中的应用服务资源。 导航到“应用程序设置”，在此处可以找到“应用程序设置”部分********。 将 MapApiQueryKey 设置为 [Azure Maps 帐户的密钥](../azure-maps/how-to-manage-account-keys.md)****。 保存设置，然后导航到“概述”**** 并重新启动应用服务。

### <a name="how-do-i-create-an-azure-maps-account"></a>如何实现创建 Azure Maps 帐户？

请参阅[如何管理 Azure Maps 帐户和密钥](../azure-maps/how-to-manage-account-keys.md)。

### <a name="how-to-obtain-your-azure-maps-account-key"></a>如何获取 Azure Maps 帐户密钥

请参阅[如何管理 Azure Maps 帐户和密钥](../azure-maps/how-to-manage-account-keys.md)。

### <a name="how-do-enable-the-interactive-map-while-debugging-locally"></a>在本地进行调试时如何启用互动地图？

在本地进行调试时，若要启用互动地图，请将你的部署的根目录中的文件 `local.user.config` 和 `<yourdeploymentname>.user.config` 中的设置 `MapApiQueryKey` 的值设置为之前复制的**查询密钥**值。

### <a name="how-do-i-use-a-different-image-at-the-home-page-of-my-dashboard"></a>如何在仪表板的主页上使用一个不同的图像？

若要更改在仪表板的主页上显示的静态图像，请替换图像 `WebApp\Content\img\world.jpg`。 然后，重新生成并重新部署 WebApp。

### <a name="how-do-i-use-non-opc-ua-devices-with-connected-factory"></a>如何将非 OPC UA 设备与连接的工厂一起使用？

若要将遥测数据从非 OPC UA 设备发送到连接的工厂，请执行以下操作：

1. 在 `ContosoTopologyDescription.json` 文件中[在连接的工厂拓扑中配置一个新工作站](iot-accelerators-connected-factory-configure.md)。

1. 以连接的工厂兼容的 JSON 格式引入遥测数据：

    ```json
    [
      {
        "ApplicationUri": "<the_value_of_OpcUri_of_your_station",
        "DisplayName": "<name_of_the_datapoint>",
        "NodeId": "value_of_NodeId_of_your_datapoint_in_the_station",
        "Value": {
          "Value": <datapoint_value>,
          "SourceTimestamp": "<timestamp>"
        }
      }
    ]
    ```

1. `<timestamp>` 的格式为：`2017-12-08T19:24:51.886753Z`

1. 重新启动连接的工厂应用服务。

### <a name="next-steps"></a>后续步骤

还可以浏览 IoT 解决方案加速器的一些其他特性和功能：

* [预测性维护解决方案加速器概述](iot-accelerators-predictive-overview.md)
* [部署连接的工厂解决方案加速器](quickstart-connected-factory-deploy.md)
* [从头保障 IoT 的安全](/azure/iot-fundamentals/iot-security-ground-up)
