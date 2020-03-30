---
title: include 文件
description: include 文件
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: include
ms.date: 08/07/2019
ms.author: robinsh
ms.custom: include file
ms.openlocfilehash: e81ce2743d2509ce52f3190218decfa4e518bdad
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "70050443"
---
<!-- This contains intro text for the "Get an IoT hub connection string" section in the iot-hub-lang-lang-twin-getstarted.md files-->

在本文中，您将创建一个后端服务，将所需属性添加到设备孪生中，然后查询标识注册表以查找报告的属性已相应更新的所有设备。 你的服务需要“服务连接”**** 权限才能修改设备孪生的所需属性，并且需要“注册表读取”**** 权限才能查询标识注册表。 没有仅包含这两个权限的默认共享访问策略，因此需要创建一个。
