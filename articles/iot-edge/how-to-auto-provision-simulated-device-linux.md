---
title: 使用 Linux VM 上的虚拟 TPM 预配设备-Azure IoT Edge
description: 使用 Linux VM 上的模拟 TPM 来测试 Azure IoT Edge 的 Azure 设备预配服务
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 03/01/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 6bb1282212ccff45f179b8750e3ed8aec27d129e
ms.sourcegitcommit: 38b11501526a7997cfe1c7980d57e772b1f3169b
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/22/2020
ms.locfileid: "76511053"
---
# <a name="create-and-provision-an-iot-edge-device-with-a-virtual-tpm-on-a-linux-virtual-machine"></a>使用 Linux 虚拟机上的虚拟 TPM 创建和预配 IoT Edge 设备

可以使用[设备预配服务](../iot-dps/index.yml)自动预配 Azure IoT Edge 设备。 如果你不熟悉自动预配过程，请在继续操作之前查看[自动预配的概念](../iot-dps/concepts-auto-provisioning.md)。

本文说明如何使用以下步骤测试模拟 IoT Edge 设备上的自动预配：

* 使用用于确保硬件安全性的模拟受信任平台模块 (TPM) 在 Hyper-V 中创建 Linux 虚拟机 (VM)。
* 创建 IoT 中心设备预配服务 (DPS) 的实例。
* 为设备创建个人注册
* 安装 IoT Edge 运行时并将设备连接到 IoT 中心

