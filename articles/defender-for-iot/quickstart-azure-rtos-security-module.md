---
title: 快速入门：配置并启用 Azure RTOS 安全模块
description: 了解如何在 Azure IoT 中心为 Azure RTOS 服务加入和启用安全模块。
services: defender-for-iot
ms.service: defender-for-iot
documentationcenter: na
author: mlottner
manager: rkarlin
editor: ''
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/06/2020
ms.author: mlottner
ms.openlocfilehash: 6f438bd7dd0b3e45fd292947a3b08db80401b825
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/22/2020
ms.locfileid: "90943437"
---
# <a name="quickstart-security-module-for-azure-rtos-preview"></a>快速入门：Azure RTOS 安全模块（预览版）

本文介绍了入门的先决条件，并说明了如何在 IoT 中心为 Azure RTOS 服务启用安全模块。 如果当前没有 IoT 中心，请参阅[使用 Azure 门户创建 IoT 中心](https://docs.microsoft.com/azure/iot-hub/iot-hub-create-through-portal)以开始操作。

> [!NOTE]
> 仅标准层 IoT 中心支持 Azure RTOS 安全模块。

## <a name="prerequisites"></a>先决条件 

### <a name="supported-devices"></a>支持的设备

- ST STM32F746G Discovery Kit
- NXP i.MX RT1060 EVK
- Microchip SAM E54 Xplained Pro EVK

从 [Azure RTOS 安全模块 GitHub 资源](hhtps://github.com/azure-rtos/azure-iot-preview/releases)下载、编译和运行你选择的特定看板和工具（IAR、semi's IDE 或 PC）的其中一个 .zip 文件。

### <a name="azure-resources"></a>Azure 资源

入门的下一个阶段是准备 Azure 资源。 需要一个 IoT 中心，建议使用 Log Analytics 工作区。 对于 IoT 中心，需要使用 IoT 中心连接字符串来连接设备。 
  
### <a name="iot-hub-connection"></a>IoT 中心连接

需要使用 IoT 中心连接才能开始使用。 

1. 在 Azure 门户中打开你的 **IoT 中心**。
1. 将 IoT 连接字符串复制到[配置文件](how-to-azure-rtos-security-module.md)中。


连接凭据采用用户应用程序配置 HOST_NAME、DEVICE_ID 和 DEVICE_SYMMETRIC_KEY  。

Azure RTOS 安全模块根据 MQTT 协议使用 Azure IoT 中间件连接。


### <a name="log-analytics-workspace"></a>Log Analytics 工作区

由于默认的适用于 IoT 的 Defender 解决方案，IoT 中心的 Log Analytics 引入处于关闭状态。 若要启用它以使用 Azure RTOS 安全模块，请执行以下操作： 
1. 在 Azure 门户中，转到 IoT 中心。
1. 选择“安全性”菜单下的“设置” 。
   :::image type="content" source="media/quickstart/azure-rtos-hub-settings.png" alt-text="访问 Azure RTOS 的数据收集选项"::: 
1. 选择“数据收集”。 
1. 在“工作区配置”选项中，将开关切换为“开” 。 
1. 创建新的或附加现有的 Log Analytics 工作区。 请确保已选中“访问原始安全数据”选项。 
 :::image type="content" source="media/quickstart/azure-rtos-data-collection-on.png" alt-text="显示数据收集选项和原始安全数据选项均已选中的 Azure RTOS 配置":::
1. 选择“保存”
1. 返回到 Azure 资源列表并确认你创建或附加的 Log Analytics 工作区已对 IoT 中心启用。
    :::image type="content" source="media/quickstart/verify-azure-resource-list.png" alt-text="检查 Azure 资源列表以确认是否存在为 IoT 中心添加的正确 Log Analytics 工作区"::: 

## <a name="next-steps"></a>后续步骤

转到下一篇文章，完成配置和自定义解决方案的操作。

> [!div class="nextstepaction"]
> [配置 Azure RTOS 安全模块](how-to-azure-rtos-security-module.md)
