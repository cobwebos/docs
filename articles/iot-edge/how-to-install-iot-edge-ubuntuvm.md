---
title: 在 Ubuntu 虚拟机上运行 Azure IoT Edge | Microsoft Docs
description: 有关在 Ubuntu 16.04 Azure 市场虚拟机上设置 Azure IoT Edge 的说明
author: gregman
manager: arjmands
ms.reviewer: kgremban
ms.service: iot-edge
services: iot-edge
ms.topic: conceptual
ms.date: 02/01/2019
ms.author: gregman
ms.openlocfilehash: 7ff7671425e2a2a5dbebe2d09cadb8ef71bc7c97
ms.sourcegitcommit: e51e940e1a0d4f6c3439ebe6674a7d0e92cdc152
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/08/2019
ms.locfileid: "55896540"
---
# <a name="run-azure-iot-edge-on-ubuntu-virtual-machines"></a>在 Ubuntu 虚拟机上运行 Azure IoT Edge

使用 Azure IoT Edge 运行时可将设备转变为 IoT Edge 设备。 该运行时可以部署在像 Raspberry Pi 一样小的设备上，也可以部署在像工业服务器一样大的设备上。 使用 IoT Edge 运行时配置设备后，即可开始从云中部署业务逻辑。

若要了解有关 IoT Edge 运行时如何工作以及包含哪些组件的详细信息，请参阅[了解 Azure IoT Edge 运行时及其体系结构](iot-edge-runtime.md)。

本文列出了使用预配置的 [Ubuntu Azure 市场套餐中的 Azure IoT Edge](http://aka.ms/azure-iot-edge-ubuntuvm) 在 Ubuntu 16.04 虚拟机上运行 Azure IoT Edge 运行时的步骤。 

首次启动时，Ubuntu VM 上的 Azure IoT Edge 会预装最新版本的 Azure IoT Edge 运行时。 它还包含一个脚本用于设置连接字符串并重启运行时（可以通过 Azure VM 门户或 Azure 命令行远程触发重启），这样，你便可以轻松配置和连接 IoT Edge 设备，而无需启动 SSH 或远程桌面会话。 此脚本会等待设置连接字符串并完全安装 IoT Edge 客户端为止，因此，你不需要在自动化中配置这些步骤。

## <a name="deploy-from-the-azure-marketplace"></a>从 Azure 市场部署
1.  导航到 [Ubuntu 上的 Azure IoT Edge](https://aka.ms/azure-iot-edge-ubuntuvm) 市场套餐，或者在 [Azure 市场](https://azuremarketplace.microsoft.com/)中搜索“Ubuntu 上的 Azure IoT Edge”
2.  选择“立即获取”，然后在下一个对话框中单击“继续”。
3.  进入 Azure 门户后，选择“创建”并遵循向导部署 VM。 
    *   首次试用 VM 时，在公共入站端口菜单中可以十分方便地使用密码和启用 SSH。 
    *   如果你有资源密集型工作负荷，应通过添加更多 CPU 和/或内存来升级虚拟机大小。
4.  部署虚拟机后，请将其配置为连接到 IoT 中心：
    1.  复制在 IoT 中心创建的 IoT Edge 设备中的设备连接字符串（如果你不熟悉此过程，可以遵循[从 Azure 门户注册新的 Azure IoT Edge 设备](how-to-register-device-portal.md)操作指南）
    1.  在 Azure 门户中选择新建的虚拟机资源，并打开“运行命令”选项
    1.  选择“RunShellScript”选项
    1.  使用设备连接字符串通过命令窗口执行以下脚本：`/etc/iotedge/configedge.sh “{device_connection_string}”`
    1.  选择“运行”
    1.  片刻之后，屏幕上应会显示一条成功消息，指出已成功设置了连接字符串。


## <a name="deploy-from-the-azure-portal"></a>从 Azure 门户部署
在 Azure 门户中，搜索“Azure IoT Edge”并选择“Ubuntu Server 16.04 LTS + Azure IoT Edge 运行时”开始 VM 创建工作流。 在此处，请完成上述“从 Azure 市场部署”说明中的步骤 3 到 4。

## <a name="deploy-from-azure-cli"></a>从 Azure CLI 部署
1.  首次通过 CLI 部署虚拟机时，需要为 Azure 订阅启用编程部署：
    1. 打开 [Ubuntu 上的 Azure IoT Edge](https://aka.ms/azure-iot-edge-ubuntuvm) 市场套餐
    1. 选择“立即获取”，然后在下一个对话框中单击“继续”
    1. 在门户中对话框的底部选择“想要以编程方式进行部署?开始使用”
    1. 在“配置编程部署”页中单击“启用”按钮，然后单击“保存”
1.  如果在桌面上使用 Azure CLI，请先登录：

    ```azurecli-interactive
    az login
    ```
    
1.  如果你有多个订阅，请选择要使用的订阅：
    1.  列出订阅：
    
       ```azurecli-interactive
       azure account list --output table
       ```
    
    1.  复制要使用的订阅的 SubscriptionID 字段
    1.  结合刚刚复制的 ID 运行以下命令：
    
       ```azurecli-interactive 
       az account set -s {SubscriptionId}
       ```
    
1.  创建新资源组（或者在后续步骤中指定现有的资源组）：

    ```azurecli-interactive
    az group create --name IoTEdgeResources --location westus2
    ```
    
1.  创建新虚拟机：

    ```azurecli-interactive
    az vm create --resource-group IoTEdgeResources --name EdgeVM –image microsoft_iot_edge:iot_edge_vm_ubuntu:ubuntu_1604_edgeruntimeonly:latest --admin-username azureuser --generate-ssh-keys --size Standard_DS1_v2
    ```

1.  设置设备连接字符串（如果你不熟悉此过程，可以遵循[使用 Azure CLI 注册新的 Azure IoT Edge 设备](how-to-register-device-cli.md)操作指南）：

    ```azurecli-interactive
    az vm run-command invoke -g IoTEdgeResources -n EdgeVM --command-id RunShellScript --script '/etc/iotedge/configedge.sh "{device_connection_string}"'
    ```

设置后若要通过 SSH 连接到此 VM，请在该命令中使用 publicIpAddress：`ssh azureuser@{publicIpAddress}`


## <a name="next-steps"></a>后续步骤

预配了安装运行时的 IoT Edge 设备后，现在可以[部署 IoT Edge 模块](how-to-deploy-modules-portal.md)。

如果无法正确安装 Edge 运行时，请参阅[故障排除](troubleshoot.md)页。

若要将现有安装更新到最新版本的 IoT Edge，请参阅[更新 IoT Edge 安全守护程序和运行时](how-to-update-iot-edge.md)。
