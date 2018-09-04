---
title: include 文件
description: include 文件
services: iot-hub
author: dominicbetts
ms.service: iot-hub
ms.topic: include
ms.date: 05/17/2018
ms.author: dobett
ms.custom: include file
ms.openlocfilehash: 935e2b9e861a889bef48c1d7ba119ab694cddfb3
ms.sourcegitcommit: f6e2a03076679d53b550a24828141c4fb978dcf9
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/27/2018
ms.locfileid: "43094205"
---
## <a name="create-a-device-identity"></a>创建设备标识

本部分使用 [Azure 门户](https://portal.azure.com)在 IoT 中心的标识注册表中创建设备标识。 设备无法连接到 IoT 中心，除非它在标识注册表中具有条目。 有关详细信息，请参阅 [IoT 中心开发人员指南](../articles/iot-hub/iot-hub-devguide-identity-registry.md)的“标识注册表”部分。使用门户中的“IoT 设备”面板为设备生成唯一设备 ID 和密钥，以用于在 IoT 中心中标识它本身。 设备 ID 区分大小写。

1. 登录到 [Azure 门户](https://portal.azure.com)

1. 选择“所有资源”，并查找 IoT 中心资源。

1. 打开 IoT 中心资源后，单击“IoT 设备”工具，并单击顶部的“添加”。 

    ![在门户中创建设备标识](./media/iot-hub-get-started-create-device-identity-portal/create-identity-portal.png)

1. 提供新设备的名称（例如 myDeviceId），然后单击“保存”。 此操作会为 IoT 中心创建新设备标识。

   [!INCLUDE [iot-hub-pii-note-naming-device](iot-hub-pii-note-naming-device.md)]

   ![添加新设备](./media/iot-hub-get-started-create-device-identity-portal/add-device.png)

1. 在设备列表中，单击新创建的设备，复制“连接字符串---主键”供以后使用。

    ![设备连接字符串](./media/iot-hub-get-started-create-device-identity-portal/device-connection-string.png)

> [!NOTE]
> IoT 中心标识注册表只存储设备标识，以启用对 IoT 中心的安全访问。 它存储设备 ID 和密钥用作安全凭据，以及可用于禁用单个设备访问的启用/禁用标志。 如果应用程序需要存储其他特定于设备的元数据，则应使用特定于应用程序的存储。 有关详细信息，请参阅 [IoT 中心开发人员指南](../articles/iot-hub/iot-hub-devguide-identity-registry.md)。
