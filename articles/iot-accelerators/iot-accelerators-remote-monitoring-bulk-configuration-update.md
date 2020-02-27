---
title: 批量管理连接到远程监视的设备 - Azure | Microsoft Docs
description: 本教程介绍如何批量管理连接到远程监视解决方案的设备。
author: Philmea
manager: philmea
ms.service: iot-accelerators
services: iot-accelerators
ms.topic: tutorial
ms.date: 11/29/2018
ms.author: philmea
ms.openlocfilehash: eaca93ac8a4e8c660be9618aefb27921a4e0a2eb
ms.sourcegitcommit: f27b045f7425d1d639cf0ff4bcf4752bf4d962d2
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/23/2020
ms.locfileid: "77565572"
---
# <a name="tutorial-manage-your-connected-devices-in-bulk"></a>教程：批量管理连接的设备

在本教程中，请使用远程监视解决方案加速器来批量管理已连接设备的配置。

作为 Contoso 的操作员，你需要使用新的固件版本来配置设备组。 你不希望在每个设备上单独更新固件。 若要更新设备组的固件，可以在远程监视解决方案加速器中使用设备组和自动设备管理。 添加到设备组的设备会在设备联机时获取最新固件。

本教程介绍以下操作：

>[!div class="checklist"]
> * 创建设备组
> * 准备和托管固件
> * 在 Azure 门户中创建设备配置
> * 将设备配置导入远程监视解决方案
> * 将配置部署到设备组中的设备
> * 监视部署

