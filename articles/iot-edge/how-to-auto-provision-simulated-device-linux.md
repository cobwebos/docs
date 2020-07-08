---
title: 在 Linux VM 上使用虚拟 TPM 预配设备 - Azure IoT Edge
description: 使用 Linux VM 上的模拟 TPM 来测试 Azure IoT Edge 的 Azure 设备预配服务
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 6/30/2020
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: d6e60329f3c61811aa8a42528244b2883ff4b685
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/02/2020
ms.locfileid: "85834091"
---
# <a name="create-and-provision-an-iot-edge-device-with-a-tpm-on-linux"></a>使用 Linux 上的 TPM 创建和预配 IoT Edge 设备

本文介绍如何使用受信任的平台模块（TPM）在 Linux IoT Edge 设备上测试自动预配。 你可以自动设置[设备预配服务](../iot-dps/index.yml)Azure IoT Edge 设备。 如果你不熟悉自动预配过程，请在继续操作之前查看[自动预配的概念](../iot-dps/concepts-auto-provisioning.md)。

这些任务如下所示：

1. 使用用于确保硬件安全性的模拟受信任平台模块 (TPM) 在 Hyper-V 中创建 Linux 虚拟机 (VM)。
1. 创建 IoT 中心设备预配服务 (DPS) 的实例。
1. 为设备创建个人注册。
1. 安装 IoT Edge 运行时并将设备连接到 IoT 中心。