> [!TIP]
> 本文介绍如何使用 TPM 模拟器测试 DPS 预配，但其中的许多功能适用于物理 TPM 硬件，如[INFINEON OPTIGA&trade; TPM](https://catalog.azureiotsolutions.com/details?title=OPTIGA-TPM-SLB-9670-Iridium-Board)、Azure IoT 认证设备。
>
> 如果你使用的是物理设备，则可以跳转到本文的[从物理设备检索设置信息](#retrieve-provisioning-information-from-a-physical-device)部分。

## <a name="prerequisites"></a>必备组件

* [已启用 Hyper-V](https://docs.microsoft.com/virtualization/hyper-v-on-windows/quick-start/enable-hyper-v) 的 Windows 开发计算机。 本文使用运行 Ubuntu Server VM 的 Windows 10。
* 活动的 IoT 中心。
* 如果使用模拟的 TPM，则[Visual Studio](https://visualstudio.microsoft.com/vs/) 2015 或更高版本启用了[" C++桌面开发](https://www.visualstudio.com/vs/support/selecting-workloads-visual-studio-2017/)" 工作负荷。

> [!NOTE]
> 将 TPM 证明与 DPS 结合使用时，TPM 2.0 是必需的，并且只能用于创建个人、非组、注册。

## <a name="create-a-linux-virtual-machine-with-a-virtual-tpm"></a>创建包含虚拟 TPM 的 Linux 虚拟机

在本部分中，将在 Hyper-v 上创建新的 Linux 虚拟机。 你使用模拟的 TPM 配置了此虚拟机，以便你可以使用它来测试自动预配 IoT Edge 的工作方式。

### <a name="create-a-virtual-switch"></a>创建虚拟交换机

使用虚拟交换机可将虚拟机连接到物理网络。

1. 在 Windows 计算机上打开 Hyper-v 管理器。

2. 在“操作”菜单中，选择“虚拟交换机管理器”。

3. 选择一个“外部”虚拟交换机，然后选择“创建虚拟交换机”。

4. 为新的虚拟交换机命名，例如 **EdgeSwitch**。 确保将连接类型设置为“外部网络”，然后选择“确定”。

5. 此时会弹出一条警告，指出网络连接可能会中断。 选择“是”继续。

如果创建新虚拟交换机时出现错误，请确保没有其他任何交换机正在使用以太网适配器，并且没有其他任何交换机使用相同的名称。

### <a name="create-virtual-machine"></a>创建虚拟机

1. 下载虚拟机使用的磁盘映像文件，并将其保存在本地。 例如 [Ubuntu 服务器](https://www.ubuntu.com/download/server)。

2. 在 Hyper-v 管理器中，在 "**操作**" 菜单中选择 "**新建**" > **虚拟机**"。

3. 使用以下特定配置完成“新建虚拟机向导”：

   1. **指定代次**：选择“第 2 代”。 第2代虚拟机启用了嵌套虚拟化，这是在虚拟机上运行 IoT Edge 所必需的。
   2. **配置网络**：设置“连接”的值设置为在上一部分创建的虚拟交换机。
   3. **安装选项**：选择“从可启动映像文件安装操作系统”，并浏览到本地保存的磁盘映像文件。

4. 在向导中选择 "**完成**" 以创建虚拟机。

创建新的 VM 可能需要几分钟。

### <a name="enable-virtual-tpm"></a>启用虚拟 TPM

创建 VM 后，打开其设置以启用可自动预配设备的虚拟受信任的平台模块（TPM）。

1. 选择虚拟机，然后打开其**设置**。

2. 导航到“安全性”。

3. 取消选中“启用安全启动”。

4. 选中“启用受信任的平台模块”。

5. 单击“确定”。  

### <a name="start-the-virtual-machine-and-collect-tpm-data"></a>启动虚拟机并收集 TPM 数据

在虚拟机中，生成一个可用于检索设备**注册 ID**和**认可密钥**的工具。

1. 启动虚拟机并连接到该虚拟机。

1. 按照虚拟机中的提示完成安装过程，然后重新启动计算机。

1. 登录到 VM，然后按照[设置 Linux 开发环境](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/devbox_setup.md#linux)中的步骤安装和生成适用于 C 语言的 Azure IOT 设备 SDK。

   >[!TIP]
   >在本文中，你将从虚拟机复制并粘贴，这并不是通过 Hyper-v 管理器连接应用程序来实现的。 你可能想要通过 Hyper-v 管理器连接到虚拟机一次以检索其 IP 地址： `ifconfig`。 然后，可以使用 IP 地址通过 SSH： `ssh <username>@<ipaddress>`进行连接。

1. 运行以下命令以生成 SDK 工具，该工具可从 TPM 模拟器检索设备设置信息。

   ```bash
   cd azure-iot-sdk-c/cmake
   cmake -Duse_prov_client:BOOL=ON -Duse_tpm_simulator:BOOL=ON ..
   cd provisioning_client/tools/tpm_device_provision
   make
   sudo ./tpm_device_provision
   ```

1. 在命令窗口中，导航到 `azure-iot-sdk-c` 目录，然后运行 TPM 模拟器。 该模拟器通过套接字在端口 2321 和 2322 上进行侦听。 请勿关闭此命令窗口;需要将此模拟器保持运行状态。

   从 `azure-iot-sdk-c` 目录中运行以下命令以启动模拟器：

   ```bash
   ./provisioning_client/deps/utpm/tools/tpm_simulator/Simulator.exe
   ```

1. 使用 Visual Studio 打开在名为 `azure_iot_sdks.sln`的 `cmake` 目录中生成的解决方案，然后使用 "**生成**" 菜单上的 "**生成解决方案**" 命令生成该解决方案。

1. **在 Visual Studio 的** “解决方案资源管理器”窗格中，导航到 **Provision\_Tools** 文件夹。 右键单击“tpm_device_provision”项目，然后选择“设为启动项目”。

1. 使用 "**调试**" 菜单上的任一 "**启动**" 命令运行解决方案。 "输出" 窗口将显示 TPM 模拟器的 "**注册 ID** " 和 "**认可密钥**"，你应该在以后为设备创建单个注册时将其复制以供以后使用。你可以关闭此窗口（使用注册 ID 和认可密钥），但让 TPM 模拟器窗口保持运行。

## <a name="retrieve-provisioning-information-from-a-physical-device"></a>从物理设备检索预配信息

在设备上，生成一个可用于检索设备的设置信息的工具。

1. 按照[设置 Linux 开发环境](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/devbox_setup.md#linux)中的步骤安装和生成适用于 C 语言的 Azure IOT 设备 SDK。

1. 运行以下命令以生成 SDK 工具，该工具可从 TPM 设备检索设备设置信息。

   ```bash
   cd azure-iot-sdk-c/cmake
   cmake -Duse_prov_client:BOOL=ON ..
   cd provisioning_client/tools/tpm_device_provision
   make
   sudo ./tpm_device_provision
   ```

1. 复制 "**注册 ID** " 和 "**认可密钥**" 的值。 稍后要使用这些值在 DPS 中为设备创建个人注册。

## <a name="set-up-the-iot-hub-device-provisioning-service"></a>设置 IoT 中心设备预配服务

在 Azure 中创建 IoT 中心设备预配服务的新实例，并将其链接到 IoT 中心。 可以遵照[设置 IoT 中心 DPS](../iot-dps/quick-setup-auto-provision.md) 中的说明操作。

运行设备预配服务后，从概述页复制“ID 范围”的值。 配置 IoT Edge 运行时时，需要使用此值。

## <a name="create-a-dps-enrollment"></a>创建 DPS 注册

从虚拟机中检索预配信息，并使用该信息在设备预配服务中创建个人注册。

在 DPS 中创建注册时，可以声明“初始设备孪生状态”。 在设备孪生中可以设置标记，以便按解决方案中所需的任何指标（例如区域、环境、位置或设备类型）将设备分组。 这些标记用于创建[自动部署](how-to-deploy-monitor.md)。

1. 在[Azure 门户](https://portal.azure.com)中，导航到 IoT 中心设备预配服务的实例。

2. 在“设置”下，选择“管理注册”。

3. 选择“添加个人注册”，然后完成以下步骤以配置注册：  

   1. 对于“机制”，请选择“TPM”。

   2. 提供从虚拟机复制的**认可密钥**和**注册 ID** 。

      > [!TIP]
      > 如果你使用的是物理 TPM 设备，则需要确定**签署密钥**，该密钥对于每个 tpm 芯片都是唯一的，并从与其关联的 tpm 芯片制造商那里获得。 例如，你可以通过创建认可密钥的 256 SHA-1 哈希，为你的 TPM 设备派生唯一的**注册 ID** 。

   3. 选择 " **True** " 可将此虚拟机声明为 IoT Edge 设备。

   4. 选择要将设备连接到的链接“IoT 中心”。 你可以选择多个中心，并根据所选分配策略将设备分配给其中一个中心。

   5. 根据需要，为设备提供一个 ID。 可以使用设备 ID 将单个设备指定为模块部署的目标。 如果未提供设备 ID，则使用注册 ID。

   6. 根据需要，将标记值添加到“初始设备孪生状态”。 可以使用标记将设备组指定为模块部署的目标。 例如：

      ```json
      {
         "tags": {
            "environment": "test"
         },
         "properties": {
            "desired": {}
         }
      }
      ```

   7. 选择“保存”。

现在此设备已经有注册，IoT Edge 运行时可以在安装过程中自动设置设备。

## <a name="install-the-iot-edge-runtime"></a>安装 IoT Edge 运行时

IoT Edge 运行时部署在所有 IoT Edge 设备上。 该运行时的组件在容器中运行，允许你将其他容器部署到设备，以便在边缘上运行代码。 在虚拟机上安装 IoT Edge 运行时。

在开始学习本文之前，请了解与设备类型匹配的 DPS“ID 范围”和设备“注册 ID”。 如果已安装示例 Ubuntu 服务器，请使用 **x64** 说明。 确保将 IoT Edge 运行时配置为自动预配而不是手动预配。

[在 Linux 上安装 Azure IoT Edge 运行时](how-to-install-iot-edge-linux.md)

## <a name="give-iot-edge-access-to-the-tpm"></a>向 IoT Edge 授予 TPM 的访问权限

IoT Edge 运行时需要有权访问 TPM 才能自动预配设备。

通过覆盖系统设置可以授予 IoT Edge 运行时对 TPM 的访问权限，以便 iotedge 服务获得根特权。 如果不想提升服务权限，也可以使用以下步骤手动提供 TPM 访问权限。

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
   crw-rw---- 1 root iotedge 10, 224 Jul 20 16:27 /dev/tpm0
   ```

   如果未看到应用了正确的权限，请尝试重新启动计算机来刷新 udev。

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

如果运行时成功启动，则可以转到 IoT 中心，查看新设备是否自动预配。 现在，设备已准备好运行 IoT Edge 模块。

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

你可以验证你在设备预配服务中创建的单个注册是否已使用。 在 Azure 门户中导航到设备预配服务实例。 打开所创建的单个注册的 "注册详细信息"。 请注意，将**分配**注册的状态并列出设备 ID。

## <a name="next-steps"></a>后续步骤

使用设备预配服务注册过程可以在预配新设备的同时，设置设备 ID 和设备孪生标记。 可以在自动设备管理中，使用这些值将单个设备或设备组指定为目标。 了解如何[使用 Azure 门户大规模部署和监视 IoT Edge 模块](how-to-deploy-monitor.md)，或[使用 Azure CLI](how-to-deploy-monitor-cli.md) 执行此操作。
