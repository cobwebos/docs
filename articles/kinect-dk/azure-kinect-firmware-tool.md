---
title: Azure Kinect 固件工具
description: 了解如何使用 Azure Kinect 固件工具查询和更新设备固件。
author: tesych
ms.author: tesych
ms.prod: kinect-dk
ms.date: 06/26/2019
ms.topic: conceptual
keywords: kinect, 固件, 更新
ms.openlocfilehash: f8b89751d27fb5a4b18d635f45f63f4f36bd05f6
ms.sourcegitcommit: a10074461cf112a00fec7e14ba700435173cd3ef
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/12/2019
ms.locfileid: "73932223"
---
# <a name="azure-kinect-dk-firmware-tool"></a>Azure Kinect DK 固件工具

Azure Kinect 固件工具可用于查询和更新 Azure Kinect DK 的设备固件。

## <a name="list-connected-devices"></a>列出已连接的设备

可以使用 -l 选项获取已连接的设备列表。  `AzureKinectFirmwareTool.exe -l`

```console
 == Azure Kinect DK Firmware Tool ==
Found 2 connected devices:
0: Device "000036590812"
1: Device "000274185112"
```

## <a name="check-device-firmware-version"></a>检查设备固件版本

可以使用 -q 选项检查第一个附加设备的当前固件版本，例如 `AzureKinectFirmwareTool.exe -q`。

```console
 == Azure Kinect DK Firmware Tool ==
Device Serial Number: 000036590812
Current Firmware Versions:
    RGB camera firmware:      1.5.92
    Depth camera firmware:    1.5.66
    Depth config file:        6109.7
    Audio firmware:           1.5.14
    Build Config:             Production
    Certificate Type:         Microsoft
```

如果附加了多个设备，可以通过将完整的序列号添加到命令，来指定要查询的设备，例如：

`AzureKinectFirmwareTool.exe -q 000036590812`

## <a name="update-device-firmware"></a>更新设备固件

此工具的最常见用途是更新设备固件。 使用 `-u` 选项调用该工具可以执行更新。 固件更新可能需要几分钟时间，具体取决于必须更新哪些固件文件。

有关固件更新的分步说明，请参阅 [Azure Kinect 固件更新](update-device-firmware.md)。  

`AzureKinectFirmwareTool.exe -u firmware\AzureKinectDK_Fw_1.5.926614.bin`

如果附加了多个设备，可以通过将完整的序列号添加到命令，来指定要查询的设备。

`AzureKinectFirmwareTool.exe -u firmware\AzureKinectDK_Fw_1.5.926614.bin 000036590812`

## <a name="reset-device"></a>重置设备

如果必须将设备置于某种已知状态，可以使用 -r 选项重置附加的 Azure Kinect DK。

如果附加了多个设备，可以通过将完整的序列号添加到命令，来指定要查询的设备。

`AzureKinectFirmwareTool.exe -r 000036590812`

## <a name="inspect-firmware"></a>检查固件

在更新实际设备之前，可以通过检查固件从固件 bin 文件中获取版本信息。

`AzureKinectFirmwareTool.exe -i firmware\AzureKinectDK_Fw_1.5.926614.bin`

```console
 == Azure Kinect DK Firmware Tool ==
Loading firmware package ..\tools\updater\firmware\AzureKinectDK_Fw_1.5.926614.bin.
File size: 1228844 bytes
This package contains:
  RGB camera firmware:      1.5.92
  Depth camera firmware:    1.5.66
  Depth config files: 6109.7 5006.27
  Audio firmware:           1.5.14
  Build Config:             Production
  Certificate Type:         Microsoft
  Signature Type:           Microsoft
```

## <a name="firmware-update-tool-options"></a>固件更新工具选项

```console
 == Azure Kinect DK Firmware Tool ==
* Usage Info *
    AzureKinectFirmwareTool.exe <Command> <Arguments>

Commands:
    List Devices: -List, -l
    Query Device: -Query, -q
        Arguments: [Serial Number]
    Update Device: -Update, -u
        Arguments: <Firmware Package Path and FileName> [Serial Number]
    Reset Device: -Reset, -r
        Arguments: [Serial Number]
    Inspect Firmware: -Inspect, -i
        Arguments: <Firmware Package Path and FileName>

    If no Serial Number is provided, the tool will just connect to the first device.

Examples:
    AzureKinectFirmwareTool.exe -List
    AzureKinectFirmwareTool.exe -Update c:\data\firmware.bin 0123456
```

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
>[有关更新设备固件的分步说明](update-device-firmware.md)
