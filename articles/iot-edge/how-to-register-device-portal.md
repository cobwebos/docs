---
title: 在 Azure 门户中注册新设备 - Azure IoT Edge | Microsoft Docs
description: 使用 Azure 门户注册新的 IoT Edge 设备并检索连接字符串
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 01/03/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.custom: seodec18
ms.openlocfilehash: 6414f694296ce1f5a8b65ccab30cceaf2172dee7
ms.sourcegitcommit: 803e66de6de4a094c6ae9cde7b76f5f4b622a7bb
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/02/2019
ms.locfileid: "53974890"
---
# <a name="register-a-new-azure-iot-edge-device-from-the-azure-portal"></a>通过 Azure 门户注册新 Azure IoT Edge 设备

在 Azure IoT Edge 中使用 IoT 设备之前，需要在 IoT 中心中注册这些设备。 注册设备后，会收到一个连接字符串，可使用该字符串针对 Edge 工作负载设置设备。

本文介绍如何使用 Azure 门户注册新 IoT Edge 设备。

## <a name="prerequisites"></a>先决条件

Azure 订阅中的 [IoT 中心](../iot-hub/iot-hub-create-through-portal.md)。

## <a name="create-a-device"></a>创建设备

在 Azure 门户中，IoT Edge 设备与连接到 IoT 中心但未启用 Edge 的设备分开创建和管理。

1. 登录 [Azure 门户](https://portal.azure.com)，导航到 IoT 中心。
2. 从菜单中选择“IoT Edge”。
3. 选择“添加 IoT Edge 设备”。
4. 提供一个描述性的设备 ID。 使用默认设置自动生成身份验证密钥并将新设备连接到中心。
5. 选择“保存”。

## <a name="view-all-devices"></a>查看所有设备

所有连接到 IoT 中心并已启用 Edge 的设备都列在 **IoT Edge** 页上。

## <a name="retrieve-the-connection-string"></a>检索连接字符串

如果已准备好设置设备，则需要连接字符串，该字符串使用物理设备在 IoT 中心内的标识链接该设备。

1. 在门户的 **IoT Edge** 页中，单击 Edge 设备列表中的设备 ID。
2. 复制**连接字符串(主密钥)** 或**连接字符串(辅助密钥)** 的值。

## <a name="next-steps"></a>后续步骤

了解如何[使用 Azure 门户将模块部署到设备](how-to-deploy-modules-portal.md)
