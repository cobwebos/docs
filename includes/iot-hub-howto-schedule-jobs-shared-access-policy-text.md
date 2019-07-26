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
ms.openlocfilehash: 47d46cf4b400e29de8c526f750a10f57d6400220
ms.sourcegitcommit: 9dc7517db9c5817a3acd52d789547f2e3efff848
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/23/2019
ms.locfileid: "68402657"
---
<!-- This contains intro text for the "Get an IoT hub connection string" section in the iot-hub-lang-lang-schedule-jobs.md files-->

在本文中, 将创建一个后端服务, 用于计划作业在设备上调用直接方法, 计划作业以更新设备克隆, 并监视每个作业的进度。 若要执行这些操作, 服务需要**注册表读取**和**注册表写入**权限。 默认情况下, 每个 IoT 中心都是使用名为**registryReadWrite**的共享访问策略创建的, 该策略将授予这些权限。
