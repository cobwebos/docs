---
title: include 文件
description: include 文件
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 04/22/2019
ms.author: robinsh
ms.custom: include file
ms.openlocfilehash: d03579f704879bd8d012bb0bb326659d1f778dee
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/02/2020
ms.locfileid: "84793288"
---
"[获取终结点运行状况](https://docs.microsoft.com/rest/api/iothub/iothubresource/getendpointhealth#iothubresource_getendpointhealth)" REST API 提供终结点的运行状况状态，以及上一个已知错误，以确定终结点不正常的原因。 下表列出了最常见的错误。

|上一个已知错误|Description/发生时间|可能的缓解|
|-----|-----|-----|
|暂时|发生暂时性错误，IoT 中心将重试该操作。|观察路由[诊断日志](https://docs.microsoft.com/azure/iot-hub/iot-hub-monitor-resource-health#routes)。|
|InternalError|将消息传递到终结点时出错。|这是一个内部异常，还会观察路由[诊断日志](https://docs.microsoft.com/azure/iot-hub/iot-hub-monitor-resource-health#routes)。|
|未授权|IoT 中心无权向指定的终结点发送消息。|验证该终结点的连接字符串是否为最新。 如果已更改，请考虑 IoT 中心上的更新。 如果终结点使用托管标识，请检查 IoT 中心主体是否对目标具有所需的权限。|
|已中止|将消息写入到终结点时，IoT 中心将受到限制。|查看受影响的端点的限制限制。 修改终结点的配置（如果需要）。|
|超时|操作超时。|请重试操作即可。|
|未找到|目标资源不存在。|请确保目标资源存在。|
|找不到容器|存储容器不存在。|确保存储容器存在。|
|已禁用容器|已禁用存储容器。|请确保已启用存储容器。|
|MaxMessageSizeExceeded|消息路由的消息大小限制为 256 Kb。正在路由的消息大小超过了此限制。|使用更少的应用程序属性或较少的消息根据来检查是否可以减小消息大小。|
|PartitioningAndDuplicateDetectionNotSupported|服务总线可能未启用重复检测。|禁用来自服务总线的重复检测，或考虑使用不带重复检测的实体。|
|SessionfulEntityNotSupported|服务总线可能未启用会话。|从服务总线禁用会话，或考虑使用不带会话的实体。|
|NoMatchingSubscriptionsForMessage|Service bus 主题中没有用于写入消息的订阅。|创建要路由到的 IoT 中心消息的订阅。|
|EndpointExternallyDisabled|终结点不处于活动状态，因此 IoT 中心可以向其发送消息。|启用终结点以使其恢复为活动状态。|
|DeviceMaximumQueueDepthExceeded|达到了服务总线大小限制。|请考虑从目标事件中心删除消息，以允许将新消息引入到事件中心。|