---
title: 在 Windows Server 虚拟机上运行 Azure IoT Edge |Microsoft Docs
description: Azure IoT Edge Windows Server Marketplace 虚拟机上的安装说明
author: gregman-msft
manager: arjmands
ms.reviewer: kgremban
ms.service: iot-edge
services: iot-edge
ms.topic: conceptual
ms.date: 06/12/2019
ms.author: gregman
ms.openlocfilehash: 99474246bf1ff5cbcc39861d56f05aa38f177f31
ms.sourcegitcommit: 38b11501526a7997cfe1c7980d57e772b1f3169b
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/22/2020
ms.locfileid: "76510033"
---
# <a name="run-azure-iot-edge-on-windows-server-virtual-machines"></a>在 Windows Server 虚拟机上运行 Azure IoT Edge

使用 Azure IoT Edge 运行时可将设备转变为 IoT Edge 设备。 该运行时可以部署在像 Raspberry Pi 一样小的设备上，也可以部署在像工业服务器一样大的设备上。 使用 IoT Edge 运行时配置设备后，即可开始从云中部署业务逻辑。

若要了解有关 IoT Edge 运行时如何工作以及包含哪些组件的详细信息，请参阅[了解 Azure IoT Edge 运行时及其体系结构](iot-edge-runtime.md)。

本文列出了使用[Windows server](https://azuremarketplace.microsoft.com/marketplace/apps/microsoftwindowsserver.windowsserver?tab=Overview) Azure Marketplace 产品/服务在 windows server 2019 虚拟机上运行 Azure IoT Edge 运行时的步骤。 按照在 Windows 上[安装 Azure IoT Edge 运行时](how-to-install-iot-edge-windows.md)中的说明与其他版本配合使用。

## <a name="deploy-from-the-azure-marketplace"></a>从 Azure 市场部署

1. 导航至[Windows server](https://azuremarketplace.microsoft.com/marketplace/apps/microsoftwindowsserver.windowsserver?tab=Overview) azure marketplace 产品/服务，或通过在[Azure marketplace](https://azuremarketplace.microsoft.com/)上搜索 "windows server"
2. 选择 "**立即获取**"
3. 在 "**软件计划**" 中，找到 "Windows Server 2019 Datacenter server Core with 容器"，然后在下一个对话框中选择 "**继续**"。
    * 你还可以将这些说明用于具有容器的 Windows Server 的其他版本
4. 进入 Azure 门户后，选择“创建”并遵循向导部署 VM。
    * 如果是第一次尝试 VM，则使用密码并在公共入站端口菜单中启用 RDP 和 SSH 是最简单的方法。
    * 如果你有资源密集型工作负荷，应通过添加更多 CPU 和/或内存来升级虚拟机大小。
5. 部署虚拟机后，请将其配置为连接到 IoT 中心：
    1. 从在 IoT 中心内创建的 IoT Edge 设备中复制设备连接字符串。 请参阅过程[检索 Azure 门户中的连接字符串](how-to-register-device.md#retrieve-the-connection-string-in-the-azure-portal)。
    1. 在 Azure 门户中选择新建的虚拟机资源，并打开“运行命令”选项
    1. 选择**RunPowerShellScript**选项
    1. 将此脚本复制到命令窗口中的设备连接字符串：

        ```powershell
        . {Invoke-WebRequest -useb aka.ms/iotedge-win} | Invoke-Expression; `
        Install-IoTEdge -Manual -DeviceConnectionString '<connection-string>'
        ```

    1. 执行脚本以安装 IoT Edge 运行时并通过选择 "**运行**" 设置连接字符串
    1. 一两分钟后，应该会看到一条消息，指出已成功安装并设置了 Edge 运行时。

## <a name="deploy-from-the-azure-portal"></a>从 Azure 门户部署

1. 在 Azure 门户中，搜索 "Windows Server" 并选择 " **Windows server 2019 Datacenter** " 以开始创建 VM 工作流。
2. 从 "**选择软件计划**" 中选择 "使用容器的 Windows Server 2019 Datacenter server Core"，然后选择 "**创建**"
3. 完成上述 "从 Azure Marketplace 部署" 说明中的步骤5。

## <a name="deploy-from-azure-cli"></a>从 Azure CLI 部署

1. 如果在桌面上使用 Azure CLI，请先登录：

   ```azurecli-interactive
   az login
   ```

1. 如果你有多个订阅，请选择要使用的订阅：
   1. 列出订阅：

      ```azurecli-interactive
      az account list --output table
      ```

   1. 复制要使用的订阅的 SubscriptionID 字段
   1. 用复制的 ID 运行此命令：

      ```azurecli-interactive
      az account set -s {SubscriptionId}
      ```

1. 创建新资源组（或者在后续步骤中指定现有的资源组）：

   ```azurecli-interactive
   az group create --name IoTEdgeResources --location westus2
   ```

1. 创建新虚拟机：

   ```azurecli-interactive
   az vm create -g IoTEdgeResources -n EdgeVM --image MicrosoftWindowsServer:WindowsServer:2019-Datacenter-Core-with-Containers:latest  --admin-username azureuser --generate-ssh-keys --size Standard_DS1_v2
   ```

   * 此命令将提示你输入密码，但你可以添加选项 `--admin-password` 以便在脚本中更轻松地对其进行设置
   * Windows Server Core 映像仅支持带远程桌面的命令行，因此，如果想要获得完整的桌面体验，请将 `MicrosoftWindowsServer:WindowsServer:2019-Datacenter-with-Containers:latest` 指定为图像

1. 设置设备连接字符串（如果不熟悉此过程，可以按照[Azure CLI 过程检索连接字符串](how-to-register-device.md#retrieve-the-connection-string-with-the-azure-cli)）：

   ```azurecli-interactive
   az vm run-command invoke -g IoTEdgeResources -n EdgeVM --command-id RunPowerShellScript --script ". {Invoke-WebRequest -useb aka.ms/iotedge-win} | Invoke-Expression; `Install-IoTEdge -Manual -DeviceConnectionString '<connection-string>'"
   ```

## <a name="next-steps"></a>后续步骤

预配了安装运行时的 IoT Edge 设备后，现在可以[部署 IoT Edge 模块](how-to-deploy-modules-portal.md)。

如果在正确安装 Edge 运行时遇到问题，请查看[故障排除](troubleshoot.md)页。

若要将现有安装更新到最新版本的 IoT Edge，请参阅[更新 IoT Edge 安全守护程序和运行时](how-to-update-iot-edge.md)。

在[Windows 虚拟机文档](https://docs.microsoft.com/azure/virtual-machines/windows/)中了解有关使用 Windows 虚拟机的详细信息。

如果要在安装后通过 SSH 连接到此 VM，请遵循使用远程桌面或远程 powershell[安装适用于 Windows Server 的 OpenSSH](https://docs.microsoft.com/windows-server/administration/openssh/openssh_install_firstuse#installing-openssh-with-powershell)指南。
