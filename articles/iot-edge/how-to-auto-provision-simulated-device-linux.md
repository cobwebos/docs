---
title: Automatically provision Linux devices with DPS - Azure IoT Edge | Microsoft Docs
description: 使用 Linux VM 上的模拟 TPM 来测试 Azure IoT Edge 的 Azure 设备预配服务
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 03/01/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 292ae570d4f2ddd0c09e667860ee2ba01b9fc6b8
ms.sourcegitcommit: 12d902e78d6617f7e78c062bd9d47564b5ff2208
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/24/2019
ms.locfileid: "74457171"
---
# <a name="create-and-provision-an-iot-edge-device-with-a-virtual-tpm-on-a-linux-virtual-machine"></a>Create and provision an IoT Edge device with a virtual TPM on a Linux virtual machine

Azure IoT Edge devices can be automatically provisioned using the [Device Provisioning Service](../iot-dps/index.yml). 如果你不熟悉自动预配过程，请在继续操作之前查看[自动预配的概念](../iot-dps/concepts-auto-provisioning.md)。

This article shows you how to test auto-provisioning on a simulated IoT Edge device with the following steps:

* 使用用于确保硬件安全性的模拟受信任平台模块 (TPM) 在 Hyper-V 中创建 Linux 虚拟机 (VM)。
* 创建 IoT 中心设备预配服务 (DPS) 的实例。
* 为设备创建个人注册
* 安装 IoT Edge 运行时并将设备连接到 IoT 中心

> [!NOTE]
> TPM 2.0 is required when using TPM attestation with DPS and can only be used to create individual, not group, enrollments.

