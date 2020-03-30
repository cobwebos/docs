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
ms.openlocfilehash: e493d1c4f5851ee510ea83e706afce5fbb6f487e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/27/2020
ms.locfileid: "70049063"
---
<!-- This contains intro text for the "Get an IoT hub connection string" section in the iot-hub-lang-lang-schedule-jobs.md files-->

在本文中，您将创建一个后端服务，其中计划作业以在设备上调用直接方法，计划作业以更新设备孪生，并监视每个作业的进度。 若要执行这些操作，你的服务需要“注册表读取”**** 和“注册表写入”**** 权限。 默认情况下，每个 IoT 中心都使用名为**注册表 ReadWrite**的共享访问策略创建，该策略授予这些权限。
