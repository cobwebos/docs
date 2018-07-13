---
title: 快速入门 Azure IoT Edge + Windows | Microsoft Docs
description: 通过在模拟边缘设备上运行分析来试用 Azure IoT Edge
author: kgremban
manager: timlt
ms.author: kgremban
ms.date: 06/24/2018
ms.topic: quickstart
ms.service: iot-edge
services: iot-edge
ms.custom: mvc
ms.openlocfilehash: 11b2fccf3c02555f50f48252f2cd9968c9ec90d7
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/11/2018
ms.locfileid: "38632878"
---
# <a name="quickstart-deploy-your-first-iot-edge-module-from-the-azure-portal-to-a-windows-device---preview"></a>快速入门：将第一个 IoT Edge 模块从 Azure 门户部署到 Windows 设备 - 预览

此快速入门中，使用 Azure IoT Edge 云接口将预生成的代码远程部署到 IoT Edge 设备。 若要完成此任务，首先使用 Windows 设备模拟 IoT Edge 设备，然后即可向其部署模块。

此快速入门介绍如何：

1. 创建 IoT 中心。
2. 将 IoT Edge 设备注册到 IoT 中心。
3. 在设备上安装并启动 IoT Edge 运行时。
4. 以远程方式将模块部署到 IoT Edge 设备并将遥测数据发送到 IoT 中心。

![教程体系结构][2]

在本快速入门中部署的模块为模拟传感器，可以生成温度、湿度和压强数据。 其他 Azure IoT Edge 教程均以本教程中通过部署模块（这些模块通过分析模拟数据来获得业务见解）执行的操作为基础。 

