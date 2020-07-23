---
title: include 文件
description: include 文件
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 04/28/2019
ms.author: robinsh
ms.custom: include file
ms.openlocfilehash: a0d1de622eefad4ae5e55a427f8b0b1bf4360c0a
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/02/2020
ms.locfileid: "84792077"
---
可以使用 REST API [Get Endpoint Health](https://docs.microsoft.com/rest/api/iothub/iothubresource/getendpointhealth#iothubresource_getendpointhealth) 获取终结点的运行状况状态。 我们建议使用与路由消息延迟相关的 [IoT 中心指标](../articles/iot-hub/iot-hub-metrics.md)来识别和调试终结点运行状况不佳或运行不正常时的错误，因为我们预计当终结点处于其中一种状态时，延迟会更高。


|运行状况状态|说明|
|---|---|
|healthy|终结点按预期方式接受消息。|
|不正常|终结点未接受消息，IoT 中心正在重试向此终结点发送消息。|
|未知|IoT 中心尚未尝试将消息传递到此终结点。|
|降级|终结点正在接受比预期慢的消息或正在从不正常状态恢复消息。|
|不活动|IoT 中心不再向此终结点发送消息。 重试将消息发送到此终结点失败。|
