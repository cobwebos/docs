---
title: 排查 Azure IoT 中心错误 409001 DeviceAlreadyExists
description: 了解如何修复错误 409001 DeviceAlreadyExists
author: jlian
manager: briz
ms.service: iot-hub
services: iot-hub
ms.topic: troubleshooting
ms.date: 01/30/2020
ms.author: jlian
ms.openlocfilehash: 93ab2ecc8e820c461a7c79082ac1d50c24f0ba8b
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: zh-CN
ms.lasthandoff: 07/02/2020
ms.locfileid: "76960784"
---
# <a name="409001-devicealreadyexists"></a>409001 DeviceAlreadyExists

本文介绍 **409001 DeviceAlreadyExists** 错误的原因和解决方案。

## <a name="symptoms"></a>症状

尝试在 IoT 中心注册设备时，请求失败且错误为 **409001 DeviceAlreadyExists**。

## <a name="cause"></a>原因

IoT 中心中已存在具有相同设备 ID 的设备。 

## <a name="solution"></a>解决方案

请使用其他设备 ID，然后重试。