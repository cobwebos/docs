---
title: include 文件
description: include 文件
services: iot-edge
author: kgremban
ms.service: iot-edge
ms.topic: include
ms.date: 06/25/2018
ms.author: kgremban
ms.custom: include file
ms.openlocfilehash: bacafdc8f7fd8e206335f3be0a086df1c54f1081
ms.sourcegitcommit: 150a40d8ba2beaf9e22b6feff414f8298a8ef868
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/27/2018
ms.locfileid: "37032106"
---
为模拟设备创建设备标识，以便它可以与 IoT 中心通信。 由于 IoT Edge 设备的行为和托管方式与典型 IoT 设备不同，请从一开始就将此设备声明为 IoT Edge 设备。 

1. 在 Azure 门户中导航到 IoT 中心。
1. 选择“IoT Edge”，然后选择“添加 IoT Edge 设备”。

   ![添加 IoT Edge 设备](./media/iot-edge-register-device/add-device.png)

1. 为模拟设备指定唯一设备 ID。
1. 选择“保存”以添加设备。
1. 从设备列表中选择新设备。
1. 复制“连接字符串 - 主密钥”的值并保存。 在下一部分中配置 IoT Edge 运行时时将用到此值。 

