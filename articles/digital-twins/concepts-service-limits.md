---
title: Azure 数字孪生公共预览版服务限制 | Microsoft Docs
description: 了解 Azure 数字孪生公共预览版服务限制
author: dwalthermsft
manager: deshner
ms.service: digital-twins
services: digital-twins
ms.topic: conceptual
ms.date: 10/26/2018
ms.author: dwalthermsft
ms.openlocfilehash: f9a3d934de47630ac3fd2356001014d006c2a4eb
ms.sourcegitcommit: 6e09760197a91be564ad60ffd3d6f48a241e083b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/29/2018
ms.locfileid: "50212261"
---
# <a name="public-preview-service-limits"></a>公共预览版服务限制

处于**公共预览版**期间，Azure 数字孪生将具有临时订阅、实例和速率限制，如下所述。

这些约束有助于简化学习新服务及其许多功能。

> [!NOTE]
> 这些限制将由**正式发布版** (**GA**) 增加和/或删除。

## <a name="per-subscription-limits"></a>每个订阅限制

处于**公共预览版**期间，每个 Azure 订阅一次只能创建或运行一个 Azure 数字孪生实例。

> [!TIP]
> 删除实例后将能够创建新的实例。

## <a name="per-instance-limits"></a>每个实例限制

反之，每个 Azure 数字孪生实例可以具有：

- 一个 **IoTHub** 资源
- 一个 **DeviceMessage** 事件类型的 **EventHub** 终结点
- 最多三个事件类型为 **SensorChange**、**SpaceChange**、**TopologyOperation** 或 **UdfCustom** 的 **EventHub**、**ServiceBus** 或 **EventGrid** 终结点

## <a name="management-api-limits"></a>管理 API 限制

管理 API 的请求速率限制是：

- 每秒向管理 API 发出 100 个请求
- 单个管理 API 查询可以返回多达 1000 个对象

> [!IMPORTANT]
> 如果超过 1000 个对象限制，将收到一个错误，并需要简化查询。

## <a name="udf-rate-limits"></a>UDF 速率限制

以下限制设置对 Azure 数字孪生实例进行的所有用户定义函数调用的总数：

- 每秒 400 次客户端库调用
- 每秒 100 次 **SendNotification** 调用

> [!NOTE]
> 以下操作可能导致临时应用其他速率限制：
> - 编辑拓扑对象元数据
> - 更新 UDF 定义
> - 第一次发送遥测数据的设备

## <a name="device-telemetry-limits"></a>限制设备遥测

以下限制会限制设备可以发送到 Azure 数字孪生实例的所有消息总数：

- 每秒 100 条消息

## <a name="next-steps"></a>后续步骤

若要尝试 Azure 数字孪生示例，请转到[查找可用房间的快速入门](./quickstart-view-occupancy-dotnet.md)。