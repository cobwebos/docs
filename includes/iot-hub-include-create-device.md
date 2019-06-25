---
title: include 文件
description: include 文件
services: iot-hub
author: robinsh
ms.service: iot-hub
ms.topic: include
ms.date: 11/06/2018
ms.author: robinsh
ms.custom: include file
ms.openlocfilehash: d70544866b9e321d98acd3978da145276e036025
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/18/2019
ms.locfileid: "67173049"
---
<!-- put the ## header in the file that includes this file -->

本部分在 IoT 中心的标识注册表中创建设备标识。 设备无法连接到 IoT 中心，除非它在标识注册表中具有条目。 有关详细信息，请参阅 [IoT 中心开发人员指南](../articles/iot-hub/iot-hub-devguide-identity-registry.md)的“标识注册表”部分 

1. 在 IoT 中心导航菜单中，打开**IoT 设备**，然后选择**添加**在 IoT 中心中注册新设备。

    ![在门户中创建设备标识](./media/iot-hub-include-create-device/create-identity-portal.png)

1. 提供在新设备的名称，例如**myDeviceId**，然后选择**保存**。 此操作会为 IoT 中心创建新设备标识。

   ![添加新设备](./media/iot-hub-include-create-device/create-a-device.png)

   [!INCLUDE [iot-hub-pii-note-naming-device](iot-hub-pii-note-naming-device.md)]


1. 创建设备后，在“IoT 设备”窗格的列表中打开该设备  。 复制“连接字符串 ---主密钥”供以后使用  。

    ![设备连接字符串](./media/iot-hub-include-create-device/device-details.png)

> [!NOTE]
> IoT 中心标识注册表只存储设备标识，以启用对 IoT 中心的安全访问。 它存储设备 ID 和密钥作为安全凭据，以及启用/禁用标志让你禁用对单个设备的访问。 如果应用程序需要存储其他特定于设备的元数据，则应使用特定于应用程序的存储。 有关详细信息，请参阅 [IoT 中心开发人员指南](../articles/iot-hub/iot-hub-devguide-identity-registry.md)。
