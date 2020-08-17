---
title: 教程 - 在 Azure IoT Central 中创建视频分析 IoT Edge 实例 (Linux VM)
description: 本教程演示如何创建视频分析 IoT Edge 实例，以便与视频分析 - 对象和运动检测应用程序模板一起使用。
services: iot-central
ms.service: iot-central
ms.subservice: iot-central-retail
ms.topic: tutorial
ms.author: nandab
author: KishorIoT
ms.date: 07/31/2020
ms.openlocfilehash: 69e5b757036a2d68fa779e3fc232cc42a034e33c
ms.sourcegitcommit: bfeae16fa5db56c1ec1fe75e0597d8194522b396
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/10/2020
ms.locfileid: "88037859"
---
# <a name="tutorial-create-an-iot-edge-instance-for-video-analytics-linux-vm"></a>教程：创建用于视频分析的 IoT Edge 实例 (Linux VM)

Azure IoT Edge 是一项完全托管的服务，可通过部署和运行以下服务在本地提供云智能：

* 自定义逻辑
* Azure 服务
* 人工智能

在 IoT Edge 中，这些服务直接在跨平台 IoT 设备上运行，从而使你能够安全地、大规模地在云中或脱机运行 IoT 解决方案。

本教程介绍如何在 Azure VM 中准备好 IoT Edge 设备。 IoT Edge 实例运行 Azure IoT Central 视频分析 - 对象和运动检测应用程序模板所使用的实时视频分析模块。

在本教程中，你将了解如何执行以下操作：
> [!div class="checklist"]
> * 创建安装了 Azure IoT Edge 运行时的 Azure VM
> * 准备好安装 IoT Edge 以托管实时视频分析模块并连接到 IoT Central

## <a name="prerequisites"></a>先决条件

在开始之前，应该先完成上一教程 - [在 Azure IoT Central 中创建视频分析应用程序](./tutorial-video-analytics-create-app.md)。

还需要一个 Azure 订阅。 如果没有 Azure 订阅，则可在 [Azure 注册页](https://aka.ms/createazuresubscription)上免费创建一个。

## <a name="deploy-azure-iot-edge"></a>部署 Azure IoT Edge

若要创建安装了最新 IoT Edge 运行时和实时视频分析模块的 Azure VM，请选择以下按钮：

[![iotedge-vm-deploy 的“部署到 Azure”按钮](https://aka.ms/deploytoazurebutton)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Flive-video-analytics%2Fmaster%2Fref-apps%2Flva-edge-iot-central-gateway%2Fvm_deploy%2FedgeModuleVMDeploy.json)

在“自定义部署”表单中填写下表中的信息：

| 字段 | 值 |
| ----- | ----- |
| 订阅 | 选择 Azure 订阅。 |
| 资源组 | *lva-rg* - 在上一教程中创建的资源组。 |
| 区域       | *美国东部* |
| DNS 标签前缀 | 为 VM 选择唯一 DNS 前缀。 |
| 管理员用户名 | *AzureUser* |
| 管理员密码 | 输入密码。 记下 scratchpad.txt 文件中的密码，以便后续使用。 |
| 作用域 ID | 在上一教程中添加网关设备时在 scratchpad.txt 文件中记下的作用域 ID。 |
| 设备 ID | *lva-gateway-001* - 在上一教程中创建的网关设备。 |
| 设备密钥 | 在上一教程中添加网关设备时在 scratchpad.txt 文件中记下的设备主密钥。 |
| IoT Central 应用主机 | 在上一教程的 scratchpad.txt 文件中记下的应用程序 URL。 例如：traders.azureiotcentral.com。 |
| IoT Central 应用 API 令牌 | 在上一教程中记下的运算符 API 令牌。 |
| IoT Central 设备预配密钥 | 在上一教程的 scratchpad.txt 文件中记下的主要组共享访问签名令牌。 |
| VM 大小 | *Standard_DS1_v2* |
| Ubuntu OS 版本 | *18.04-LTS* |
| 位置 | *[resourceGroup().location]* |

选择“查看 + 创建”。 完成验证后，选择“创建”。 通常需要三分钟才能完成部署。 部署完成后，导航到 Azure 门户中的 lva-rg 资源组。

## <a name="ensure-the-iot-edge-runtime-loads-the-modules"></a>确保 IoT Edge 运行时加载模块

在 Azure 门户中，导航到 lva-rg 资源组，然后选择虚拟机。 然后，在“支持 + 故障排除”部分，选择“串行控制台” 。

按 Enter，获取 `login:` 提示。 使用 AzureUser 作为创建 VM 时选择的用户名和密码。

运行以下命令检查 IoT Edge 运行时的版本。 撰写本文时，版本为 1.0.9：

```bash
sudo iotedge --version
```

使用该命令列出 IoT Edge 模块：

```bash
sudo iotedge list
```

部署将以下五个 IoT Edge 模块配置为运行：

* LvaEdgeGatewayModule
* edgeAgent
* edgeHub
* lvaEdge
* lvaYolov3

部署创建了一个自定义 IoT Edge 环境，其中包含实时视频分析所需的模块。 部署更新了默认的 config.yaml，以确保 IoT 设备预配服务所使用的 IoT Edge 运行时连接到 IoT Central。 部署还在 /data/storage 文件夹中创建了一个名为 state.json 的文件，用于为模块提供其他配置数据 。 有关详细信息，请参阅[创建用于视频分析的 IoT Edge 实例 (Intel NUC)](./tutorial-video-analytics-iot-edge-nuc.md) 教程。

若要对 IoT Edge 设备进行故障排除，请参阅[对 IoT Edge 设备进行故障排除](https://docs.microsoft.com/azure/iot-edge/troubleshoot)

## <a name="use-the-rtsp-simulator"></a>使用 RTSP 模拟器

如果没有真实相机设备连接到 IoT Edge 设备，可以使用视频分析应用程序模板中的两个模拟相机设备。 本部分说明如何在 IoT Edge 设备中使用模拟视频流。

这些说明将 [Live555 Media Server](http://www.live555.com/mediaServer/) 用作 Docker 容器中的 RTSP 模拟器。

> [!NOTE]
> 对此存储库中的第三方软件的引用仅仅旨在提供信息和便利。 Microsoft 不为第三方软件担保，也不为其提供权利。 有关详细信息，请参阅 [Live555 Media Server](http://www.live555.com/mediaServer/)。

使用以下命令在 IoT Edge VM 上的 Docker 容器中运行 rtspvideo 实用程序。 Docker 容器创建后台 RTSP 流：

```bash
sudo docker run -d --name live555 --rm -p 554:554 mcr.microsoft.com/lva-utilities/rtspsim-live555:1.2
```

使用以下命令列出 Docker 容器：

```bash
sudo docker ps
```

该列表包括一个名为 live555 的容器。

## <a name="next-steps"></a>后续步骤

你现已在 Azure 上运行的 Linux VM 中部署了 IoT Edge 运行时、LVA 模块和 Live555 模拟流。

若要管理相机，请遵循下一教程

> [!div class="nextstepaction"]
> [监视和管理视频分析 - 对象和运动检测应用程序](./tutorial-video-analytics-manage.md)
