---
title: "将 Intel NUC 设置为 Azure IoT 网关 | Microsoft Docs"
description: "将 Intel NUC 设置为传感器和 Azure IoT 中心之间的 IoT 网关，以便收集传感器信息并将其发送到 IoT 中心。"
services: iot-hub
documentationcenter: 
author: shizn
manager: yjianfeng
tags: 
keywords: "iot 网关, intel nuc, nuc 计算机, DE3815TYKE"
ms.assetid: f41d6b2e-9b00-40df-90eb-17d824bea883
ms.service: iot-hub
ms.devlang: c
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/28/2016
ms.author: xshi
translationtype: Human Translation
ms.sourcegitcommit: 1c4f44787a7200a1c3634b258df32d30152daa90
ms.openlocfilehash: 7725f49d71cb77dd6ff7ae075cc7449e568c21d7


---
# <a name="set-up-intel-nuc-as-an-iot-gateway"></a>将 Intel NUC 设置为 IoT 网关

## <a name="what-you-will-do"></a>执行的操作

- 将 Intel NUC 设置为 IoT 网关。
- 在 Intel NUC 上安装 Azure IoT 网关 SDK 包。
- 在 Intel NUC 上运行“hello_world”示例应用程序以验证网关功能。
如果有任何问题，请在[故障排除页面](iot-hub-gateway-kit-c-sim-troubleshooting.md)上查找解决方案。

## <a name="what-you-will-learn"></a>你要学习的知识

在本课程中将学习：

- 如何使用外围设备连接 Intel NUC。
- 如何使用智能包管理器在 Intel NUC 上安装和更新所需的包。
- 如何运行“hello_world”示例应用程序以验证网关功能。

## <a name="what-you-need"></a>所需条件

- 预安装了 Intel IoT 网关软件套件（Wind River Linux *7.0.0.13）的 Intel NUC 工具包 DE3815TYKE。
- 一根以太网电缆。
- 一个键盘。
- 一根 HDMI 或 VGA 电缆。
- 一台具有 HDMI 或 VGA 端口的显示器。

![网关工具包](media/iot-hub-gateway-kit-lessons/lesson1/kit_without_sensortag.png)

## <a name="connect-intel-nuc-with-the-peripherals"></a>使用外围设备连接 Intel NUC

下图为使用各种外围设备连接的 Intel NUC 示例：

1. 连接到键盘。
2. 通过 VGA 电缆或 HDMI 电缆连接到显示器。
3. 通过以太网电缆连接到有线网络。
4. 通过电源线连接到电源。

![连接到外围设备的 Intel NUC](media/iot-hub-gateway-kit-lessons/lesson1/nuc.png)

## <a name="connect-to-the-intel-nuc-system-from-host-computer-via-secure-shell-ssh"></a>通过安全外壳 (SSH) 从主机连接到 Intel NUC 系统

此处需要键盘和显示器以获取 NUC 设备的 IP 地址。 如果已经知道 IP 地址，则可以跳到本部分的步骤 3。

1. 按电源按钮打开 Intel NUC 并登录系统。

   默认用户名和密码均为 `root`。

2. 通过运行 `ifconfig` 命令获取 NUC 的 IP 地址。 已对 NUC 设备完成此步骤。

   以下是命令输出的示例。

   ![ifconfig 输出显示 NUC IP](media/iot-hub-gateway-kit-lessons/lesson1/ifconfig.png)

   在此示例中，`inet addr:` 后面的值是 IP 地址，计划从主机远程连接到 Intel NUC 时需要此 IP 地址。

3. 使用以下一个主机上的 SSH 客户端连接到 Intel NUC。

   - [PuTTY](http://www.putty.org/) for Windows。
   - Ubuntu 或 macOS 上的内置 SSH 客户端。

   在主机上对 Intel NUC 进行操作更加有效和高效。 你需要 IP 地址、用户名和密码以通过 SSH 客户端连接 NUC。 下面是在 macOS 上使用 SSH 客户端的示例。
   ![在 macOS 上运行的 SSH 客户端](media/iot-hub-gateway-kit-lessons/lesson1/ssh.png)

## <a name="install-the-azure-iot-gateway-sdk-package"></a>安装 Azure IoT 网关 SDK 包

Azure IoT 网关 SDK 包中包含 SDK 及其依赖项的预编译二进制文件。 这些二进制文件是 Azure IoT 网关 SDK、Azure IoT SDK 和相应的工具。 此包还包含用于验证网关功能的“hello_world”示例应用程序。 此 SDK 是网关的核心部分。 若要安装此包，请执行以下步骤：

1. 通过在终端窗口中运行以下命令添加 IoT 云存储库：

   ```bash
   rpm --import http://iotdk.intel.com/misc/iot_pub.key
   smart channel --add IoT_Cloud type=rpm-md name="IoT_Cloud" baseurl=http://iotdk.intel.com/repos/iot-cloud/wrlinux7/rcpl13/ -y
   ```

   `rpm` 命令导入 rpm 密钥。 `smart channel` 命令将 rpm 通道添加到智能包管理器。 在运行 `smart update` 命令之前，可以看到如下输出。

   ![rpm 和智能通道命令输出](media/iot-hub-gateway-kit-lessons/lesson1/rpm_smart_channel.png)

   ```bash
   smart update
   ```

2. 通过运行以下命令安装此包：

   ```bash
   smart install packagegroup-cloud-azure -y
   ```

   `packagegroup-cloud-azure` 是包的名称。 使用 `smart install` 命令安装此包。

   安装此包后，Intel NUC 可以用作网关。

## <a name="run-the-azure-iot-gateway-sdk-helloworld-sample-application"></a>运行 Azure IoT 网关 SDK“hello_world”示例应用程序

转到 `azureiotgatewaysdk/samples`，运行“hello_world”示例应用程序。 此示例应用程序从 `hello_world.json` 文件创建网关，并使用 Azure IoT 网关 SDK 体系结构的基本组件每 5 秒向文件中记录一条 hello world 消息。

通过运行以下命令运行“hello_world”示例应用程序：

```bash
cd /usr/share/azureiotgatewaysdk/samples/hello_world/
./hello_world hello_world.json
```

如果网关功能正常工作，示例应用程序将生成以下输出：

![应用程序输出](media/iot-hub-gateway-kit-lessons/lesson1/hello_world.png)

如果有任何问题，请在[故障排除页面](iot-hub-gateway-kit-c-troubleshooting.md)上查找解决方案。

## <a name="summary"></a>摘要

祝贺你！ 你已经完成了将 Intel NUC 设置为网关。 现在你可以转到下一课中设置主机、 创建 Azure IoT 中心并注册 Azure IoT 中心逻辑设备。

## <a name="next-steps"></a>后续步骤
[准备好主机和 Azure IoT 中心](iot-hub-gateway-kit-c-sim-lesson2-get-the-tools-win32.md)



<!--HONumber=Dec16_HO3-->


