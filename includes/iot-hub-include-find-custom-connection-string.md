---
title: include 文件
description: include 文件
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: include
ms.date: 11/02/2018
ms.author: robinsh
ms.custom: include file
ms.openlocfilehash: 6f43bbcd83861f7d39de2aa89bbe035c2ff5b809
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "70050413"
---
<!-- This tells how to create a custom shared access policy for your IoT hub and get the connection string for it-->

若要创建授予“服务连接”和“注册表读取”权限的共享访问策略并获取此策略的连接字符串，请执行以下步骤：

1. 在 [Azure 门户](https://portal.azure.com)中，选择“资源组”。 选择中心所在的资源组，然后从资源列表中选择中心。

1. 在中心的左侧窗格上，选择“共享访问策略”。

1. 在策略列表上方的顶部菜单中，选择“添加”。

1. 在“添加共享访问策略”下，输入策略的描述性名称，例如 serviceAndRegistryRead。 在“权限”下，选择“注册表读取”和“服务连接”，然后选择“创建”。

    ![演示如何添加新的共享访问策略](./media/iot-hub-include-find-custom-connection-string/iot-hub-add-custom-policy.png)

1. 从策略列表中选择新策略。

1. 在“共享访问密钥”下，选择“连接字符串 - 主密钥”的复制图标并保存值。

    ![显示如何检索连接字符串](./media/iot-hub-include-find-custom-connection-string/iot-hub-get-connection-string.png)

有关 IoT 中心共享访问策略和权限的详细信息，请参阅[访问控制和权限](../articles/iot-hub/iot-hub-devguide-security.md#access-control-and-permissions)。
