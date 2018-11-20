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
ms.openlocfilehash: 86ae75118dd1311ea2ae92fb718fe4c58b8e5673
ms.sourcegitcommit: ada7419db9d03de550fbadf2f2bb2670c95cdb21
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/02/2018
ms.locfileid: "50961749"
---
# <a name="public-preview-service-limits"></a>公共预览版服务限制

处于公共预览版期间，Azure 数字孪生具有临时订阅、实例和速率限制。

这些约束有助于简化学习新服务及其许多功能。

> [!NOTE]
> 正式发布版 (GA) 将增大或删除这些限制。

## <a name="per-subscription-limits"></a>每个订阅的限制

处于公共预览版期间，每个 Azure 订阅一次只能创建或运行一个 Azure 数字孪生实例。

> [!TIP]
> 如果删除你的实例，则可以新建一个。

## <a name="per-instance-limits"></a>每个实例的限制

反之，每个 Azure 数字孪生实例可以具有：

- 一个 **IoTHub** 资源。
- 一个 **DeviceMessage** 事件类型的 **EventHub** 终结点。
- 最多三个事件类型为 **SensorChange**、**SpaceChange**、**TopologyOperation** 或 **UdfCustom** 的 **EventHub**、**ServiceBus** 或 **EventGrid** 终结点。

## <a name="management-api-limits"></a>管理 API 限制

管理 API 的请求速率限制是：

- 每秒向管理 API 发出 100 个请求。
- 单个管理 API 查询可以返回最多 1,000 个对象。 

> [!IMPORTANT]
> 如果超出了 1,000 个对象这一限制，你将收到一个错误并且必须简化查询。

## <a name="udf-rate-limits"></a>UDF 速率限制

以下限制设置对 Azure 数字孪生实例进行的所有用户定义函数调用的总数：

- 每秒 400 次客户端库调用
- 每秒 100 次 **SendNotification** 调用

> [!NOTE]
> 以下操作可能会导致临时应用其他速率限制：
> - 对拓扑对象元数据进行编辑
> - 对 UDF 定义进行更新
> - 第一次发送遥测数据的设备

## <a name="device-telemetry-limits"></a>限制设备遥测

以下限制会限制设备可以发送到 Azure 数字孪生实例的所有消息总数：

- 每秒 100 条消息

## <a name="next-steps"></a>后续步骤

若要尝试 Azure 数字孪生示例，请转到[查找可用房间的快速入门](./quickstart-view-occupancy-dotnet.md)。