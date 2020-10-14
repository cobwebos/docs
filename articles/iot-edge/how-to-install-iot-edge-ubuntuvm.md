---
title: 在 Ubuntu 虚拟机上运行 Azure IoT Edge | Microsoft Docs
description: Ubuntu 18.04 LTS 虚拟机的 Azure IoT Edge 设置说明
author: toolboc
manager: veyalla
ms.reviewer: kgremban
ms.service: iot-edge
services: iot-edge
ms.topic: conceptual
ms.date: 06/29/2020
ms.author: pdecarlo
ms.custom: devx-track-azurecli
ms.openlocfilehash: d14ef742c19556a9df2bd193a54bfaa1672d5964
ms.sourcegitcommit: 2e72661f4853cd42bb4f0b2ded4271b22dc10a52
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/14/2020
ms.locfileid: "92044218"
---
# <a name="run-azure-iot-edge-on-ubuntu-virtual-machines"></a>在 Ubuntu 虚拟机上运行 Azure IoT Edge

使用 Azure IoT Edge 运行时可将设备转变为 IoT Edge 设备。 该运行时可以部署在像 Raspberry Pi 一样小的设备上，也可以部署在像工业服务器一样大的设备上。 使用 IoT Edge 运行时配置设备后，即可开始从云中部署业务逻辑。

若要了解有关 IoT Edge 运行时如何工作以及包含哪些组件的详细信息，请参阅[了解 Azure IoT Edge 运行时及其体系结构](iot-edge-runtime.md)。

本文列出了部署一个使用预先提供的设备连接字符串安装并配置了 Azure IoT Edge 运行时的 Ubuntu 18.04 LTS 虚拟机的步骤。 部署是使用 [iotedge-vm-deploy](https://github.com/Azure/iotedge-vm-deploy) 项目存储库中维护的基于 [cloud-init](../virtual-machines/linux/using-cloud-init.md
) 的 [Azure 资源管理器模板](../azure-resource-manager/templates/overview.md)完成的。

