---
title: "SensorTag 设备和 Azure IoT 网关 - 第 2 课：注册设备 | Microsoft Docs"
description: 
services: iot-hub
documentationcenter: 
author: shizn
manager: timtl
tags: 
keywords: "azure iot 中心, 物联网云, azure iot 中心创建设备, ti sensortag, ti ble"
ROBOTS: NOINDEX
redirect_url: /azure/iot-hub/iot-hub-gateway-kit-c-lesson1-set-up-nuc
ms.assetid: 2c18f5ae-e39a-48ae-a9fe-04bb595740a0
ms.service: iot-hub
ms.devlang: c
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 3/21/2017
ms.author: xshi
translationtype: Human Translation
ms.sourcegitcommit: 61e9a9fc7876094c04238c61cfc38efdd97b05f7
ms.openlocfilehash: a895f18b4cefc01f12613e701c71bd471bb369c3
ms.lasthandoff: 01/25/2017


---

# <a name="create-your-azure-iot-hub-and-register-your-device"></a>创建 Azure IoT 中心并注册设备

## <a name="what-you-will-do"></a>执行的操作

- 创建资源组
- 创建第一个 IoT 中心
- 使用 Azure CLI 在 IoT 中心注册设备。 

在 IoT 中心中注册设备时，Azure IoT 中心服务为设备生成一个密钥，以用于在服务中进行身份验证。 

如果有任何问题，请在[故障排除页面](iot-hub-gateway-kit-c-troubleshooting.md)上查找解决方案。

## <a name="what-you-will-learn"></a>你要学习的知识

在本课程中将学习：

- 如何使用 Azure CLI 创建 IoT 中心。
- 如何在 IoT 中心注册设备。

## <a name="what-you-need"></a>所需条件

- 一个有效的 Azure 订阅。 如果没有 Azure 帐户，只需几分钟时间就能创建一个[免费的 Azure 试用帐户](http://azure.microsoft.com/pricing/free-trial/)。
- 应该安装 Azure CLI。

## <a name="create-an-iot-hub"></a>创建 IoT 中心

若要创建 IoT 中心，请执行以下步骤：

1. 通过运行以下命令登录到 Azure 帐户：

   ```bash
   az login
   ```

   在成功登录后列出所有可用订阅。

2. 运行以下命令，设置默认的需要使用的 Azure 订阅：

   ```bash
   az account set --subscription {subscription id or name}
   ```

   可以在 `az login` 或 `az account list` 命令的输出中找到 `subscription ID or name`。

3. 通过运行以下命令注册提供程序。 资源提供程序是为应用程序提供资源的服务。 必须先注册提供程序，然后才能部署该提供程序提供的 Azure 资源。

   ```bash
   az provider register -n "Microsoft.Devices"
   ```

4. 通过运行以下命令在美国西部区域中创建名为 `iot-gateway` 的资源组：

   ```bash
   az group create --name iot-gateway --location westus
   ```
   
   `westus` 是你在其中创建资源组的位置。 如果希望使用其他位置，可以运行 `az account list-locations -o table` 来查看 Azure 支持的所有位置。

5. 通过运行以下命令在 `iot-gateway` 资源组中创建一个 IoT 中心：

   ```bash
   az iot hub create --name {my hub name} --resource-group iot-gateway
   ```

默认情况下，该工具在免费定价层中创建 IoT 中心。 有关详细信息，请参阅 [Azure IoT 中心定价](https://azure.microsoft.com/pricing/details/iot-hub/)。

> [!NOTE]
> IoT 中心的名称必须是全局唯一的。 在 Azure 订阅下只能创建一个 F1 版本的 Azure IoT 中心。

## <a name="register-your-device-in-your-iot-hub"></a>在 IoT 中心中注册设备

必须为向 IoT 中心发送消息并从 IoT 中心接收消息的每个设备注册一个唯一 ID。
运行以下命令，在 IoT 中心注册设备：

```bash
az iot device create --device-id mydevice --hub-name {my hub name} --resource-group iot-gateway
```

## <a name="summary"></a>摘要

你已创建了一个 IoT 中心并在该 IoT 中心内注册了逻辑设备的设备标识。 现在你可以了解如何配置并运行网关示例应用程序，以将数据从物理设备发送到云中的 IoT 中心。

## <a name="next-steps"></a>后续步骤
[配置并运行 BLE 示例应用程序](iot-hub-gateway-kit-c-lesson3-configure-ble-app.md)
