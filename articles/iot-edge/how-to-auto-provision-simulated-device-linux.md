---
title: 使用 DPS 自动预配 Azure IoT Edge 设备 - Linux | Microsoft Docs
description: 使用 Linux VM 上的模拟 TPM 来测试 Azure IoT Edge 的设备预配
author: kgremban
manager: timlt
ms.author: kgremban
ms.date: 06/27/2018
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 9609aab6c70bc0c2755de142023bd26e7417987a
ms.sourcegitcommit: 756f866be058a8223332d91c86139eb7edea80cc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/02/2018
ms.locfileid: "37347593"
---
# <a name="create-and-provision-an-edge-device-with-a-virtual-tpm-on-a-linux-virtual-machine"></a>使用 Linux 虚拟机上的虚拟 TPM 创建和预配 Edge 设备

可以使用[设备预配服务](../iot-dps/index.yml)自动预配 Azure IoT Edge 设备，就像预配未启用 Edge 的设备一样。 如果你不熟悉自动预配过程，请在继续操作之前查看[自动预配的概念](../iot-dps/concepts-auto-provisioning.md)。 

本文介绍如何使用以下步骤，在模拟的 Edge 设备上测试自动预配： 

* 使用用于确保硬件安全性的模拟受信任平台模块 (TPM) 在 Hyper-V 中创建 Linux 虚拟机 (VM)。
* 创建 IoT 中心设备预配服务 (DPS) 的实例。
* 为设备创建个人注册
* 安装 IoT Edge 运行时并将设备连接到 IoT 中心

本文中的步骤仅用于测试目的。

## <a name="prerequisites"></a>先决条件

