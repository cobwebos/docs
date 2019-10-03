---
title: include 文件
description: include 文件
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: include
ms.date: 02/26/2019
ms.author: robinsh
ms.custom: include file
ms.openlocfilehash: 8d8f8021925ac9c9e427dd6571ecaa1a30c85497
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/18/2019
ms.locfileid: "67173572"
---
<!-- This is the instructions for creating a simulated device you can use for testing routing.-->

接下来，创建设备标识并保存其密钥供之后使用。 此设备标识由模拟应用程序用来发送消息到 IoT 中心。 此功能在 PowerShell 中不可用，在使用 Azure 资源管理器模板时也不可用。 以下步骤介绍如何使用 [Azure 门户](https://portal.azure.com)创建模拟设备。

1. 打开 [Azure 门户](https://portal.azure.com)并登录到 Azure 帐户。

2. 选择“资源组”，然后选择你的资源组  。 本教程使用 ContosoResources  。

3. 在资源列表中，选择 IoT 中心。 本教程使用 ContosoTestHub  。 从中心窗格选择“IoT 设备”  。

4. 选择“+ 添加”  。 在添加设备窗格中，填写设备 ID。 本教程使用 Contoso-Test-Device  。 将密钥留空，勾选“自动生成密钥”  。 确保已启用“将设备连接到 IoT 中心”  。 选择“保存”。 

   ![“添加设备”屏幕](./media/iot-hub-include-create-simulated-device-portal/add-device.png)

5. 创建设备后，即可选择它来查看生成的密钥。 选择主密钥上的“复制”图标，将其保存在某个位置（如记事本）供本教程的测试阶段使用。

   ![设备详细信息（包括密钥）](./media/iot-hub-include-create-simulated-device-portal/device-details.png)