如果没有 Azure 订阅，请在开始之前创建一个[免费帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

<!--
If this is going to be a tutorial - we need to split this include into two so that we can accommodate the additional prerequisites:

[!INCLUDE [iot-accelerators-tutorial-prereqs](../../includes/iot-accelerators-tutorial-prereqs.md)]
-->

## <a name="prerequisites"></a>必备条件

若要遵循本教程，需在 Azure 订阅中部署远程监视解决方案加速器的实例。

如果尚未部署远程监视解决方案加速器，应完成[部署基于云的远程监视解决方案](quickstart-remote-monitoring-deploy.md)教程。

需要使用一个 Azure 存储帐户来托管固件文件。 可以使用现有的存储帐户，也可以在订阅中[创建新的存储帐户](../storage/common/storage-account-create.md)。

本教程使用 [IoT DevKit](https://microsoft.github.io/azure-iot-developer-kit/) 设备作为示例设备。

需要在本地计算机上安装以下软件：

* [Visual Studio Code (VS Code)](https://code.visualstudio.com/)。
* [Azure IoT Workbench](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.vscode-iot-workbench) VS Code 扩展。

开始之前：

* 确保 [IoT DevKit 设备上的引导加载程序为 1.4.0 或更高版本](https://microsoft.github.io/azure-iot-developer-kit/docs/firmware-upgrading/)。
* 确保 IoT DevKit SDK 的版本与引导加载程序的相同。 可以在 VS Code 中使用 Azure IoT Workbench 更新 IoT DevKit SDK。 打开命令面板，输入 **Arduino: Board Manager**。 有关详细信息，请参阅[准备开发环境](../iot-hub/iot-hub-arduino-iot-devkit-az3166-get-started.md#prepare-the-development-environment)。

还需将至少一个 IoT DevKit 设备连接到远程监视解决方案加速器。 如果尚未连接 IoT DevKit 设备，请参阅[将 MXChip IoT DevKit AZ3166 连接到 IoT 远程监视解决方案加速器](iot-accelerators-arduino-iot-devkit-az3166-devkit-remote-monitoringV2.md)。

## <a name="navigate-to-the-dashboard"></a>导航至仪表板

若要在浏览器中查看远程监视解决方案仪表板，请首先导航到 [Microsoft Azure IoT 解决方案加速器](https://www.azureiotsolutions.com/Accelerators#dashboard)。 可能会要求使用 Azure 订阅凭据登录。

然后，单击你在[快速入门](quickstart-remote-monitoring-deploy.md)中部署的远程监视解决方案加速器的磁贴上的“启动”  。

## <a name="create-a-device-group"></a>创建设备组

若要自动更新设备组的固件，必须确保设备是远程监视解决方案中某个设备组的成员：

1. 在“设备”页上，选择已连接到解决方案加速器的  所有 **IoT DevKit** 设备。 然后单击“作业”  。

1. 在“作业”面板中，选择“标记”，将作业名称设置为 **AddDevKitTag**，然后添加名为 **IsDevKitDevice** 且值为 **Y** 的文本标记。   然后单击“应用”。 

1. 现在，可以使用标记值创建设备组。 在“设备”页上，单击“管理设备组”。  

1. 创建在条件中使用标记名称 **IsDevKitDevice** 和值 **Y** 的文本筛选器。 将设备组另存为“IoT DevKit 设备”。 

稍后在本教程中，请使用此设备组来应用可更新所有成员的固件的设备配置。

## <a name="prepare-and-host-the-firmware"></a>准备和托管固件

[Azure IoT Workbench](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.vscode-iot-workbench) VS Code 扩展包含用于固件更新的示例设备代码。

### <a name="open-the-firmware-ota-sample-in-vs-code"></a>在 VS Code 中打开固件 OTA 示例

1. 确保 IoT DevKit 未连接到计算机。 启动 VS Code，然后将 DevKit 连接到计算机。

1. 按 **F1** 打开命令面板，键入并选择“IoT Workbench:  示例”。 然后选择“IoT DevKit”作为开发板。 

1. 找到 **Firmware OTA**，单击“打开示例”。  此时会打开一个新的 VS Code 窗口，并显示 **firmware_ota** 项目文件夹：

    ![IoT Workbench，请选择 Firmware OTA 示例](media/iot-accelerators-remote-monitoring-bulk-configuration-update/iot-workbench-firmware-example.png)

### <a name="build-the-new-firmware"></a>生成新的固件

设备固件的初始版本为 1.0.0。 新固件的版本号应该更高。

1. 在 VS Code 中打开 **FirmwareOTA.ino** 文件，将 `currentFirmwareVersion` 从 `1.0.0` 更改为 `1.0.1`：

    ![更改固件版本](media/iot-accelerators-remote-monitoring-bulk-configuration-update/version-1-0-1.png)

1. 打开命令面板，然后键入并选择“IoT Workbench:  设备”。 然后选择“设备编译”，对代码进行编译： 

    ![设备编译](media/iot-accelerators-remote-monitoring-bulk-configuration-update/iot-workbench-device-compile.png)

    VS Code 将编译的文件保存在项目的 `.build` 文件夹中。 VS Code 可能会在资源管理器视图中隐藏 `.build` 文件夹，具体取决于设置。

### <a name="generate-the-crc-value-and-calculate-the-firmware-file-size"></a>生成 CRC 值并计算固件文件大小

1. 打开命令面板，然后键入并选择“IoT Workbench:  设备”。 然后选择“生成 CRC”： 

    ![生成 CRC](media/iot-accelerators-remote-monitoring-bulk-configuration-update/iot-workbench-device-crc.png)

1. VS Code 在输出窗口中生成并输出 CRC 值、固件文件名和路径，以及文件大小。 请记下这些值供以后使用：

    ![CRC 信息](media/iot-accelerators-remote-monitoring-bulk-configuration-update/crc-info.png)

### <a name="upload-the-firmware-to-the-cloud"></a>将固件上传到云

使用 Azure 存储帐户在云中托管新的固件文件。

1. 导航到 Azure 门户中的存储帐户。 在“服务”部分选择“Blob”。  创建名为“firmware”的公共容器，用于存储固件文件： 

    ![创建文件夹](media/iot-accelerators-remote-monitoring-bulk-configuration-update/blob-folder.png)

1. 若要将固件文件上传到容器，请选择“firmware”容器，然后单击“上传”  。 

1. 选择 **FirmwareOTA.ino.bin**。 已在上一部分记下此文件的完整路径。

1. 固件文件上传完成后，请记下文件 URL。

### <a name="build-and-upload-the-original-firmware-to-the-iot-devkit-device"></a>生成原始固件并将其上传到 IoT DevKit 设备

1. 在 VS Code 中打开 **FirmwareOTA.ino** 文件，将 `currentFirmwareVersion` 改回 `1.0.0`：

    ![版本 1.0.0](media/iot-accelerators-remote-monitoring-bulk-configuration-update/version-1-0-1.png)

1. 打开命令面板，然后键入并选择“IoT Workbench:  设备”。 然后，选择“设备上传”  ：

    ![设备上传](media/iot-accelerators-remote-monitoring-bulk-configuration-update/device-upload.png)

1. VS Code 会验证代码并将其上传到 IoT DevKit 设备。

1. 当上传完成后，IoT DevKit 设备会重启。 当重启完成后，IoT DevKit 的屏幕会显示“FW 版本:  1.0.0”，并显示它在检查是否有新固件：

    ![ota-1](media/iot-accelerators-remote-monitoring-bulk-configuration-update/ota-1.jpg)

## <a name="create-a-device-configuration"></a>创建设备配置

设备配置指定设备的所需状态。 通常情况下，开发人员会在 Azure 门户的“IoT 设备配置”页上[创建配置](../iot-hub/iot-hub-automatic-device-management.md#create-a-configuration)。  设备配置是一个 JSON 文档，可指定设备的所需状态和一系列指标。

在本地计算机上将以下配置另存为名为 **firmware-update.json** 的文件。 将 `YOURSTRORAGEACCOUNTNAME`、`YOURCHECKSUM` 和 `YOURPACKAGESIZE` 占位符替换为以前记下的值。

```json
{
  "id": "firmware-update",
  "schemaVersion": null,
  "labels": {
    "Version": "1.0.1",
    "Devices": "IoT DevKit"
  },
  "content": {
    "deviceContent": {
      "properties.desired.firmware": {
        "fwVersion": "1.0.1",
        "fwPackageURI": "https://YOURSTRORAGEACCOUNTNAME.blob.core.windows.net/firmware/FirmwareOTA.ino.bin",
        "fwPackageCheckValue": "YOURCHECKSUM",
        "fwSize": YOURPACKAGESIZE
      }
    }
  },
  "targetCondition": "",
  "priority": 10,
  "systemMetrics": {
    "results": {
      "targetedCount": 0,
      "appliedCount": 0
    },
    "queries": {
      "targetedCount": "",
      "appliedCount": "select deviceId from devices where configurations.[[firmware-update]].status = 'Applied'"
    }
  },
  "metrics": {
    "results": {
      "Current": 1
    },
    "queries": {
      "Current": "SELECT deviceId FROM devices WHERE properties.reported.firmware.fwUpdateStatus='Current' AND properties.reported.firmware.type='IoTDevKit'",
      "Downloading": "SELECT deviceId FROM devices WHERE properties.reported.firmware.fwUpdateStatus='Downloading' AND properties.reported.firmware.type='IoTDevKit'",
      "Verifying": "SELECT deviceId FROM devices WHERE properties.reported.firmware.fwUpdateStatus='Verifying' AND properties.reported.firmware.type='IoTDevKit'",
      "Applying": "SELECT deviceId FROM devices WHERE properties.reported.firmware.fwUpdateStatus='Applying' AND properties.reported.firmware.type='IoTDevKit'",
      "Error": "SELECT deviceId FROM devices WHERE properties.reported.firmware.fwUpdateStatus='Error' AND properties.reported.firmware.type='IoTDevKit'"
    }
  }
}
```

请在以下部分使用此配置文件。

## <a name="import-a-configuration"></a>导入配置

在本部分，请在远程监视解决方案加速器中以包的形式导入设备配置。 通常情况下，操作员会完成此任务。

1. 在远程监视 Web UI 中，导航到“包”页并单击“+ 新建包”：  

    ![新建包](media/iot-accelerators-remote-monitoring-bulk-configuration-update/packagepage.png)

1. 在“新建包”面板中，选择“设备配置”作为包类型，选择“固件”作为配置类型。    在本地计算机上单击“浏览”，找到 **firmware-update.json** 文件，然后单击“上传”：  

    ![上传包](media/iot-accelerators-remote-monitoring-bulk-configuration-update/uploadpackage.png)

1. 现在，包列表包含 **firmware-update** 包。

## <a name="deploy-the-configuration-to-your-devices"></a>将配置部署到设备

在此部分，请创建并执行一个部署，以便将设备配置应用到 IoT DevKit 设备。

1. 在远程监视 Web UI 中，导航到“部署”页并单击“+ 新建部署”：  

    ![新部署](media/iot-accelerators-remote-monitoring-bulk-configuration-update/deploymentpage.png)

1. 在“新建部署”面板中，使用以下设置创建部署： 

    |选项|值|
    |---|---|
    |名称|部署固件更新|
    |包类型|设备配置|
    |配置类型|固件|
    |程序包|firmware-update.json|
    |设备组|IoT DevKit 设备|
    |优先度|10|

    ![创建部署](media/iot-accelerators-remote-monitoring-bulk-configuration-update/newdeployment.png)

    单击“应用”  。 可以在显示以下指标的“部署”页中看到新的部署： 

    * “目标”显示设备组中的设备数。 
    * “已应用”显示更新了配置内容的设备数。 
    * “成功”显示部署中报告成功的设备数。 
    * “失败”显示部署中报告失败的设备数。 

## <a name="monitor-the-deployment"></a>监视部署

数分钟后，IoT DevKit 会检索新的固件信息并开始将其下载到设备：

![ota-2](media/iot-accelerators-remote-monitoring-bulk-configuration-update/ota-2.jpg)

下载最多可能需要数分钟的时间，具体取决于网络速度。 等固件下载完以后，设备会验证文件大小和 CRC 值。 如果验证成功，MXChip 上的屏幕会显示“通过”。 

![ota-3](media/iot-accelerators-remote-monitoring-bulk-configuration-update/ota-3.jpg)

如果此检查成功，设备会重启。 在重启之前，会看到倒计时从 **5** 倒计到 **0**。

![ota-4](media/iot-accelerators-remote-monitoring-bulk-configuration-update/ota-4.jpg)

重启以后，IoT DevKit 引导加载程序会将固件升级到新版本。 升级可能要需要数秒钟。 在此阶段，设备中的 RGB LED 为红色，屏幕为空白。

![ota-5](media/iot-accelerators-remote-monitoring-bulk-configuration-update/ota-5.jpg)

当重启完成后，IoT DevKit 设备就会运行 1.0.1 版固件。

![ota-6](media/iot-accelerators-remote-monitoring-bulk-configuration-update/ota-6.jpg)

在“部署”页上单击某个部署，查看设备更新时的状态。  可以看到设备组中每个设备的状态，以及所定义的自定义指标。

![部署详细信息](media/iot-accelerators-remote-monitoring-bulk-configuration-update/deploymentstatus.png)

[!INCLUDE [iot-accelerators-tutorial-cleanup](../../includes/iot-accelerators-tutorial-cleanup.md)]

## <a name="next-steps"></a>后续步骤

本教程介绍了如何更新连接到解决方案的设备组的固件。 若干更新设备，请让解决方案使用自动设备管理。 若要详细了解 IoT 中心的解决方案中的自动设备管理功能，请参阅[使用 Azure 门户大规模配置和监视 IoT 设备](../iot-hub/iot-hub-auto-device-config.md)。