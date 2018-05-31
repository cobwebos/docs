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
ms.openlocfilehash: f8cd78e63099f864c5fc54b6268f6e558d738626
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/20/2018
ms.locfileid: "34371365"
---
## <a name="create-an-iot-hub"></a>创建 IoT 中心

[!INCLUDE [iot-hub-create-hub](iot-hub-create-hub.md)]

创建 IoT 中心以后，即可找到将设备和应用程序连接到 IoT 中心时需要使用的重要信息。 

在 IoT 中心导航菜单中，打开“共享访问策略”。
选择“iothubowner”策略，然后复制 IoT 中心的“连接字符串---主密钥”。 有关详细信息，请参阅[控制对 IoT 中心的访问](../articles/iot-hub/iot-hub-devguide-security.md)。

   > [!NOTE] 
   > 此设置教程不需要此 iothubowner 连接字符串。 不过，在完成此设置以后，可能需要将其用于某些介绍其他 IoT 方案的教程。

   ![获取 IoT 中心连接字符串](./media/iot-hub-get-started-create-hub-and-device/create-iot-hub5.png)

## <a name="register-a-device-in-the-iot-hub-for-your-device"></a>在 IoT 中心为设备进行设备注册

1. 在 IoT 中心导航菜单中，打开“IoT 设备”，然后单击“添加”，在 IoT 中心注册设备。

   ![在 IoT 中心的“IoT 设备”中添加设备](./media/iot-hub-get-started-create-hub-and-device/create-identity-portal.png)

2. 输入新设备的“设备 ID”。 设备 ID 区分大小写。

   [!INCLUDE [iot-hub-pii-note-naming-device](iot-hub-pii-note-naming-device.md)]

4. 单击“ **保存**”。
5. 创建设备后，在“IoT 设备”窗格的列表中打开该设备。
6. 复制“连接字符串 ---主密钥”供以后使用。

   ![获取设备连接字符串](./media/iot-hub-get-started-create-hub-and-device/device-connection-string.png)