* [已启用 Hyper-V](https://docs.microsoft.com/virtualization/hyper-v-on-windows/quick-start/enable-hyper-v) 的 Windows 开发计算机。 本文使用运行 Ubuntu Server VM 的 Windows 10。 
* 活动的 IoT 中心。 

## <a name="create-a-linux-virtual-machine-with-a-virtual-tpm"></a>创建包含虚拟 TPM 的 Linux 虚拟机

在本部分，我们将在 Hyper-V 中创建一个包含模拟 TPM 的新 Linux 虚拟机，以便可以使用它来测试如何在 IoT Edge 中使用自动预配。 

### <a name="create-a-virtual-switch"></a>创建虚拟交换机

使用虚拟交换机可将虚拟机连接到物理网络。

1. 在 Windows 计算机上打开 Hyper-V。 

2. 在“操作”菜单中，选择“虚拟交换机管理器”。 

3. 选择一个“外部”虚拟交换机，然后选择“创建虚拟交换机”。 

4. 为新的虚拟交换机命名，例如 **EdgeSwitch**。 确保将连接类型设置为“外部网络”，然后选择“确定”。

5. 此时会弹出一条警告，指出网络连接可能会中断。 选择“是”继续。 

如果创建新虚拟交换机时出现错误，请确保没有其他任何交换机正在使用以太网适配器，并且没有其他任何交换机使用相同的名称。 

### <a name="create-virtual-machine"></a>创建虚拟机

1. 下载虚拟机使用的磁盘映像文件，并将其保存在本地。 例如 [Ubuntu 服务器](https://www.ubuntu.com/download/server)。 

2. 再次打开 Hyper-V。 在“操作”菜单中，选择“新建” > “虚拟机”。

3. 使用以下特定配置完成“新建虚拟机向导”：

   1. **指定代次**：选择“第 2 代”。
   2. **配置网络**：设置“连接”的值设置为在上一部分创建的虚拟交换机。 
   3. **安装选项**：选择“从可启动映像文件安装操作系统”，并浏览到本地保存的磁盘映像文件。

创建新 VM 可能需要几分钟时间。 

### <a name="enable-virtual-tpm"></a>启用虚拟 TPM

1. 创建 VM 后，打开其设置。 
2. 导航到“安全性”。 
3. 取消选中“启用安全启动”。
4. 选中“启用受信任的平台模块”。 
5. 单击“确定”。  

### <a name="start-the-virtual-machine-and-collect-tpm-data"></a>启动虚拟机并收集 TPM 数据

在虚拟机中，生成一个可用于检索设备“注册 ID”和“认可密钥”的 C SDK 工具。 

1. 启动并连接到 VM，以完成安装过程。 

2. 在 VM 中，遵循[设置 Linux 开发环境](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/devbox_setup.md#linux)中的步骤安装并生成适用于 C 的 Azure IoT 设备 SDK。 

3. 运行以下命令，生成用于检索设备预配信息的 C SDK 工具。 

   ```bash
   cd azure-iot-sdk-c/cmake
   cmake -Duse_prov_client:BOOL=ON ..
   cd provisioning_client/tools/tpm_device_provision
   make
   sudo ./tpm_device_provision
   ```

3. 复制“注册 ID”和“认可密钥”的值。 稍后要使用这些值在 DPS 中为设备创建个人注册。 

## <a name="set-up-the-iot-hub-device-provisioning-service"></a>设置 IoT 中心设备预配服务

在 Azure 中创建 IoT 中心设备预配服务的新实例，并将其链接到 IoT 中心。 可以遵照[设置 IoT 中心 DPS](../iot-dps/quick-setup-auto-provision.md) 中的说明操作。

运行设备预配服务后，从概述页复制“ID 范围”的值。 配置 IoT Edge 运行时时，需要使用此值。 

## <a name="create-a-dps-enrollment"></a>创建 DPS 注册

从虚拟机中检索预配信息，并使用该信息在设备预配服务中创建个人注册。 

在 DPS 中创建注册时，可以声明“初始设备孪生状态”。 在设备孪生中可以设置标记，以便按解决方案中所需的任何指标（例如区域、环境、位置或设备类型）将设备分组。 这些标记用于创建[自动部署](how-to-deploy-monitor.md)。 


1. 在 [Azure 门户](https://portal.azure.com)中，导航到 IoT 中心设备预配服务的实例。 

2. 在“设置”下，选择“管理注册”。 

3. 选择“添加个人注册”，然后完成以下步骤以配置注册：  

   1. 对于“机制”，请选择“TPM”。 
   2. 插入从虚拟机中复制的“认可密钥”和“注册 ID”。
   3. 选择“启用”，声明此虚拟机是 IoT Edge 设备。 
   4. 选择要将设备连接到的链接“IoT 中心”。 
   5. 根据需要，为设备提供一个 ID。 可以使用设备 ID 将单个设备指定为模块部署的目标。 
   6. 根据需要，将标记值添加到“初始设备孪生状态”。 可以使用标记将设备组指定为模块部署的目标。 
   7. 选择“保存”。 


## <a name="install-the-iot-edge-runtime"></a>安装 IoT Edge 运行时

IoT Edge 运行时部署在所有 IoT Edge 设备上。 该运行时的组件在容器中运行，允许你将其他容器部署到设备，以便在边缘上运行代码。 在虚拟机上安装 IoT Edge 运行时。 

在开始学习本文之前，请了解与设备类型匹配的 DPS“ID 范围”和设备“注册 ID”。 如果已安装示例 Ubuntu 服务器，请使用 **x64** 说明。 确保将 IoT Edge 运行时配置为自动预配而不是手动预配。 

* [Linux (x64)](how-to-install-iot-edge-linux.md)
* [Linux (ARM32v7/armhf)](how-to-install-iot-edge-linux-arm.md)

## <a name="give-iot-edge-access-to-the-tpm"></a>向 IoT Edge 授予 TPM 的访问权限

IoT Edge 运行时需要有权访问 TPM 才能自动预配设备。 

使用以下步骤授予 TPM 访问权限。 或者，可以重写 systemd 设置，使 *iotedge* 服务能够以 root 身份运行，以此实现相同的目的。 

1. 在设备上找到 TPM 硬件模块的文件路径，并将其保存为本地变量。 

   ```bash
   tpm=$(sudo find /sys -name dev -print | fgrep tpm | sed 's/.\{4\}$//')
   ```

2. 创建一条新规则，用于向 IoT Edge 运行时授予 tpm0 的访问权限。 

   ```bash
   sudo touch /etc/udev/rules.d/tpmaccess.rules
   ```

3. 打开 rules 文件。 

   ```bash
   sudo nano /etc/udev/rules.d/tpmaccess.rules
   ```

4. 将以下访问信息复制到 rules 文件。 

   ```input 
   # allow iotedge access to tpm0
   KERNEL=="tpm0", SUBSYSTEM=="tpm", GROUP="iotedge", MODE="0660"
   ```

5. 保存并退出该文件。 

6. 触发 udev 系统来评估新规则。 

   ```bash
   /bin/udevadm trigger $tpm
   ```

7. 验证是否已成功应用该规则。

   ```bash
   ls -l /dev/tpm0
   ```

   如果成功应用，输出将如下所示：

   ```output
   crw------- 1 root root 10, 224 Jun 28 22:34 /dev/tpm0
   ```

8. 打开 IoT Edge 运行时 overrides 文件。 

   ```bash
   sudo systemctl edit iotedge.service
   ```

9. 添加以下代码以建立 TPM 环境变量。

   ```input
   [Service]
   Environment=IOTEDGE_USE_TPM_DEVICE=ON
   ```

9. 验证重写是否成功。

   ```bash
   sudo systemctl cat iotedge.service
   ```

   如果重写成功，输出将显示 **iotedge** 默认服务变量，然后显示 **override.conf** 中设置的环境变量。 

12. 重载设置。

   ```bash
   sudo systemctl daemon-reload
   ```

## <a name="restart-the-iot-edge-runtime"></a>重启 IoT Edge 运行时

重启 IoT Edge 运行时，使之拾取你在设备上所做的所有配置更改。 

   ```bash
   sudo systemctl restart iotedge
   ```

检查 IoT Edge 运行时是否正在运行。 

   ```bash
   sudo systemctl status iotedge
   ```

如果出现预配错误，可能表示配置更改尚未生效。 请尝试再次重启 IoT Edge 守护程序。 

   ```bash
   sudo systemctl daemon-reload
   ```
   
或者，请尝试重启虚拟机，以确定重新启动后更改是否生效。 

## <a name="verify-successful-installation"></a>验证是否成功安装

如果成功启动了运行时，则可以转到 IoT 中心，查看新设备是否已自动预配并已准备好运行 IoT Edge 模块。 

检查 IoT Edge 守护程序的状态。

```cmd/sh
systemctl status iotedge
```

检查守护程序日志。

```cmd/sh
journalctl -u iotedge --no-pager --no-full
```

列出正在运行的模块。

```cmd/sh
iotedge list
```


## <a name="next-steps"></a>后续步骤

使用设备预配服务注册过程可以在预配新设备的同时，设置设备 ID 和设备孪生标记。 可以在自动设备管理中，使用这些值将单个设备或设备组指定为目标。 了解如何[使用 Azure 门户大规模部署和监视 IoT Edge 模块](how-to-deploy-monitor.md)，或[使用 Azure CLI](how-to-deploy-monitor-cli.md) 执行此操作
