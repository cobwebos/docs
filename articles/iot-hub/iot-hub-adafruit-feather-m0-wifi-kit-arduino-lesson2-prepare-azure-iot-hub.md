---
title: "将 Arduino 连接到 Azure IoT - 第 2 课：注册设备 | Microsoft 文档"
description: "使用 Azure CLI 创建资源组、创建 Azure IoT 中心，以及在 Azure IoT 中心注册 Adafruit Feather M0 WiFi。"
services: iot-hub
documentationcenter: 
author: shizn
manager: timlt
tags: 
keywords: "将 arduino 连接到云, azure iot 中心, 物联网云, azure iot 中心创建设备, arduino 云"
ROBOTS: NOINDEX
redirect_url: /azure/iot-hub/iot-hub-adafruit-feather-m0-wifi-kit-arduino-get-started
ms.assetid: 5edc690b-7a1d-4ebc-b011-ff27bfffe6e8
ms.service: iot-hub
ms.devlang: arduino
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 3/21/2017
ms.author: xshi
ms.translationtype: Human Translation
ms.sourcegitcommit: 97acd09d223e59fbf4109bc8a20a25a2ed8ea366
ms.openlocfilehash: ac9421be9211d53f8b5239c356201ee3efd27999
ms.contentlocale: zh-cn
ms.lasthandoff: 03/10/2017

---
<a id="create-your-iot-hub-and-register-your-adafruit-feather-m0-wifi-arduino-board" class="xliff"></a>

# 创建 IoT 中心并注册 Adafruit Feather M0 WiFi Arduino 开发板

<a id="what-you-will-do" class="xliff"></a>

## 执行的操作
* 创建资源组。
* 在资源组中创建 Azure IoT 中心。
* 使用 Azure 命令行接口 (Azure CLI) 将 Arduino 开发板添加到 Azure IoT 中心。

使用 Azure CLI 将 Arduino 开发板添加到 IoT 中心时，服务会为 Arduino 开发板生成一个密钥，用于向服务进行身份验证。 如果有任何问题，请在[故障排除页面][troubleshoot]上查找解决方案。

<a id="what-you-will-learn" class="xliff"></a>

## 你要学习的知识
本文介绍：
* 如何使用 Azure CLI 创建 IoT 中心。
* 如何在 IoT 中心为 Arduino 开发板创建设备标识

<a id="what-you-need" class="xliff"></a>

## 所需条件
* 一个 Azure 帐户
* 安装了 Azure CLI 的计算机

<a id="create-your-iot-hub" class="xliff"></a>

## 创建 IoT 中心
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
> 在 Azure 订阅下只能创建一个 F1 版本的 Azure IoT 中心。

<a id="register-your-arduino-board-in-your-iot-hub" class="xliff"></a>

## 在 IoT 中心注册 Arduino 开发板
必须为向 IoT 中心发送消息并从 IoT 中心接收消息的每个设备注册一个唯一 ID。

运行以下命令，在 IoT 中心注册 Arduino 开发板：

```bash
az iot device create --device-id mym0wifi --hub-name {my hub name}
```

<a id="summary" class="xliff"></a>

## 摘要
你已创建了一个 IoT 中心并在该 IoT 中心内注册了 Arduino 开发板的设备标识。 现在可以学习如何从 Arduino 开发板向 IoT 中心发送消息了。

<a id="next-steps" class="xliff"></a>

## 后续步骤
[创建 Azure Function App 和 Azure 存储帐户来处理和存储 IoT 中心消息][process-and-store-iot-hub-messages]。


<!-- Images and links -->

[troubleshoot]: iot-hub-adafruit-feather-m0-wifi-kit-arduino-troubleshooting.md
[process-and-store-iot-hub-messages]: iot-hub-adafruit-feather-m0-wifi-kit-arduino-lesson3-deploy-resource-manager-template.md
