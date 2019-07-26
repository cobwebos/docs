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
ms.openlocfilehash: b6ca43616a2e7e7611b122bce5c95084e1fd5012
ms.sourcegitcommit: 9dc7517db9c5817a3acd52d789547f2e3efff848
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/23/2019
ms.locfileid: "68402368"
---
<!-- This tells how to get the connection string for the service shared access policy of your IoT hub -->

若要获取**服务**策略的 IoT 中心连接字符串, 请执行以下步骤:

1. 在[Azure 门户](https://portal.azure.com)中打开 IoT 中心。  若要访问 IoT 中心, 最简单的方法是选择 "**资源组**", 选择 iot 中心所在的资源组, 然后从资源列表中选择 iot 中心。

2. 在 IoT 中心的左侧窗格中, 选择 "**共享访问策略**"。

3. 在策略列表中, 选择 "**服务**策略"。

4. 在 "**共享访问密钥**" 下, 选择连接字符串的复制图标 **--primary key**并保存值。

    ![显示如何检索连接字符串](./media/iot-hub-include-find-service-connection-string/iot-hub-get-connection-string.png)

有关 IoT 中心共享访问策略和权限的详细信息, 请参阅[访问控制和权限](../articles/iot-hub/iot-hub-devguide-security.md#access-control-and-permissions)。
