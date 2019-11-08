---
title: 将 DevKit 设备连接到 Azure IoT Central 应用程序 | Microsoft Docs
description: 作为设备开发人员，了解如何使用 IoT 即插即用将 MXChip IoT DevKit 设备连接到 Azure IoT Central 应用程序。
author: liydu
ms.author: liydu
ms.date: 08/17/2019
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: jeffya
ms.openlocfilehash: de394a98f78b0bdb7cdf090de5c052cc761aa92d
ms.sourcegitcommit: 827248fa609243839aac3ff01ff40200c8c46966
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/07/2019
ms.locfileid: "73748617"
---
# <a name="connect-an-mxchip-iot-devkit-device-to-your-azure-iot-central-application"></a>将 MXChip IoT DevKit 设备连接到 Azure IoT Central 应用程序

本文介绍了如何将 MXChip IoT DevKit （DevKit）设备连接到 Azure IoT Central 应用程序。 设备使用 DevKit 设备的认证 IoT 即插即用模型来配置其到 IoT Central 的连接。

本操作指南文章介绍：

- 获取 IoT Central 应用程序的连接详细信息。
- 准备设备并将其连接到 IoT Central 应用程序。
- 查看 IoT Central 中设备的遥测和属性。

## <a name="prerequisites"></a>先决条件

若要完成本文中的步骤，需要以下资源：

- [DevKit 设备](https://aka.ms/iot-devkit-purchase)。
- 从**预览应用程序**模板创建的 IoT Central 应用程序。 可以按照[创建 IoT 即插即用应用程序](./quick-deploy-iot-central-pnp.md?toc=/azure/iot-central-pnp/toc.json&bc=/azure/iot-central-pnp/breadcrumb/toc.json)中的步骤进行操作。

## <a name="get-device-connection-details"></a>获取设备连接详细信息

在 Azure IoT Central 应用程序中，选择 "**管理**" 选项卡，然后选择 "**设备连接**"。 记下 "**作用域 ID** **" 和 "主密钥"** （在 "**查看密钥**" 链接中）。 确保启用**自动批准**。

![设备组连接详细信息](media/howto-connect-devkit-pnp/device-group-connection-details.png)

## <a name="prepare-the-device"></a>准备设备

1. 从 GitHub 下载适用于 DevKit 设备的最新[预建 Azure IoT Central 即插即用固件](https://github.com/MXCHIP/IoTDevKit/raw/master/pnp/iotc_devkit/bin/iotc_devkit.bin)。

1. 使用 USB 线缆将 DevKit 设备连接到开发计算机。 在 Windows 中，已映射到 DevKit 设备上的存储的驱动器中会打开一个文件资源管理器窗口。 例如，该驱动器可能名为 **AZ3166 (D:)** 。

1. 将**iotc_devkit**文件拖到驱动器窗口上。 复制完成后，设备会使用新固件重新启动。

    > [!NOTE]
    > 如果在屏幕上看到 "**无 wi-fi**" 等错误，这是因为 DevKit 尚未连接到 WiFi。

1. 在 DevKit 上，按下**按钮 b**，按下并释放 "**重置**" 按钮，然后松开**按钮 B**。设备现在处于访问点模式。 若要确认，屏幕会显示 "IoT DevKit-AP" 和配置门户 IP 地址。

1. 在计算机或平板电脑上，连接到设备屏幕上显示的 WiFi 网络名称。 WiFi 网络以**AZ**开头，后跟 MAC 地址。 当你连接到此网络时，你无法访问 internet。 此状态是预期的，你只需在配置设备时短时间连接到此网络。

1. 打开 Web 浏览器并导航到 [http://192.168.0.1/](http://192.168.0.1/)。 将显示以下网页：

    ![配置 UI](media/howto-connect-devkit-pnp/config-ui.png)

    在网页上输入以下内容：

    - WiFi 网络（SSID）的名称。
    - WiFi 网络密码。
    - 连接详细信息：你可以自行选择的**设备 id** ，以及你之前记下的**作用域 ID**和**组 SAS 主密钥**。

    > [!NOTE]
    > 目前，IoT DevKit 只能连接到 2.4 GHz Wi-fi，因为硬件限制，不支持 5 GHz。

1. 选择 "**配置设备**"，DevKit 设备重新启动并运行应用程序：

    ![重新启动 UI](media/howto-connect-devkit-pnp/reboot-ui.png)

    "DevKit" 屏幕会显示应用程序正在运行的确认：

    ![DevKit 正在运行](media/howto-connect-devkit-pnp/devkit-running.png)

DevKit 首先在 IoT Central 应用程序中注册新设备，然后开始发送数据。

## <a name="view-the-telemetry"></a>查看遥测数据

在此步骤中，你将在 Azure IoT Central 应用程序中查看遥测数据。

在 IoT Central 应用程序中，选择 "**设备**" 选项卡，选择已添加的设备。 在 "**概述**" 选项卡中，可以看到来自 DevKit 设备的遥测数据：

![IoT Central 设备概述](media/howto-connect-devkit-pnp/mxchip-overview-page.png)

## <a name="review-the-code"></a>查看代码

若要查看代码或修改并编译代码，请参阅[MXChip IoT DevKit 示例代码 GitHub 存储库](https://github.com/MXCHIP/IoTDevKit/tree/master/pnp)。

## <a name="next-steps"></a>后续步骤

现在，你已了解如何将 DevKit 设备连接到 Azure IoT Central 应用程序，接下来建议的下一步是了解如何为你自己的 IoT 设备[设置自定义设备模板](./howto-set-up-template-pnp.md?toc=/azure/iot-central-pnp/toc.json&bc=/azure/iot-central-pnp/breadcrumb/toc.json)。
