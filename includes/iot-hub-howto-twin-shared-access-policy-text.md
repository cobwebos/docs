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
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "70050443"
---
<!-- This contains intro text for the "Get an IoT hub connection string" section in the iot-hub-lang-lang-twin-getstarted.md files-->

在本文中，你将创建一项后端服务，该服务将所需的属性添加到设备孪生，然后查询标识注册表，从而查找具有已相应更新的报告属性的所有设备。 服务需要“服务连接”权限才能修改设备孪生的所需属性，并且需要“注册表读取”权限才能查询标识注册表。 没有仅包含这两个权限的默认共享访问策略，因此需要创建一个。