> [!TIP]
> This article describes how to test DPS provisioning using a TPM simulator, but much of it applies to physical TPM hardware such as the [Infineon OPTIGA&trade; TPM](https://catalog.azureiotsolutions.com/details?title=OPTIGA-TPM-SLB-9670-Iridium-Board), an Azure Certified for IoT device.
>
> If you're using a physical device, you can skip ahead to the [Retrieve provisioning information from a physical device](#retrieve-provisioning-information-from-a-physical-device) section in this article.

## <a name="prerequisites"></a>必备组件

* [已启用 Hyper-V](https://docs.microsoft.com/virtualization/hyper-v-on-windows/quick-start/enable-hyper-v) 的 Windows 开发计算机。 本文使用运行 Ubuntu Server VM 的 Windows 10。
* 活动的 IoT 中心。
* If using a simulated TPM, [Visual Studio](https://visualstudio.microsoft.com/vs/) 2015 or later with the ['Desktop development with C++'](https://www.visualstudio.com/vs/support/selecting-workloads-visual-studio-2017/) workload enabled.

## <a name="create-a-linux-virtual-machine-with-a-virtual-tpm"></a>创建包含虚拟 TPM 的 Linux 虚拟机

In this section, you create a new Linux virtual machine on Hyper-V. You configured this virtual machine with a simulated TPM so that you can use it for testing how automatic provisioning works with IoT Edge. 

### <a name="create-a-virtual-switch"></a>创建虚拟交换机

使用虚拟交换机可将虚拟机连接到物理网络。

1. Open Hyper-V Manager on your Windows machine. 

2. 在“操作”菜单中，选择“虚拟交换机管理器”。 

3. 选择一个“外部”虚拟交换机，然后选择“创建虚拟交换机”。 

4. 为新的虚拟交换机命名，例如 **EdgeSwitch**。 确保将连接类型设置为“外部网络”，然后选择“确定”。

5. 此时会弹出一条警告，指出网络连接可能会中断。 选择“是”继续。 

如果创建新虚拟交换机时出现错误，请确保没有其他任何交换机正在使用以太网适配器，并且没有其他任何交换机使用相同的名称。 

### <a name="create-virtual-machine"></a>创建虚拟机

1. 下载虚拟机使用的磁盘映像文件，并将其保存在本地。 例如 [Ubuntu 服务器](https://www.ubuntu.com/download/server)。 

2. In Hyper-V Manager again, select **New** > **Virtual Machine** in the **Actions** menu.

3. 使用以下特定配置完成“新建虚拟机向导”：

   1. **指定代次**：选择“第 2 代”。 Generation 2 virtual machines have nested virtualization enabled, which is required to run IoT Edge on a virtual machine.
   2. **配置网络**：设置“连接”的值设置为在上一部分创建的虚拟交换机。 
   3. **安装选项**：选择“从可启动映像文件安装操作系统”，并浏览到本地保存的磁盘映像文件。

4. Select **Finish** in the wizard to create the virtual machine.

创建新的 VM 可能需要几分钟。 

### <a name="enable-virtual-tpm"></a>启用虚拟 TPM

Once your VM is created, open its settings to enable the virtual trusted platform module (TPM) that lets you auto-provision the device.

1. Select the virtual machine, then open its **Settings**.

2. 导航到“安全性”。 

3. 取消选中“启用安全启动”。

4. 选中“启用受信任的平台模块”。 

5. 单击 **“确定”** 。  

### <a name="start-the-virtual-machine-and-collect-tpm-data"></a>启动虚拟机并收集 TPM 数据

In the virtual machine, build a tool that you can use to retrieve the device's **Registration ID** and **Endorsement key**.

1. Start your virtual machine and connect to it.

1. Follow the prompts within the virtual machine to finish the installation process and reboot the machine.

1. Sign in to your VM, then follow the steps in [Set up a Linux development environment](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/devbox_setup.md#linux) to install and build the Azure IoT device SDK for C.

   >[!TIP]
   >In the course of this article, you'll copy to and paste from the virtual machine, which is not easy through the Hyper-V Manager connection application. You may want to connect to the virtual machine through Hyper-V Manager once to retrieve its IP address: `ifconfig`. Then, you can use the IP address to connect through SSH: `ssh <username>@<ipaddress>`.

1. Run the following commands to build the SDK tool that retrieves your device provisioning information from the TPM simulator.

   ```bash
   cd azure-iot-sdk-c/cmake
   cmake -Duse_prov_client:BOOL=ON -Duse_tpm_simulator:BOOL=ON ..
   cd provisioning_client/tools/tpm_device_provision
   make
   sudo ./tpm_device_provision
   ```

1. From a command window, navigate to the `azure-iot-sdk-c` directory and run the TPM simulator. 该模拟器通过套接字在端口 2321 和 2322 上进行侦听。 Do not close this command window; you will need to keep this simulator running.

   From the `azure-iot-sdk-c` directory, run the following command to start the simulator:

   ```bash
   ./provisioning_client/deps/utpm/tools/tpm_simulator/Simulator.exe
   ```

1. Using Visual Studio, open the solution generated in the `cmake` directory named `azure_iot_sdks.sln`, and build it using the **Build solution** command on the **Build** menu.

1. **在 Visual Studio 的** “解决方案资源管理器”窗格中，导航到 **Provision\_Tools** 文件夹。 右键单击“tpm_device_provision”项目，然后选择“设为启动项目”。

1. Run the solution using either of the **Start** commands on the **Debug** menu. The output window displays the TPM simulator's **Registration ID** and the **Endorsement key**, which you should copy for use later when you create an individual enrollment for your device in You can close this window (with Registration ID and Endorsement key), but leave the TPM simulator window running.

## <a name="retrieve-provisioning-information-from-a-physical-device"></a>Retrieve provisioning information from a physical device

On your device, build a tool that you can use to retrieve the device's provisioning information.

1. Follow the steps in [Set up a Linux development environment](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/devbox_setup.md#linux) to install and build the Azure IoT device SDK for C.

1. Run the following commands to build the SDK tool that retrieves your device provisioning information from the TPM device.

   ```bash
   cd azure-iot-sdk-c/cmake
   cmake -Duse_prov_client:BOOL=ON ..
   cd provisioning_client/tools/tpm_device_provision
   make
   sudo ./tpm_device_provision
   ```

1. Copy the values for **Registration ID** and **Endorsement key**. 稍后要使用这些值在 DPS 中为设备创建个人注册。

## <a name="set-up-the-iot-hub-device-provisioning-service"></a>设置 IoT 中心设备预配服务

在 Azure 中创建 IoT 中心设备预配服务的新实例，并将其链接到 IoT 中心。 可以遵照[设置 IoT 中心 DPS](../iot-dps/quick-setup-auto-provision.md) 中的说明操作。

运行设备预配服务后，从概述页复制“ID 范围”的值。 配置 IoT Edge 运行时时，需要使用此值。 

## <a name="create-a-dps-enrollment"></a>创建 DPS 注册

从虚拟机中检索预配信息，并使用该信息在设备预配服务中创建个人注册。 

在 DPS 中创建注册时，可以声明“初始设备孪生状态”。 在设备孪生中可以设置标记，以便按解决方案中所需的任何指标（例如区域、环境、位置或设备类型）将设备分组。 这些标记用于创建[自动部署](how-to-deploy-monitor.md)。 

1. In the [Azure portal](https://portal.azure.com), navigate to your instance of IoT Hub Device Provisioning Service. 

2. 在“设置”下，选择“管理注册”。 

3. 选择“添加个人注册”，然后完成以下步骤以配置注册：  

   1. 对于“机制”，请选择“TPM”。 

   2. Provide the **Endorsement key** and **Registration ID** that you copied from your virtual machine.

      > [!TIP]
      > If you're using a physical TPM device, you need to determine the **Endorsement key**, which is unique to each TPM chip and is obtained from the TPM chip manufacturer associated with it. You can derive a unique **Registration ID** for your TPM device by, for example, creating an SHA-256 hash of the endorsement key.

   3. Select **True** to declare that this virtual machine is an IoT Edge device. 

   4. 选择要将设备连接到的链接“IoT 中心”。 You can choose multiple hubs, and the device will be assigned to one of them according to the selected allocation policy. 

   5. 根据需要，为设备提供一个 ID。 可以使用设备 ID 将单个设备指定为模块部署的目标。 If you don't provide a device ID, the registration ID is used.

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

Now that an enrollment exists for this device, the IoT Edge runtime can automatically provision the device during installation. 

## <a name="install-the-iot-edge-runtime"></a>安装 IoT Edge 运行时

IoT Edge 运行时部署在所有 IoT Edge 设备上。 该运行时的组件在容器中运行，允许你将其他容器部署到设备，以便在边缘上运行代码。 在虚拟机上安装 IoT Edge 运行时。 

在开始学习本文之前，请了解与设备类型匹配的 DPS“ID 范围”和设备“注册 ID”。 如果已安装示例 Ubuntu 服务器，请使用 **x64** 说明。 确保将 IoT Edge 运行时配置为自动预配而不是手动预配。 

[Install the Azure IoT Edge runtime on Linux](how-to-install-iot-edge-linux.md)

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

You can verify that the individual enrollment that you created in Device Provisioning Service was used. Navigate to your Device Provisioning Service instance in the Azure portal. Open the enrollment details for the individual enrollment that you created. Notice that the status of the enrollment is **assigned** and the device ID is listed. 

## <a name="next-steps"></a>后续步骤

使用设备预配服务注册过程可以在预配新设备的同时，设置设备 ID 和设备孪生标记。 可以在自动设备管理中，使用这些值将单个设备或设备组指定为目标。 了解如何[使用 Azure 门户大规模部署和监视 IoT Edge 模块](how-to-deploy-monitor.md)，或[使用 Azure CLI](how-to-deploy-monitor-cli.md) 执行此操作。
