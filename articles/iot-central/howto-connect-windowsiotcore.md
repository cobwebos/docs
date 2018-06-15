---
title: 将 Windows IoT Core 设备连接到 Azure IoT Central 应用程序 | Microsoft Docs
description: 了解如何以设备开发人员的身份将 MXChip IoT DevKit 设备连接到 Azure IoT Central 应用程序。
author: miriambrus
ms.author: mriamb
ms.date: 04/09/2018
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: peterpr
ms.openlocfilehash: c36a9798718c37fba889323830b76cf8201785cf
ms.sourcegitcommit: 1b8665f1fff36a13af0cbc4c399c16f62e9884f3
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/11/2018
ms.locfileid: "35261893"
---
# <a name="connect-a-windows-iot-core-device-to-your-azure-iot-central-application"></a>将 Windows IoT Core 设备连接到 Azure IoT Central 应用程序

本文介绍如何以设备开发人员的身份将 Windows IoT Core 设备连接到 Microsoft Azure IoT Central 应用程序。

## <a name="before-you-begin"></a>开始之前

若要完成本文中的步骤，需要以下各项：

1. 基于“示例 Devkit”应用程序模板创建的 Azure IoT Central 应用程序。 有关详细信息，请参阅[创建 Azure IoT Central 应用程序](howto-create-application.md)。
2. 运行 Windows 10 IoT Core 操作系统的设备。 就本演练来说，我们将使用 Raspberry Pi

从“示例 Devkit”应用程序模板创建的应用程序包含一个具有以下特征的 **Windows IoT Core** 设备模板：

### <a name="telemetry-measurements"></a>遥测度量

| 字段名称     | 单位  | 最小值 | 最大值 | 小数位数 |
| -------------- | ------ | ------- | ------- | -------------- |
| 湿度       | %      | 0       | 100     | 0              |
| temp           | °C     | -40     | 120     | 0              |
| 压强       | hPa    | 260     | 1260    | 0              |

### <a name="settings"></a>设置

数字设置

| 显示名称 | 字段名称 | 单位 | 小数位数 | 最小值 | 最大值 | Initial |
| ------------ | ---------- | ----- | -------------- | ------- | ------- | ------- |
| 风扇速度    | fanSpeed   | RPM   | 0              | 0       | 1000    | 0       |


### <a name="properties"></a>属性

| Type            | 显示名称 | 字段名称 | 数据类型 |
| --------------- | ------------ | ---------- | --------- |
| 设备属性 | 模具编号   | dieNumber  | 数字    |
| 文本            | 位置     | location   | 不适用       |

## <a name="add-a-real-device"></a>添加真实设备

在 Azure IoT Central 应用程序中，从“Windows IoT Core”设备模板添加真实设备，并记下设备连接字符串。 有关详细信息，请参阅[向 Azure IoT Central 应用程序添加真实设备](tutorial-add-device.md)。

### <a name="prepare-the-windows-iot-core-device"></a>准备 Windows IoT Core 设备

若要设置 Windows IoT Core 设备，请按 [Set up a Windows IoT Core device] (https://github.com/Microsoft/microsoft-iot-central-firmware/tree/master/WindowsIoT#setup-a-physical-device)（设置 Windows IoT Core 设备）中的分步指南操作。

### <a name="add-a-real-device"></a>添加真实设备

在 Azure IoT Central 应用程序中，从“Windows IoT Core”设备模板添加真实设备，并记下设备连接字符串。 有关详细信息，请参阅[向 Azure IoT Central 应用程序添加真实设备](tutorial-add-device.md)。

## <a name="prepare-the-windows-10-iot-core-device"></a>准备 Windows 10 IoT Core 设备

### <a name="what-youll-need"></a>你将需要的内容

若要设置物理 Windows 10 IoT Core 设备，需首先有一个运行 Windows 10 IoT Core 的设备。 在[此处](https://developer.microsoft.com/en-us/windows/iot/getstarted/prototype/setupdevice)了解如何设置 Windows 10 IoT Core 设备。

此外还需要一个能够与 Azure IoT Central 通信的客户端应用程序。 可以使用 Azure SDK 开发你自己的自定义应用程序，然后使用 Visual Studio 将其部署到设备；也可以下载[预生成的示例](https://developer.microsoft.com/en-us/windows/iot/samples)，然后直接在设备上部署并运行。 

### <a name="deploying-the-sample-client-application"></a>部署示例客户端应用程序

若要将上一步的客户端应用程序部署到 Windows 10 IoT 设备以便对其进行准备，请执行以下操作：

**确保将连接字符串存储在设备上供客户端应用程序使用**
* 在桌面上，将连接字符串保存在名为 connection.string.iothub 的文本文件中。
* 将文本文件复制到设备的文档文件夹：`[device-IP-address]\C$\Data\Users\DefaultAccount\Documents\connection.string.iothub`

完成后，需在任意浏览器中键入 http://[device-IP-address]:8080，以便打开 [Windows 设备门户](https://docs.microsoft.com/en-us/windows/iot-core/manage-your-device/deviceportal)。

在该处，需按照下图所示执行相关操作：
1. 展开左侧的“应用”节点。
2. 单击“快速运行示例”。
3. 单击“Azure IoT 中心客户端”。
4. 单击“部署并运行”。

![Windows 设备门户中 Azure IoT 中心客户端的 Gif](./media/howto-connect-windowsiotcore/iothubapp.gif)

成功后，应用程序会在设备上启动，如下所示：

![Azure IoT 中心客户端应用的平屏幕截图](./media/howto-connect-windowsiotcore/IoTHubForegroundClientScreenshot.png)

在 Azure IoT Central 中，可以看到在 Raspberry Pi 上运行的代码如何与应用程序交互：

* 在真实设备的“度量”页上，可以看到遥测数据。
* 在“属性”页上，可以看到报告的“模具编号”属性的值。
* 在“设置”页中，可以更改 Raspberry Pi 上的各种设置，例如电压和风扇速度。

## <a name="download-the-source-code"></a>下载源代码

如需浏览并修改客户端应用程序的源代码，可以从[此处](https://github.com/Microsoft/Windows-iotcore-samples/tree/develop/Samples/Azure/IoTHubClients)的 GitHub 中下载。 如果打算修改代码，则应按[此处](https://github.com/Microsoft/Windows-iotcore-samples)的自述文件中针对桌面操作系统的说明进行操作。

> [!NOTE]
> 如果开发环境中未安装 **git**，可以从 [https://git-scm.com/download](https://git-scm.com/download) 下载。
