---
title: include 文件
description: include 文件
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: include
ms.date: 07/17/2019
ms.author: robinsh
ms.custom: include file
ms.openlocfilehash: f97ab0a0bb91af90daaf60c5c2c85f0ede56a5e7
ms.sourcegitcommit: 9dc7517db9c5817a3acd52d789547f2e3efff848
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/23/2019
ms.locfileid: "68403946"
---
<!-- This contains intro text for the "Get an IoT hub connection string" section in the iot-hub-lang-lang-twin-getstarted.md files-->

在本文中, 你将创建一个后端服务, 该服务将所需的属性添加到设备克隆, 然后查询标识注册表, 以查找具有相应更新的已报告属性的所有设备。 你的服务需要 "**服务连接**" 权限才能修改设备克隆所需的属性, 并且需要**注册表读取**权限来查询标识注册表。 没有默认的共享访问策略仅包含这两个权限, 因此需要创建一个。