首次启动时，该 Ubuntu 18.04 LTS 虚拟机会[通过 cloud-init 安装最新版本的 Azure IoT Edge 运行时](https://github.com/Azure/iotedge-vm-deploy/blob/master/cloud-init.txt)。 在运行时启动之前，该虚拟机还会设置提供的连接字符串，使你可以轻松配置和连接 IoT Edge 设备，而无需启动 SSH 或远程桌面会话。 

## <a name="deploy-using-deploy-to-azure-button"></a>使用“部署到 Azure”按钮进行部署

使用[“部署到 Azure”按钮](../azure-resource-manager/templates/deploy-to-azure-button.md)可以简化 GitHub 中维护的 [Azure 资源管理器模板](../azure-resource-manager/templates/overview.md)的部署。  本部分将演示 [iotedge-vm-deploy](https://github.com/Azure/iotedge-vm-deploy) 项目存储库中包含的“部署到 Azure”按钮的用法。  


1. 我们将使用 iotedge-vm-deploy Azure 资源管理器模板部署支持 Azure IoT Edge 的 Linux VM。  若要开始，请单击下面的按钮：

    [![iotedge-vm-deploy 的“部署到 Azure”按钮](https://aka.ms/deploytoazurebutton)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2Fazure%2Fiotedge-vm-deploy%2Fmaster%2FedgeDeploy.json)

1. 在新启动的窗口中，填写可用的窗体字段：

    > [!div class="mx-imgBorder"]
    > [![显示 iotedge-vm-deploy 模板的屏幕截图](./media/how-to-install-iot-edge-ubuntuvm/iotedge-vm-deploy.png)](./media/how-to-install-iot-edge-ubuntuvm/iotedge-vm-deploy.png)

    **订阅**：要将虚拟机部署到的活动 Azure 订阅。

    **资源组**：用于包含该虚拟机及其相关资源的现有或新建的资源组。

    **DNS 标签前缀**：必需的值，用作虚拟机主机名的前缀。

    **管理员用户名**：一个用户名，将为其提供对部署的 root 特权。

    **设备连接字符串**：在所需 [IoT 中心](../iot-hub/about-iot-hub.md)内创建的设备的[设备连接字符串](./how-to-manual-provision-symmetric-key.md)。

    **VM 大小**：要部署的虚拟机的[大小](../cloud-services/cloud-services-sizes-specs.md)

    **Ubuntu OS 版本**：要在基础虚拟机上安装的 Ubuntu OS 版本。

    **位置**：要将虚拟机部署到的[地理区域](https://azure.microsoft.com/global-infrastructure/locations/)，此值默认为所选资源组的位置。

    **身份验证类型**：根据偏好选择“sshPublicKey”或“password”。  

    **管理员密码或密钥**：SSH 公钥的值或密码的值，具体取决于所选的身份验证类型。

    填写所有字段后，选中页面底部的复选框以接受条款，然后选择“购买”开始部署。 

1. 验证部署是否已成功完成。  虚拟机资源应已部署到所选的资源组中。  请记下计算机名称，此名称应采用 `vm-0000000000000` 格式。 另外，请记下关联的“DNS 名称”，其格式应为 `<dnsLabelPrefix>`.`<location>`.cloudapp.azure.com。 

    可以从 Azure 门户中新部署的虚拟机的“概述”部分获取“DNS 名称”。  

    > [!div class="mx-imgBorder"]
    > [![显示 IoT Edge VM 的 DNS 名称的屏幕截图](./media/how-to-install-iot-edge-ubuntuvm/iotedge-vm-dns-name.png)](./media/how-to-install-iot-edge-ubuntuvm/iotedge-vm-dns-name.png)

1. 设置后若要通过 SSH 连接到此 VM，请在命令中使用关联的“DNS 名称”：`ssh <adminUsername>@<DNS_Name>`

## <a name="deploy-from-azure-cli"></a>从 Azure CLI 部署

1. 使用以下命令来确保已安装 Azure CLI IoT 扩展：
    ```azurecli-interactive
    az extension add --name azure-iot
    ```

1. 接下来，如果在桌面上使用 Azure CLI，请先登录：

   ```azurecli-interactive
   az login
   ```

1. 如果你有多个订阅，请选择要使用的订阅：
   1. 列出订阅：

      ```azurecli-interactive
      az account list --output table
      ```

   1. 复制要使用的订阅的 SubscriptionID 字段。

   1. 使用复制的 ID 设置工作订阅：

      ```azurecli-interactive
      az account set -s <SubscriptionId>
      ```

1. 创建新资源组（或者在后续步骤中指定现有的资源组）：

   ```azurecli-interactive
   az group create --name IoTEdgeResources --location westus2
   ```

1. 创建新虚拟机：

    若要为 authenticationType 使用 `password`，请参阅以下示例： 

   ```azurecli-interactive
   az deployment group create \
   --resource-group IoTEdgeResources \
   --template-uri "https://aka.ms/iotedge-vm-deploy" \
   --parameters dnsLabelPrefix='my-edge-vm1' \
   --parameters adminUsername='<REPLACE_WITH_USERNAME>' \
   --parameters deviceConnectionString=$(az iot hub device-identity connection-string show --device-id <REPLACE_WITH_DEVICE-NAME> --hub-name <REPLACE-WITH-HUB-NAME> -o tsv) \
   --parameters authenticationType='password' \
   --parameters adminPasswordOrKey="<REPLACE_WITH_SECRET_PASSWORD>"
   ```

    若要使用 SSH 密钥进行身份验证，可为 authenticationType 指定 `sshPublicKey`，然后在 adminPasswordOrKey 参数中提供 SSH 密钥的值。    下面显示了一个示例。

    ```azurecli-interactive
    #Generate the SSH Key
    ssh-keygen -m PEM -t rsa -b 4096 -q -f ~/.ssh/iotedge-vm-key -N ""  

    #Create a VM using the iotedge-vm-deploy script
    az deployment group create \
    --resource-group IoTEdgeResources \
    --template-uri "https://aka.ms/iotedge-vm-deploy" \
    --parameters dnsLabelPrefix='my-edge-vm1' \
    --parameters adminUsername='<REPLACE_WITH_USERNAME>' \
    --parameters deviceConnectionString=$(az iot hub device-identity connection-string show --device-id <REPLACE_WITH_DEVICE-NAME> --hub-name <REPLACE-WITH-HUB-NAME> -o tsv) \
    --parameters authenticationType='sshPublicKey' \
    --parameters adminPasswordOrKey="$(< ~/.ssh/iotedge-vm-key.pub)"
    ```

1. 验证部署是否已成功完成。  虚拟机资源应已部署到所选的资源组中。  请记下计算机名称，此名称应采用 `vm-0000000000000` 格式。 另外，请记下关联的“DNS 名称”，其格式应为 `<dnsLabelPrefix>`.`<location>`.cloudapp.azure.com。 

    可以从上一步骤的 JSON 格式输出中获取“DNS 名称”，它作为“公共 SSH”条目的一部分显示在 outputs 节中。     使用此条目的值可以通过 SSH 连接到新部署的计算机。

    ```bash
    "outputs": {
      "public SSH": {
        "type": "String",
        "value": "ssh <adminUsername>@<DNS_Name>"
      }
    }
    ```

    也可以从 Azure 门户中新部署的虚拟机的“概述”部分获取“DNS 名称”。  

    > [!div class="mx-imgBorder"]
    > [![显示 IoT Edge VM 的 DNS 名称的屏幕截图](./media/how-to-install-iot-edge-ubuntuvm/iotedge-vm-dns-name.png)](./media/how-to-install-iot-edge-ubuntuvm/iotedge-vm-dns-name.png)

1. 设置后若要通过 SSH 连接到此 VM，请在命令中使用关联的“DNS 名称”：`ssh <adminUsername>@<DNS_Name>`

## <a name="next-steps"></a>后续步骤

预配了安装运行时的 IoT Edge 设备后，现在可以[部署 IoT Edge 模块](how-to-deploy-modules-portal.md)。

如果无法正确安装 IoT Edge 运行时，请参阅[故障排除](troubleshoot.md)页。

若要将现有安装更新到最新版本的 IoT Edge，请参阅[更新 IoT Edge 安全守护程序和运行时](how-to-update-iot-edge.md)。

若要通过 SSH 或其他入站连接打开端口以访问 VM，请参阅有关[打开 Linux VM 的端口和终结点](../virtual-machines/linux/nsg-quickstart.md)的 Azure 虚拟机文档