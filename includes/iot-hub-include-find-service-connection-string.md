---
title: include 文件
description: include 文件
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: include
ms.date: 07/16/2019
ms.author: robinsh
ms.custom: include file
ms.openlocfilehash: 55cdd864d73ce084d994c64233e79d5a58b17def
ms.sourcegitcommit: a6888fba33fc20cc6a850e436f8f1d300d03771f
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/16/2019
ms.locfileid: "69558441"
---
<!-- This tells how to get the connection string for the service shared access policy of your IoT hub -->

若要获取**服务**策略的 IoT 中心连接字符串, 请执行以下步骤:

1. 在[Azure 门户](https://portal.azure.com)中, 选择 "**资源组**"。 选择中心所在的资源组, 然后从资源列表中选择你的中心。

1. 在 IoT 中心的左侧窗格中, 选择 "**共享访问策略**"。

1. 在策略列表中, 选择 "**服务**策略"。

1. 在 "**共享访问密钥**" 下, 选择连接字符串的复制图标 **--primary key**并保存值。

    ![显示如何检索连接字符串](./media/iot-hub-include-find-service-connection-string/iot-hub-get-connection-string.png)

有关 IoT 中心共享访问策略和权限的详细信息, 请参阅[访问控制和权限](../articles/iot-hub/iot-hub-devguide-security.md#access-control-and-permissions)。
