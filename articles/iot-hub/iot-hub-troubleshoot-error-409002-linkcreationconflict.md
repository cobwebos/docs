---
title: 排查 Azure IoT 中心错误 409002 LinkCreationConflict
description: 了解如何修复错误 409002 LinkCreationConflict
author: jlian
manager: briz
ms.service: iot-hub
services: iot-hub
ms.topic: troubleshooting
ms.date: 01/30/2020
ms.author: jlian
ms.openlocfilehash: 3c7515be573a0b74a39a77a91fbc554862c7f7aa
ms.sourcegitcommit: fa6fe765e08aa2e015f2f8dbc2445664d63cc591
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/01/2020
ms.locfileid: "76960771"
---
# <a name="409002-linkcreationconflict"></a>409002 LinkCreationConflict

本文介绍**409002 LinkCreationConflict**错误的原因和解决方案。

## <a name="symptoms"></a>症状

你会看到错误**409002 LinkCreationConflict**记录在诊断日志中，以及设备断开连接或云到设备的消息失败。 

<!-- When using AMQP? -->

## <a name="cause"></a>原因

通常，当 IoT 中心检测到客户端具有多个连接时，会发生此错误。 事实上，当新的连接请求到达具有现有连接的设备时，IoT 中心会关闭与此错误有关的现有连接。

### <a name="cause-1"></a>原因 1

在最常见的情况下，单独的问题（例如[404104 DeviceConnectionClosedRemotely](iot-hub-troubleshoot-error-404104-deviceconnectionclosedremotely.md)）会导致设备断开连接。 设备会立即尝试重新建立连接，但 IoT 中心仍会将设备视为已连接。 IoT 中心关闭先前的连接并记录此错误。

### <a name="cause-2"></a>原因 2

设备端逻辑出现故障会导致设备在已打开时建立连接。

## <a name="solution"></a>解决方案

此错误通常表现为一个不同的暂时性问题的副作用，因此请查看日志中的其他错误以进一步排除故障。 否则，请确保仅在连接断开时才发出新的连接请求。
