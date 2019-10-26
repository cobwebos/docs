---
title: 将 Windows IoT Core 设备连接到 Azure IoT Central 应用程序 | Microsoft Docs
description: 了解如何以设备开发人员的身份将 MXChip IoT DevKit 设备连接到 Azure IoT Central 应用程序。
author: miriambrus
ms.author: miriamb
ms.date: 08/22/2019
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: peterpr
ms.openlocfilehash: d880130e114b2bd3e4f978c2ae3fc7bacf0648c4
ms.sourcegitcommit: 4c3d6c2657ae714f4a042f2c078cf1b0ad20b3a4
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/25/2019
ms.locfileid: "72953897"
---
# <a name="connect-a-windows-iot-core-device-to-your-azure-iot-central-application"></a>将 Windows IoT Core 设备连接到 Azure IoT Central 应用程序

[!INCLUDE [iot-central-original-pnp](../../../includes/iot-central-original-pnp-note.md)]

本文介绍如何以设备开发人员的身份将 Windows IoT Core 设备连接到 Microsoft Azure IoT Central 应用程序。

## <a name="before-you-begin"></a>开始之前

若要完成本文中的步骤，需要以下各项：

- 基于“示例 Devkit”应用程序模板创建的 Azure IoT Central 应用程序。 有关详细信息，请参阅[创建应用程序快速入门](quick-deploy-iot-central.md)。

- 运行 Windows 10 IoT Core 操作系统的设备。 有关详细信息，请参阅[设置 Windows 10 IoT Core 设备](https://docs.microsoft.com/windows/iot-core/tutorials/quickstarter/devicesetup)。

- 安装[了 node.js 8.0.0 版或](https://nodejs.org/)更高版本的开发计算机。 若要检查版本，可以在命令行中运行 `node --version`。 Node.js 适用于各种操作系统。

## <a name="the-sample-devkits-application"></a>示例 Devkits 应用程序

从“示例 Devkit”应用程序模板创建的应用程序包含一个具有以下特征的 Windows IoT Core 设备模板：

- 设备的遥测度量：**湿度**、**温度**和**压力**。
- 用于控制**风扇速度**的设置。
- 设备属性**模具编号**和云属性**位置**。

有关设备模板配置的完整详细信息，请参阅[Windows IoT Core 设备模板详细信息](#device-template-details)。

## <a name="add-a-real-device"></a>添加真实设备

在 Azure IoT Central 应用程序中，使用 " **Device Explorer** " 页从**Windows 10 IoT Core**设备模板添加真实设备。 记下设备连接详细信息（**作用域 id**、**设备 id**和**主密钥**）。

## <a name="prepare-the-device"></a>准备设备

为了使设备能够连接到 IoT Central，它需要一个连接字符串：

1. 使用 `dps-keygen` 命令行实用程序生成连接字符串：

    若要安装[密钥生成器实用程序](https://github.com/Azure/dps-keygen)，请运行以下命令：

    ```cmd/sh
    npm i -g dps-keygen
    ```

1. 若要生成连接字符串，请使用之前记下的连接详细信息运行以下命令：

    ```cmd/sh
    dps-keygen -di:<Device ID> -dk:<Primary or Secondary Key> -si:<Scope ID>
    ```

1. 复制 `dps-keygen` 输出中的连接字符串，以便在设备代码中使用。

若要使设备代码访问连接字符串，请将其保存到 Windows 10 IoT Core 设备上 `C:\Data\Users\DefaultAccount\Documents\` 文件夹中的一个名为**iothub**的文件中。

若要将**iothub**文件从桌面计算机复制到设备上的 `C:\Data\Users\DefaultAccount\Documents\` 文件夹，可以使用[Windows 设备门户](https://docs.microsoft.com/windows/iot-core/manage-your-device/deviceportal)：

1. 使用 web 浏览器导航到设备上的 Windows 设备门户。
1. 若要浏览设备上的文件，请选择 "**应用 > 文件资源管理器**"。
1. 导航到 "**用户 Folders\Documents**"。 然后上传**iothub**文件：

    ![上传连接字符串](media/howto-connect-windowsiotcore/device-portal.png)

## <a name="deploy-and-run"></a>部署和运行

若要在设备上部署并运行示例应用程序，可以使用[Windows 设备门户](https://docs.microsoft.com/windows/iot-core/manage-your-device/deviceportal)：

1. 使用 web 浏览器导航到设备上的 Windows 设备门户。
1. 若要部署和运行**Azure IoT 中心客户端**应用程序，请选择 "**应用" > "快速运行" 示例**。 然后选择 " **Azure IoT 中心客户端**"。
1. 然后选择 "**部署并运行**"。

    ![部署和运行](media/howto-connect-windowsiotcore/quick-run.png)

几分钟后，可以在 IoT Central 应用程序中查看来自设备的遥测数据。

[Windows 设备门户](https://docs.microsoft.com/windows/iot-core/manage-your-device/deviceportal)包含可用于对设备进行故障排除的工具：

- "**应用管理器**" 页可让你控制设备上运行的应用。
- 如果没有将监视器连接到设备，则可以使用 "**设备设置**" 页从设备中捕获屏幕快照。 例如：

    ![应用屏幕快照](media/howto-connect-windowsiotcore/iot-hub-foreground-client.png)

## <a name="download-the-source-code"></a>下载源代码

若要浏览和修改客户端应用程序的源代码，可以从[Iotcore GitHub 存储库](https://github.com/Microsoft/Windows-iotcore-samples/blob/master/Samples/Azure/IoTHubClients)中下载。

## <a name="device-template-details"></a>设备模板详细信息

从“示例 Devkit”应用程序模板创建的应用程序包含一个具有以下特征的 Windows IoT Core 设备模板：

### <a name="telemetry-measurements"></a>遥测数据度量

| 字段名     | 单位  | 最小值 | 最大值 | 小数位数 |
| -------------- | ------ | ------- | ------- | -------------- |
| 湿度       | %      | 0       | 100     | 0              |
| temp           | °C     | -40     | 120     | 0              |
| 压力       | hPa    | 260     | 1260    | 0              |

### <a name="settings"></a>设置

数字设置

| 显示名称 | 字段名 | 单位 | 小数位数 | 最小值 | 最大值 | 初始 |
| ------------ | ---------- | ----- | -------------- | ------- | ------- | ------- |
| 风扇速度    | fanSpeed   | RPM   | 0              | 0       | 1000    | 0       |

### <a name="properties"></a>属性

| Type            | 显示名称 | 字段名 | 数据类型 |
| --------------- | ------------ | ---------- | --------- |
| 设备属性 | 模具号   | dieNumber  | 数字    |
| 文本            | Location     | 位置   | N/A       |

## <a name="next-steps"></a>后续步骤

现在，你已了解如何将 Windows IoT Core 设备连接到 Azure IoT Central 应用程序，接下来建议的下一步是了解如何为你自己的 IoT 设备[设置自定义设备模板](howto-set-up-template.md)。
