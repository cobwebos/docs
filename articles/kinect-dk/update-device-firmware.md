---
title: 更新 Azure Kinect DK 固件
description: 了解如何使用 Azure Kinect 固件工具更新 Azure Kinect DK 设备固件。
author: tesych
ms.author: tesych
ms.prod: kinect-dk
ms.date: 06/26/2019
ms.topic: conceptual
keywords: kinect, 固件, 更新, 恢复
ms.openlocfilehash: eb60003a4233110cb33208bcb8e9784737bb2a8b
ms.sourcegitcommit: aee08b05a4e72b192a6e62a8fb581a7b08b9c02a
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/09/2020
ms.locfileid: "75771590"
---
# <a name="update-azure-kinect-dk-firmware"></a>更新 Azure Kinect DK 固件

本文档提供有关如何更新 Azure Kinect DK 上的设备固件的指导。

Azure Kinect DK 不会自动更新固件。 可以使用 [Azure Kinect 固件工具](azure-kinect-firmware-tool.md)手动将固件更新到最新可用版本。

## <a name="prepare-for-firmware-update"></a>准备固件更新

1. [下载 SDK](sensor-sdk-download.md)。
2. 安装 SDK。
3. 在 SDK 安装位置的 tools 文件夹中，应该可以看到：

    - AzureKinectFirmwareTool.exe
    - firmware 文件夹中的固件 .bin 文件，例如 *AzureKinectDK_Fw_1.5.926614.bin*。

4. 将设备连接到主机电脑并将其打开。

> [!IMPORTANT]
> 在更新固件期间，请保持连接 USB 端口和电源。 在更新期间断开连接可能会导致固件损坏。

## <a name="update-device-firmware"></a>更新设备固件

1. 在 (SDK 安装位置)\tools\ 文件夹中打开命令提示符。
2. 使用 Azure Kinect 固件工具更新固件

    `AzureKinectFirmwareTool.exe -u <device_firmware_file.bin>`

    示例：

    `AzureKinectFirmwareTool.exe -u firmware\AzureKinectDK_Fw_1.5.926614.bin`

3. 等到固件更新完成。 这可能需要几分钟时间，具体取决于映像大小。

### <a name="verify-device-firmware-version"></a>验证设备固件版本

1. 验证固件是否已更新。

    `AzureKinectFirmwareTool.exe -q`

2. 查看以下示例。

    ```console
       >AzureKinectFirmwareTool.exe -q
 == Azure Kinect DK Firmware Tool ==
Device Serial Number: 000805192412
Current Firmware Versions:
  RGB camera firmware:      1.6.102
  Depth camera firmware:    1.6.75
  Depth config file:        6109.7
  Audio firmware:           1.6.14
  Build Config:             Production
  Certificate Type:         Microsoft
    ```

3. 如果看到上面的输出，则表示固件已更新。

4. 更新固件后，可以运行 [Azure Kinect 查看器](azure-kinect-viewer.md)来验证所有传感器是否按预期方式工作。

## <a name="troubleshooting"></a>故障排除

固件更新可能出于多种原因而失败。 如果固件更新失败，请尝试以下缓解步骤：

1. 再次尝试运行固件更新命令。

2. 通过查询固件版本来确认设备是否仍处于连接状态。        AzureKinectFirmareTool.exe

3. 如果所有其他方法均失败，请遵循[恢复](https://support.microsoft.com/help/4494277/reset-azure-kinect-dk)步骤将固件还原为出厂设置，然后重试。

如有其他任何问题，请参阅 [Microsoft 支持页](https://aka.ms/kinectsupport)

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
>[Azure Kinect 固件工具](azure-kinect-firmware-tool.md)
