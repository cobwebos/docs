---
title: include 文件
description: include 文件
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: include
ms.date: 04/03/2020
ms.author: robinsh
ms.custom: include file
ms.openlocfilehash: 1a0502568c1673bcd7f57d3e9bc9c95ed90bbefa
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/28/2020
ms.locfileid: "80756913"
---
<!-- This tells how to create a custom shared access policy that has service connect and registry RW permissions for your IoT hub and get the connection string for it-->

若要创建授予**服务连接**和**注册表写入**权限的共享访问策略，并获取此策略的连接字符串，请执行以下步骤：

1. 在[Azure 门户](https://portal.azure.com)中，选择 "**资源组**"。 选择中心所在的资源组，然后从资源列表中选择中心。

1. 在中心的左侧窗格中，选择“共享访问策略”****。

1. 从策略列表上方的顶部菜单中选择“添加”****。

1. 在 "**添加共享访问策略**" 下，输入策略的描述性名称，如 " *serviceAndRegistryReadWrite*"。 在 "**权限**" 下，选择 "**注册表写入**和**服务连接**"，然后选择 "**创建**"。 （当您选择 "**注册表写入**" 时，将自动包含**注册表读取**权限。）

    ![显示如何添加新的共享访问策略](./media/iot-hub-include-find-service-regrw-connection-string/iot-hub-add-svc-regrw-policy.png)

1. 从策略列表中选择新策略。

1. 在“共享访问密钥”**** 下，选择“连接字符串 - 主密钥”**** 所对应的“复制”图标并保存该值。

    ![显示如何检索连接字符串](./media/iot-hub-include-find-service-regrw-connection-string/iot-hub-get-connection-string.png)

有关 IoT 中心共享访问策略和权限的详细信息，请参阅[访问控制和权限](../articles/iot-hub/iot-hub-devguide-security.md#access-control-and-permissions)。
