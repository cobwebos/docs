---
title: 在 Ubuntu 虚拟机上运行 Azure IoT Edge | Microsoft Docs
description: Ubuntu 18.04 LTS 虚拟机的 Azure IoT 边缘设置说明
author: toolboc
manager: veyalla
ms.reviewer: kgremban
ms.service: iot-edge
services: iot-edge
ms.topic: conceptual
ms.date: 03/19/2020
ms.author: pdecarlo
ms.openlocfilehash: 64e2787aa282e75893fa34e6de1373e6afed09fe
ms.sourcegitcommit: 8a9c54c82ab8f922be54fb2fcfd880815f25de77
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/27/2020
ms.locfileid: "80349596"
---
# <a name="run-azure-iot-edge-on-ubuntu-virtual-machines"></a>在 Ubuntu 虚拟机上运行 Azure IoT Edge

使用 Azure IoT Edge 运行时可将设备转变为 IoT Edge 设备。 该运行时可以部署在像 Raspberry Pi 一样小的设备上，也可以部署在像工业服务器一样大的设备上。 使用 IoT Edge 运行时配置设备后，即可开始从云中部署业务逻辑。

若要了解有关 IoT Edge 运行时如何工作以及包含哪些组件的详细信息，请参阅[了解 Azure IoT Edge 运行时及其体系结构](iot-edge-runtime.md)。

本文列出了部署 Ubuntu 18.04 LTS 虚拟机的步骤，该虚拟机安装了 Azure IoT Edge 运行时，并使用预提供的设备连接字符串进行了配置。 部署使用在[iotedge-vm 部署](https://github.com/Azure/iotedge-vm-deploy)项目存储库中维护[的基于云的](../virtual-machines/linux/using-cloud-init.md
) [Azure 资源管理器模板](../azure-resource-manager/templates/overview.md)完成。

