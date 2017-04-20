---
title: "将 Intel Edison (C) 连接到 Azure IoT - 第 2 课：注册设备 | Microsoft Docs"
description: "使用 Azure CLI 创建资源组、创建 Azure IoT 中心，以及在 Azure IoT 中心注册 Edison。"
services: iot-hub
documentationcenter: 
author: shizn
manager: timtl
tags: 
keywords: 
ROBOTS: NOINDEX
redirect_url: /azure/iot-hub/iot-hub-intel-edison-kit-c-get-started
ms.assetid: 80bfc3cd-a1fc-4775-8994-d8033381dd3d
ms.service: iot-hub
ms.devlang: c
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 3/21/2017
ms.author: xshi
translationtype: Human Translation
ms.sourcegitcommit: 475b25f02715a60493e79ecd2170854019dfc4ac
ms.openlocfilehash: 9eebae32bf469e0744855ed9f98306bd36eb9671
ms.lasthandoff: 01/25/2017


---
# <a name="create-your-iot-hub-and-register-intel-edison"></a>创建 IoT 中心并注册 Intel Edison
## <a name="what-you-will-do"></a>执行的操作
* 创建资源组。
* 在资源组中创建 Azure IoT 中心。
* 使用 Azure 命令行接口 (Azure CLI) 将 Intel Edison 添加到 Azure IoT 中心。

使用 Azure CLI 将 Edison 添加到 IoT 中心时，服务会为 Edison 生成一个密钥，用于向服务进行身份验证。 如果有任何问题，请在[故障排除页面][troubleshooting]上查找解决方案。

## <a name="what-you-will-learn"></a>你要学习的知识
本文介绍：
* 如何使用 Azure CLI 创建 IoT 中心。
* 如何在 IoT 中心为 Edison 创建设备标识。

## <a name="what-you-need"></a>需要什么
* 一个 Azure 帐户。 如果没有 Azure 帐户，只需几分钟时间就能创建一个[免费的 Azure 试用帐户](http://azure.microsoft.com/pricing/free-trial/)。
* 应该安装 Azure CLI。

## <a name="create-your-iot-hub"></a>创建 IoT 中心
Azure IoT 中心可帮助你连接、监视并管理数百万 IoT 资产。 若要创建 IoT 中心，请执行以下步骤：

1. 通过运行以下命令登录到 Azure 帐户：

   ```bash
   az login
   ```

   在成功登录后会列出你的所有可用订阅。

2. 通过运行以下命令设置要使用的默认订阅：

   ```bash
   az account set --subscription {subscription id or name}
   ```

   可以在 `az login` 或 `az account list` 命令的输出中找到 `subscription ID or name`。

3. 通过运行以下命令注册提供程序。 资源提供程序是为应用程序提供资源的服务。 必须先注册提供程序，然后才能部署该提供程序提供的 Azure 资源。

   ```bash
   az provider register -n "Microsoft.Devices"
   ```
4. 通过运行以下命令在 West US 区域中创建名为 iot-sample 的资源组：

   ```bash
   az group create --name iot-sample --location westus
   ```

   `westus` 是你在其中创建资源组的位置。 如果希望使用其他位置，可以运行 `az account list-locations -o table` 来查看 Azure 支持的所有位置。

5. 通过运行以下命令在 iot-sample 资源组中创建一个 IoT 中心：

   ```bash
   az iot hub create --name {my hub name} --resource-group iot-sample
   ```

默认情况下，该工具在免费定价层中创建 IoT 中心。 有关详细信息，请参阅 [Azure IoT 中心定价](https://azure.microsoft.com/pricing/details/iot-hub/)。

> [!NOTE] 
> IoT 中心的名称必须是全局唯一的。
> 在 Azure 订阅下只能创建一个 F1 版的 Azure IoT 中心。


## <a name="register-edison-in-your-iot-hub"></a>在 IoT 中心注册 Edison
每个将消息发送到 IoT 中心并从 IoT 中心接收消息的设备都必须使用唯一 ID 注册。

运行以下命令，在 IoT 中心注册 Edison：

```bash
az iot device create --device-id myinteledison --hub-name {my hub name}
```

## <a name="summary"></a>摘要
已创建 IoT 中心并已在 IoT 中心使用设备标识注册 Edison。 已准备好学习如何将消息从 Edison 发送到 IoT 中心。

## <a name="next-steps"></a>后续步骤
[创建 Azure Function App 和 Azure 存储帐户来处理和存储 IoT 中心消息][process-and-store-iot-hub-messages]。


<!-- Images and links -->

[troubleshooting]: iot-hub-intel-edison-kit-c-troubleshooting.md
[process-and-store-iot-hub-messages]: iot-hub-intel-edison-kit-c-lesson3-deploy-resource-manager-template.md