>[!NOTE]
>Windows 上的 IoT Edge 运行时为[公开预览版](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

如果没有可用的 Azure 订阅，可以在开始前创建一个[免费帐户][lnk-account]。

## <a name="prerequisites"></a>先决条件

本快速入门可以将 Windows 计算机或虚拟机变成 IoT Edge 设备。 如果正在虚拟机中运行 Windows，则启用[嵌套虚拟化][lnk-nested]并分配至少 2GB 内存。 

在用于 IoT Edge 设备的计算机上做好以下先决条件准备：

1. 确保使用的是支持的 Windows 版本：
   * Windows 10 或更高版本
   * Windows Server 2016 或更高版本
2. 安装[用于 Windows 的 Docker][lnk-docker] 并确保其正在运行。
3. 将 Docker 配置为使用 [Linux 容器](https://docs.docker.com/docker-for-windows/#switch-between-windows-and-linux-containers)

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

请使用 Azure CLI 完成本快速入门中的多个步骤。Azure IoT 有一个可以启用其他功能的扩展。 

将 Azure IoT 扩展添加到 Cloud Shell 实例。

   ```azurecli-interactive
   az extension add --name azure-cli-iot-ext
   ```

## <a name="create-an-iot-hub"></a>创建 IoT 中心

在 Azure 门户中创建 IoT 中心，启动快速入门。
![创建 IoT 中心][3]

免费级的 IoT 中心适用于此快速入门。 如果曾经用过 IoT 中心并且已创建免费的中心，则可使用该 IoT 中心。 每个订阅仅能有一个免费 IoT 中心。 

1. 在 Azure Cloud Shell 中创建一个资源组。 以下代码在“美国西部”区域创建名为“TestResources”的资源组。 将快速入门和教程的所有资源置于一个组中可以对其集中管理。 

   ```azurecli-interactive
   az group create --name TestResources --location westus
   ```

1. 在新的资源组中创建 IoT 中心。 以下代码在资源组“TestResources”中创建免费的“F1”中心。 将 *{hub_name}* 替换为 IoT 中心的唯一名称。

   ```azurecli-interactive
   az iot hub create --resource-group TestResources --name {hub_name} --sku F1 
   ```

## <a name="register-an-iot-edge-device"></a>注册 IoT Edge 设备

使用新创建的 IoT 中心注册 IoT Edge 设备。
![注册设备][4]

为模拟设备创建设备标识，以便它可以与 IoT 中心通信。 由于 IoT Edge 设备的行为和托管方式与典型 IoT 设备不同，请从一开始就将此设备声明为 IoT Edge 设备。 

1. 在 Azure Cloud Shell 中输入以下命令，以便在中心创建名为 **myEdgeDevice** 的设备。

   ```azurecli-interactive
   az iot hub device-identity create --device-id myEdgeDevice --hub-name {hub_name} --edge-enabled
   ```

1. 检索设备的连接字符串，该字符串将物理设备与其在 IoT 中心的标识链接在一起。 

   ```azurecli-interactive
   az iot hub device-identity show-connection-string --device-id myEdgeDevice --hub-name {hub_name}
   ```

1. 复制并保存连接字符串。 在下一部分中配置 IoT Edge 运行时时将用到此值。 

## <a name="install-and-start-the-iot-edge-runtime"></a>安装和启动 IoT Edge 运行时

在 IoT Edge 设备上安装并启动 Azure IoT Edge 运行时。 
![注册设备][5]

IoT Edge 运行时部署在所有 IoT Edge 设备上。 它有三个组件。 每次某个 Edge 设备在启动后通过启动 IoT Edge 代理来启动此设备时，**IoT Edge 安全守护程序**就会启动。 **IoT Edge 代理**协助部署和监视 IoT Edge 设备（包括 IoT Edge 中心）的模块。 IoT Edge 中心管理 IoT Edge 设备模块之间以及设备和 Azure IoT 中心之间的通信。 

>[!NOTE]
>安装脚本仍在开发之中，因此此部分的安装步骤目前仍需手动完成。 

此部分的说明要求为 IoT Edge 运行时配置 Linux 容器。 若要使用 Windows 容器，请参阅[在 Windows 上安装与 Windows 容器配合使用的 Azure IoT Edge 运行时](how-to-install-iot-edge-windows-with-windows.md)。

### <a name="download-and-install-the-iot-edge-service"></a>下载并安装 IoT Edge 服务

1. 在 IoT Edge 设备上，以管理员身份运行 PowerShell。

2. 下载 IoT Edge 服务包。

  ```powershell
  Invoke-WebRequest https://aka.ms/iotedged-windows-latest -o .\iotedged-windows.zip
  Expand-Archive .\iotedged-windows.zip C:\ProgramData\iotedge -f
  Move-Item c:\ProgramData\iotedge\iotedged-windows\* C:\ProgramData\iotedge\ -Force
  rmdir C:\ProgramData\iotedge\iotedged-windows
  $env:Path += ";C:\ProgramData\iotedge"
  SETX /M PATH "$env:Path"
  ```

3. 安装 vcruntime。

  ```powershell
  Invoke-WebRequest -useb https://download.microsoft.com/download/0/6/4/064F84EA-D1DB-4EAA-9A5C-CC2F0FF6A638/vc_redist.x64.exe -o vc_redist.exe
  .\vc_redist.exe /quiet /norestart
  ```

4. 创建并启动 IoT Edge 服务。

   ```powershell
   New-Service -Name "iotedge" -BinaryPathName "C:\ProgramData\iotedge\iotedged.exe -c C:\ProgramData\iotedge\config.yaml"
   Start-Service iotedge
   ```

5. 为 IoT Edge 服务使用的端口添加防火墙例外。

   ```powershell
   New-NetFirewallRule -DisplayName "iotedged allow inbound 15580,15581" -Direction Inbound -Action Allow -Protocol TCP -LocalPort 15580-15581 -Program "C:\programdata\iotedge\iotedged.exe" -InterfaceType Any
   ```

6. 创建名为 **iotedge.reg** 的新文件并使用文本编辑器将其打开。 

7. 添加以下内容，然后保存该文件。 

   ```input
   Windows Registry Editor Version 5.00
   [HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\EventLog\Application\iotedged]
   "CustomSource"=dword:00000001
   "EventMessageFile"="C:\\ProgramData\\iotedge\\iotedged.exe"
   "TypesSupported"=dword:00000007
   ```

8. 在文件资源管理器中导航到该文件并双击它，以便将所做的更改导入到 Windows 注册表。 

### <a name="configure-the-iot-edge-runtime"></a>配置 IoT Edge 运行时 

使用在注册新设备时复制的 IoT Edge 设备连接字符串配置此运行时。 然后，配置运行时网络。 

1. 打开 IoT Edge 配置文件，其位置为 `C:\ProgramData\iotedge\config.yaml`。 该文件是受保护的文件，因此请以管理员身份运行一个文本编辑器（例如记事本），然后使用编辑器打开文件。 

2. 找到“预配”节，使用从 IoT Edge 设备详细信息中复制的字符串更新 **device_connection_string** 的值。 

3. 在管理员 PowerShell 窗口中，检索 IoT Edge 设备的主机名并复制输出。 

   ```powershell
   hostname
   ```

4. 在配置文件中，找到“Edge 设备主机名”节。 使用从 PowerShell 复制的主机名更新 **hostname** 的值。

3. 在管理员 PowerShell 窗口中，检索 IoT Edge 设备的 IP 地址。 

   ```powershell
   ipconfig
   ```

4. 复制输出的 **vEthernet (DockerNAT)** 部分的“IPv4 地址”的值。 

5. 创建名为 **IOTEDGE_HOST** 的环境变量，将 *\<ip_address\>* 替换为 IoT Edge 设备的 IP 地址。 

  ```powershell
  [Environment]::SetEnvironmentVariable("IOTEDGE_HOST", "http://<ip_address>:15580")
  ```
  
  在重启时保留环境变量。

  ```powershell
  SETX /M IOTEDGE_HOST "http://<ip_address>:15580"
  ```


6. 在 `config.yaml` 文件中，找到“Connect 设置”节。 将 **management_uri** 和 **workload_uri** 值更新为你的 IP 地址以及在上一部分打开的端口。 将 **\<GATEWAY_ADDRESS\>** 替换为你的 IP 地址。 

   ```yaml
   connect: 
     management_uri: "http://<GATEWAY_ADDRESS>:15580"
     workload_uri: "http://<GATEWAY_ADDRESS>:15581"
   ```

7. 找到“Listen 设置”节，为 **management_uri** 和 **workload_uri** 添加相同的值。 

   ```yaml
   listen:
     management_uri: "http://<GATEWAY_ADDRESS>:15580"
     workload_uri: "http://<GATEWAY_ADDRESS>:15581"
   ```

8. 找到“Moby Container Runtime 设置”节，验证是否已将 **network** 的值设置为 `nat`。

9. 保存此配置文件。 

10. 在 PowerShell 中重启 IoT Edge 服务。

   ```powershell
   Stop-Service iotedge -NoWait
   sleep 5
   Start-Service iotedge
   ```

### <a name="view-the-iot-edge-runtime-status"></a>查看 IoT Edge 运行时状态

验证是否已成功安装并配置运行时。

1. 检查 IoT Edge 服务的状态。

   ```powershell
   Get-Service iotedge
   ```

2. 若需排查服务问题，请检索服务日志。 

   ```powershell
   # Displays logs from today, newest at the bottom.

   Get-WinEvent -ea SilentlyContinue `
    -FilterHashtable @{ProviderName= "iotedged";
      LogName = "application"; StartTime = [datetime]::Today} |
    select TimeCreated, Message |
    sort-object @{Expression="TimeCreated";Descending=$false}
   ```

3. 查看在 IoT Edge 设备上运行的所有模块。 由于此服务是第一次运行，因此只会看到 **edgeAgent** 模块在运行。 edgeAgent 模块会默认运行，用于安装并启动部署到设备的任何其他模块。 

   ```powershell
   iotedge list
   ```

   ![查看设备上的一个模块](./media/quickstart/iotedge-list-1.png)

## <a name="deploy-a-module"></a>部署模块

从云端管理 Azure IoT Edge 设备，部署将遥测数据发送到 IoT 中心的模块。
![注册设备][6]

[!INCLUDE [iot-edge-deploy-module](../../includes/iot-edge-deploy-module.md)]

## <a name="view-generated-data"></a>查看生成的数据

此快速入门中，创建了新的 IoT Edge 设备，并在该设备上安装了 IoT Edge 运行时。 然后，使用了 Azure 门户推送 IoT Edge 模块，使其在不更改设备本身的情况下在设备上运行。 这种情况下，推送的模块创建可用于本教程的环境数据。 

确认从云中部署的模块正在 IoT Edge 设备上运行。 

```powershell
iotedge list
```

   ![查看设备上的三个模块](./media/quickstart/iotedge-list-2.png)

查看从 tempSensor 模块发送到云的消息。 

```powershell
iotedge logs tempSensor -f
```

  ![查看模块中的数据](./media/quickstart/iotedge-logs.png)

也可使用 [IoT 中心资源管理器工具][lnk-iothub-explorer]或 [Visual Studio Code 的 Azure IoT Toolkit 扩展](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-toolkit)查看 IoT 中心接收的消息。 

## <a name="clean-up-resources"></a>清理资源

可以使用在此快速入门中配置的模拟设备来测试 IoT Edge 教程。 若要阻止 tempSensor 模块向 IoT 中心发送数据，请使用以下命令停止 IoT Edge 服务并删除在设备上创建的容器。 需要再次将计算机用作 IoT Edge 设备时，请记住启动此服务。 

   ```powershell
   Stop-Service iotedge -NoWait
   docker rm -f $(docker ps -aq)
   ```

如果不再需要所创建的 Azure 资源，则可使用以下命令删除已创建的资源组及其关联的任何资源：

   ```azurecli-interactive
   az group delete --name TestResources
   ```

## <a name="next-steps"></a>后续步骤

在本快速入门中，你创建了新的 IoT Edge 设备并使用 Azure IoT Edge 云接口将代码部署到该设备上。 现在，你有了一个可以生成其环境的原始数据的测试设备。 

可以继续阅读其他教程，了解如何使用 Azure IoT Edge 将此数据转化为边缘业务见解。

> [!div class="nextstepaction"]
> [使用 Azure Functions 筛选传感器数据](tutorial-deploy-function.md)


<!-- Images -->
[1]: ./media/quickstart/cloud-shell.png
[2]: ./media/quickstart/install-edge-full.png
[3]: ./media/quickstart/create-iot-hub.png
[4]: ./media/quickstart/register-device.png
[5]: ./media/quickstart/start-runtime.png
[6]: ./media/quickstart/deploy-module.png


<!-- Links -->
[lnk-docker]: https://docs.docker.com/docker-for-windows/install/ 
[lnk-iothub-explorer]: https://github.com/azure/iothub-explorer
[lnk-account]: https://azure.microsoft.com/free
[lnk-portal]: https://portal.azure.com
[lnk-nested]: https://docs.microsoft.com/virtualization/hyper-v-on-windows/user-guide/nested-virtualization
[lnk-delete]: https://docs.microsoft.com/cli/azure/iot/hub?view=azure-cli-latest#az_iot_hub_delete

<!-- Anchor links -->
[anchor-register]: #register-an-iot-edge-device