> [!TIP]
> 本文介绍了如何使用 TPM 模拟器测试 DPS 预配，但其中的大部分内容都适用于物理 TPM 硬件，例如 [Infineon OPTIGA&trade; TPM](https://catalog.azureiotsolutions.com/details?title=OPTIGA-TPM-SLB-9670-Iridium-Board)（一款 Azure IoT 认证设备）。
>
> 如果使用的是物理设备，则可以跳至本文的[从物理设备中检索预配信息](#retrieve-provisioning-information-from-a-physical-device)部分。

## <a name="prerequisites"></a>先决条件

* [已启用 Hyper-V](https://docs.microsoft.com/virtualization/hyper-v-on-windows/quick-start/enable-hyper-v) 的 Windows 开发计算机。 本文使用运行 Ubuntu Server VM 的 Windows 10。
* 活动的 IoT 中心。

> [!NOTE]
> 将 TPM 证明与 DPS 一起使用时，TPM 2.0 是必需的，并且只能用于创建个人（而非组）注册。

## <a name="create-a-linux-virtual-machine-with-a-virtual-tpm"></a>创建包含虚拟 TPM 的 Linux 虚拟机

在本部分，我们将在 Hyper-V 上创建新的 Linux 虚拟机。 使用模拟的 TPM 配置此虚拟机，以测试自动预配如何与 IoT Edge 一起工作。

### <a name="create-a-virtual-switch"></a>创建虚拟交换机

使用虚拟交换机可将虚拟机连接到物理网络。

1. 在 Windows 计算机上打开 Hyper-V 管理器。

2. 在“操作”菜单中，选择“虚拟交换机管理器”。 

3. 选择一个“外部”虚拟交换机，然后选择“创建虚拟交换机”。 

4. 为新的虚拟交换机命名，例如 **EdgeSwitch**。 确保将连接类型设置为“外部网络”，然后选择“确定”。 

5. 此时会弹出一条警告，指出网络连接可能会中断。 选择“是”继续。

如果创建新虚拟交换机时出现错误，请确保没有其他任何交换机正在使用以太网适配器，并且没有其他任何交换机使用相同的名称。

### <a name="create-virtual-machine"></a>创建虚拟机

1. 下载虚拟机使用的磁盘映像文件，并将其保存在本地。 例如， [Ubuntu server 18.04](http://releases.ubuntu.com/18.04.4/)。 有关 IoT Edge 设备支持的操作系统的信息，请参阅[Azure IoT Edge 支持的系统](support.md)。

2. 在 hyper-v 管理器中，在**Action**  >  **New**  >  "**操作**" 菜单中选择 "操作" "新建**虚拟机**"。

3. 使用以下特定配置完成“新建虚拟机向导”：

   1. **指定代系**：选择“第 2 代”。 第 2 代虚拟机已启用嵌套虚拟化，在虚拟机上运行 IoT Edge 必须启用此功能。
   2. **配置网络**：设置“连接”的值设置为在上一部分创建的虚拟交换机。
   3. **安装选项**：选择“从可启动映像文件安装操作系统”，并浏览到本地保存的磁盘映像文件。

4. 在向导中选择“完成”以创建虚拟机。

创建新的 VM 可能需要几分钟。

### <a name="enable-virtual-tpm"></a>启用虚拟 TPM

创建 VM 后，打开其设置以启用允许你自动预配设备的虚拟受信任平台模块 (TPM)。

1. 在 "Hyper-v 管理器" 中，右键单击 VM，然后选择 "**设置**"。

2. 导航到“安全性”。

3. 取消选中“启用安全启动”。

4. 选中“启用受信任的平台模块”。

5. 单击 **“确定”** 。  

### <a name="start-the-virtual-machine-and-collect-tpm-data"></a>启动虚拟机并收集 TPM 数据

在虚拟机中，生成一个可用于检索设备“注册 ID”和“认可密钥”的工具。 

1. 在 Hyper-v 管理器中，启动 VM 并连接到该 VM。

1. 遵照虚拟机中的提示完成安装过程，然后重新启动虚拟机。

1. 登录到 VM，然后遵循[设置 Linux 开发环境](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/devbox_setup.md#linux)中的步骤安装并生成适用于 C 的 Azure IoT 设备 SDK。

   >[!TIP]
   >在本文中，你将复制并粘贴到虚拟机上，这并不是通过 Hyper-v 管理器连接应用程序来实现的。 你可能想要通过 Hyper-v 管理器连接到虚拟机一次以检索其 IP 地址。 首先运行 `sudo apt install net-tools` ，然后执行 `hostname -I` 。 然后，可以使用该 IP 地址通过 SSH 进行连接：`ssh <username>@<ipaddress>`。

1. 运行以下命令以生成 SDK 工具，该工具可从 TPM 检索设备预配信息。

   ```bash
   cd azure-iot-sdk-c/cmake
   cmake -Duse_prov_client:BOOL=ON ..
   cd provisioning_client/tools/tpm_device_provision
   make
   sudo ./tpm_device_provision
   ```

1. "输出" 窗口将显示设备的 "**注册 ID** " 和 "**认可密钥**"。 复制这些值，以便以后在为设备创建单个注册时使用。

获得注册 ID 和认可密钥后，请继续[设置 IoT 中心设备预配服务](#set-up-the-iot-hub-device-provisioning-service)部分

## <a name="retrieve-provisioning-information-from-a-physical-device"></a>从物理设备检索预配信息

如果你使用的是物理 IoT Edge 设备而不是 VM，请构建一个可用于检索设备的设置信息的工具。

1. 按照[设置 Linux 开发环境](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/devbox_setup.md#linux)中的步骤安装并生成适用于 C 的 Azure IoT 设备 SDK。

1. 运行以下命令，生成用于从 TPM 设备检索设备预配信息的 SDK 工具。

   ```bash
   cd azure-iot-sdk-c/cmake
   cmake -Duse_prov_client:BOOL=ON ..
   cd provisioning_client/tools/tpm_device_provision
   make
   sudo ./tpm_device_provision
   ```

1. 复制“注册 ID”和“认可密钥”的值。  稍后要使用这些值在 DPS 中为设备创建个人注册。

## <a name="set-up-the-iot-hub-device-provisioning-service"></a>设置 IoT 中心设备预配服务

在 Azure 中创建 IoT 中心设备预配服务的新实例，并将其链接到 IoT 中心。 可以遵照[设置 IoT 中心 DPS](../iot-dps/quick-setup-auto-provision.md) 中的说明操作。

运行设备预配服务后，从概述页复制“ID 范围”的值。 配置 IoT Edge 运行时时，需要使用此值。

## <a name="create-a-dps-enrollment"></a>创建 DPS 注册

从虚拟机中检索预配信息，并使用该信息在设备预配服务中创建个人注册。

在 DPS 中创建注册时，可以声明“初始设备孪生状态”。 在设备孪生中可以设置标记，以便按解决方案中所需的任何指标（例如区域、环境、位置或设备类型）将设备分组。 这些标记用于创建[自动部署](how-to-deploy-at-scale.md)。

> [!TIP]
> 在 Azure CLI 中，可以创建一个[注册](https://docs.microsoft.com/cli/azure/ext/azure-iot/iot/dps/enrollment)，并使用**启用了边缘**的标志来指定设备是 IoT Edge 设备。

1. 在 [Azure 门户](https://portal.azure.com)中，导航到 IoT 中心设备预配服务的实例。

2. 在“设置”下，选择“管理注册”。 

3. 选择“添加个人注册”，然后完成以下步骤以配置注册：  

   1. 对于“机制”，请选择“TPM”。 

   2. 提供从虚拟机中复制的“认可密钥”和“注册 ID”。 

      > [!TIP]
      > 如果使用的是物理 TPM 设备，则需要确定**认可密钥**，该密钥对于每个 TPM 芯片都是唯一的，并且可以从与之关联的 TPM 芯片制造商处获得。 例如，可以通过创建认可密钥的 SHA-256 哈希来为 TPM 设备派生唯一的**注册 ID**。

   3. 根据需要，为设备提供一个 ID。 如果未提供设备 ID，则会使用注册 ID。

   4. 选择“True”，以声明此虚拟机是 IoT Edge 设备。

   5. 选择要将设备连接到的链接 IoT 中心，或选择 "**链接到新的 Iot 中心**"。 你可以选择多个中心，并根据所选的分配策略将设备分配给其中的一个中心。

   6. 根据需要，将标记值添加到“初始设备孪生状态”。 可以使用标记将设备组指定为模块部署的目标。 有关详细信息，请参阅[大规模部署 IoT Edge 模块](how-to-deploy-at-scale.md)。

   7. 选择“保存” 。

既然此设备已存在注册，IoT Edge 运行时在安装期间可以自动预配设备。

## <a name="install-the-iot-edge-runtime"></a>安装 IoT Edge 运行时

IoT Edge 运行时部署在所有 IoT Edge 设备上。 该运行时的组件在容器中运行，允许你将其他容器部署到设备，以便在边缘上运行代码。 在虚拟机上安装 IoT Edge 运行时。

在开始学习本文之前，请了解与设备类型匹配的 DPS“ID 范围”和设备“注册 ID”。  如果已安装示例 Ubuntu 服务器，请使用 **x64** 说明。 确保将 IoT Edge 运行时配置为自动预配而不是手动预配。

在转到配置安全守护程序的步骤时，请确保选择 "[选项 2" "自动预配](how-to-install-iot-edge-linux.md#option-2-automatic-provisioning)"，并配置 TPM 证明。

[在 Linux 上安装 Azure IoT Edge 运行时](how-to-install-iot-edge-linux.md)

## <a name="give-iot-edge-access-to-the-tpm"></a>向 IoT Edge 授予 TPM 的访问权限

IoT Edge 运行时需要访问 TPM 才能自动预配你的设备。

通过覆盖系统设置可以授予 IoT Edge 运行时对 TPM 的访问权限，以便 `iotedge` 服务获得根特权。 如果不想提升服务权限，也可以使用以下步骤手动提供 TPM 访问权限。

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
   KERNEL=="tpm0", SUBSYSTEM=="tpm", OWNER="iotedge", MODE="0600"
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

   成功的输出如下所示：

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

可以验证是否使用了在设备预配服务中创建的个人注册。 在 Azure 门户中导航到设备预配服务实例。 打开创建的个人注册的注册详细信息。 注意注册状态是否为“已分配”并且设备 ID 已列出。

## <a name="next-steps"></a>后续步骤

通过 DPS 注册过程，你可以在预配新设备时设置设备 ID 和设备克隆标记。 可以在自动设备管理中，使用这些值将单个设备或设备组指定为目标。 了解如何[使用 Azure 门户大规模部署和监视 IoT Edge 模块](how-to-deploy-at-scale.md)，或[使用 Azure CLI](how-to-deploy-cli-at-scale.md) 执行此操作。