在第一次启动时，Ubuntu 18.04 LTS 虚拟机[通过云 init 安装最新版本的 Azure IoT 边缘运行时](https://github.com/Azure/iotedge-vm-deploy/blob/master/cloud-init.txt)。 它还在运行时开始之前设置提供的连接字符串，使您能够轻松配置和连接 IoT Edge 设备，而无需启动 SSH 或远程桌面会话。 

## <a name="deploy-using-deploy-to-azure-button"></a>使用部署到 Azure 按钮进行部署

["部署到 Azure"按钮](../azure-resource-manager/templates/deploy-to-azure-button.md)允许简化在 GitHub 上维护[的 Azure 资源管理器模板的部署](../azure-resource-manager/templates/overview.md)。  本节将演示[iotedge-vm 部署](https://github.com/Azure/iotedge-vm-deploy)项目存储库中包含的"部署到 Azure 按钮"的使用情况。  


1. 我们将使用 iotedge-vm 部署 Azure 资源管理器模板部署启用 Azure IoT Edge 的 Linux VM。  首先，单击下面的按钮：

    [![部署到 Azure 按钮以进行 iotedge-vm 部署](https://aka.ms/deploytoazurebutton)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2Fazure%2Fiotedge-vm-deploy%2Fmaster%2FedgeDeploy.json)

1. 在新启动的窗口中，填写可用的表单字段：

    > [!div class="mx-imgBorder"]
    > [![显示 iotedge-vm 部署模板的屏幕截图](./media/how-to-install-iot-edge-ubuntuvm/iotedge-vm-deploy.png)](./media/how-to-install-iot-edge-ubuntuvm/iotedge-vm-deploy.png)

    **订阅**：要将虚拟机部署到的活动 Azure 订阅。

    **资源组**：包含虚拟机及其关联资源的现有或新创建的资源组。

    **DNS 标签前缀**：用于前缀虚拟机主机名的您选择的必需值。

    **管理员用户名**：用户名，将在部署时提供根权限。

    **设备连接字符串**：在预期[IoT 中心](../iot-hub/about-iot-hub.md)中创建的[设备的设备连接字符串](how-to-register-device.md)。

    **VM 大小**：要部署的虚拟机[的大小](../cloud-services/cloud-services-sizes-specs.md)

    **Ubuntu OS 版本**：要安装在基本虚拟机上的 Ubuntu 操作系统的版本。

    **位置**：要将虚拟机部署到的[地理区域](https://azure.microsoft.com/global-infrastructure/locations/)，此值默认为所选资源组的位置。

    **身份验证类型**：根据您的偏好选择**sshPublicKey**或**密码**。

    **管理员密码或密钥**：SSH 公钥的值或密码的值，具体取决于身份验证类型的选择。

    填写所有字段后，选择页面底部的复选框以接受条款，然后选择 **"购买"** 以开始部署。

1. 验证部署是否成功完成。  虚拟机资源应已部署到选定的资源组中。  请注意机器名称，这应该是格式`vm-0000000000000`。 此外，请注意关联的 DNS**名称**，该名称应采用 格式`<dnsLabelPrefix>`。`<location>`.cloudapp.azure.com.

    **DNS 名称**可以从 Azure 门户中新部署的虚拟机的 **"概述"** 部分获取。

    > [!div class="mx-imgBorder"]
    > [![显示 iotedge vm 的 dns 名称的屏幕截图](./media/how-to-install-iot-edge-ubuntuvm/iotedge-vm-dns-name.png)](./media/how-to-install-iot-edge-ubuntuvm/iotedge-vm-dns-name.png)

1. 如果要在设置后将 SSH 放入此 VM，请使用具有以下命令的关联**DNS 名称**：`ssh <adminUsername>@<DNS_Name>`

## <a name="deploy-from-azure-cli"></a>从 Azure CLI 部署

1. 确保已安装 Azure CLI iot 扩展：
    ```azurecli-interactive
    az extension add --name azure-iot
    ```

1. 接下来，如果您在桌面上使用 Azure CLI，请从登录开始：

   ```azurecli-interactive
   az login
   ```

1. 如果您有多个订阅，请选择要使用的订阅：
   1. 列出订阅：

      ```azurecli-interactive
      az account list --output table
      ```

   1. 复制要使用的订阅的订阅 ID 字段。

   1. 使用复制的 ID 设置工作订阅：

      ```azurecli-interactive
      az account set -s <SubscriptionId>
      ```

1. 创建新资源组（或者在后续步骤中指定现有的资源组）：

   ```azurecli-interactive
   az group create --name IoTEdgeResources --location westus2
   ```

1. 创建新虚拟机：

    要使用 的`password`**身份验证类型**，请参阅下面的示例：

   ```azurecli-interactive
   az group deployment create \
   --name edgeVm \
   --resource-group IoTEdgeResources \
   --template-uri "https://aka.ms/iotedge-vm-deploy" \
   --parameters dnsLabelPrefix='my-edge-vm1' \
   --parameters adminUsername='<REPLACE_WITH_USERNAME>' \
   --parameters deviceConnectionString=$(az iot hub device-identity show-connection-string --device-id <REPLACE_WITH_DEVICE-NAME> --hub-name <REPLACE-WITH-HUB-NAME> -o tsv) \
   --parameters authenticationType='password' \
   --parameters adminPasswordOrKey="<REPLACE_WITH_SECRET_PASSWORD>"
   ```

    要使用 SSH 密钥进行身份验证，可以通过指定 的`sshPublicKey`**身份验证类型**来执行此操作，然后在**adminPasswordOrKey**参数中提供 SSH 密钥的值。  下面显示了一个示例。

    ```azurecli-interactive
    #Generate the SSH Key
    ssh-keygen -m PEM -t rsa -b 4096 -q -f ~/.ssh/iotedge-vm-key -N ""  

    #Create a VM using the iotedge-vm-deploy script
    az group deployment create \
    --name edgeVm \
    --resource-group IoTEdgeResources \
    --template-uri "https://aka.ms/iotedge-vm-deploy" \
    --parameters dnsLabelPrefix='my-edge-vm1' \
    --parameters adminUsername='<REPLACE_WITH_USERNAME>' \
    --parameters deviceConnectionString=$(az iot hub device-identity show-connection-string --device-id <REPLACE_WITH_DEVICE-NAME> --hub-name <REPLACE-WITH-HUB-NAME> -o tsv) \
    --parameters authenticationType='sshPublicKey' \
    --parameters adminPasswordOrKey="$(< ~/.ssh/iotedge-vm-key.pub)"
     
    ```

1. 验证部署是否成功完成。  虚拟机资源应已部署到选定的资源组中。  请注意机器名称，这应该是格式`vm-0000000000000`。 此外，请注意关联的 DNS**名称**，该名称应采用 格式`<dnsLabelPrefix>`。`<location>`.cloudapp.azure.com.

    **DNS 名称**可以从上一步的 JSON 格式输出中获取，**该输出部分**作为**公共 SSH**条目的一部分。  此条目的值可用于 SSH 到新部署的计算机。

    ```bash
    "outputs": {
      "public SSH": {
        "type": "String",
        "value": "ssh <adminUsername>@<DNS_Name>"
      }
    }
    ```

    **DNS 名称**也可以从 Azure 门户中新部署的虚拟机的 **"概述"** 部分获取。

    > [!div class="mx-imgBorder"]
    > [![显示 iotedge vm 的 dns 名称的屏幕截图](./media/how-to-install-iot-edge-ubuntuvm/iotedge-vm-dns-name.png)](./media/how-to-install-iot-edge-ubuntuvm/iotedge-vm-dns-name.png)

1. 如果要在设置后将 SSH 放入此 VM，请使用具有以下命令的关联**DNS 名称**：`ssh <adminUsername>@<DNS_Name>`

## <a name="next-steps"></a>后续步骤

预配 IoT Edge 设备和安装运行后，现在可以[部署 IoT Edge 模块](how-to-deploy-modules-portal.md)。

如果无法正确安装 IoT Edge 运行时，请参阅[故障排除](troubleshoot.md)页。

若要将现有安装更新到最新版本的 IoT Edge，请参阅[更新 IoT Edge 安全守护程序和运行时](how-to-update-iot-edge.md)。

如果要打开端口以通过 SSH 或其他入站连接访问 VM，请参阅 Azure 虚拟机文档，了解[打开 Linux VM 的端口和终结点](../virtual-machines/linux/nsg-quickstart.md)
