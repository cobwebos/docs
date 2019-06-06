---
title: 在 Azure 门户中注册新设备 - Azure IoT Edge | Microsoft Docs
description: 使用 Azure 门户注册新的 IoT Edge 设备并检索连接字符串
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 06/03/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.custom: seodec18
ms.openlocfilehash: 16660fbed465cc70f16cde430024f33b8aa4350e
ms.sourcegitcommit: 600d5b140dae979f029c43c033757652cddc2029
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/04/2019
ms.locfileid: "66495358"
---
# <a name="register-a-new-azure-iot-edge-device-from-the-azure-portal"></a>通过 Azure 门户注册新 Azure IoT Edge 设备

在 Azure IoT Edge 中使用 IoT 设备之前，需要在 IoT 中心中注册这些设备。 后注册的设备，你将收到可用于设置设备的 IoT Edge 工作负荷的连接字符串。

本文介绍如何使用 Azure 门户注册新 IoT Edge 设备。

## <a name="prerequisites"></a>必备组件

可用的标准[IoT 中心](../iot-hub/iot-hub-create-through-portal.md)在 Azure 订阅中。

## <a name="create-a-device"></a>创建设备

在 Azure 门户中，IoT Edge 设备与连接到 IoT 中心但未启用 Edge 的设备分开创建和管理。

1. 登录 [Azure 门户](https://portal.azure.com)，导航到 IoT 中心。
2. 从菜单中选择“IoT Edge”  。
3. 选择“添加 IoT Edge 设备”  。
4. 提供一个描述性的设备 ID。 使用默认设置来自动生成身份验证密钥和新设备连接到中心。
5. 选择“保存”。 

## <a name="view-all-devices"></a>查看所有设备

所有连接到 IoT 中心并已启用 Edge 的设备都列在 **IoT Edge** 页上。

## <a name="retrieve-the-connection-string"></a>检索连接字符串

如果已准备好设置设备，则需要连接字符串，该字符串使用物理设备在 IoT 中心内的标识链接该设备。

1. 从**IoT Edge**在门户页中，单击从列表中的 IoT Edge 设备的设备 ID。
2. 复制**连接字符串(主密钥)** 或**连接字符串(辅助密钥)** 的值。

## <a name="next-steps"></a>后续步骤

了解如何[将模块部署到 Azure 门户的设备](how-to-deploy-modules-portal.md)。
