---
title: 限制和配额 IoT 即插即用预览 |微软文档
description: 了解使用 IoT 即插即用预览时适用的限制、配额和限制。
author: miagdp
ms.author: miag
ms.date: 12/26/2019
ms.topic: conceptual
ms.service: iot-pnp
services: iot-pnp
ms.openlocfilehash: 48ecaaba6d956efd9da75d0582fa06d231cb3f80
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/27/2020
ms.locfileid: "75531371"
---
# <a name="iot-plug-and-play-preview-limits-quotas-and-throttles"></a>IoT 即插即用预览限制、配额和限制

本文介绍了在公共预览版中应用的特定于 IoT 的即插即用限制、配额和限制。 还有适用的现有[IoT 中心配额和限制](../iot-hub/iot-hub-devguide-quotas-throttling.md)。

## <a name="iot-hub"></a>IoT 中心

对于公共预览版，以下限制和配额适用于 IoT 中心：

| 限制、限制和限制 | “值” | 说明 |
|-----|-----|-----|
| 每个中心可以注册的设备功能模型 （DCM） 或接口的数量 | 1500 ||
| 每个设备可注册的最大接口数 | 40 ||
| 每个设备可注册的最大 DCM 数量 | 1 ||
| 接口/DCM 文件的最大大小 | 512 字符 ||
| 接口名称的最大大小 | 256 字符 ||
| 属性名称的最大大小  | 64 字节，7 个深度级别（第一个级别保留为`$iotin`） | 允许的字符：a-z、A-Z、0-9（不作为第一个字符）和下划线。 |
| 属性值的最大大小 | 512 字节 ||
| 命令名称的最大大小 | 100 字节 ||
| 设备克隆的大小 | 与[IoT 中心限制相同](../iot-hub/iot-hub-devguide-device-twins.md#device-twin-size) ||
| 跨 SKU 解析 API 调用（无论单位数如何） | 100 请求/秒 ||

## <a name="model-repository"></a>模型存储库

对于公共预览，以下限制和配额适用于模型存储库：

| 限制、限制和限制| “值” |
|-----|-----|
| 每个 Azure 活动目录租户的公司模型存储库数 | 1 |
| 每个模型存储库的授权密钥数 | 10  |
| 每个公司模型存储库的模型（DCM 或接口）数| 1500  |
| 每个 Azure 活动目录租户的公共模型存储库中的模型（DCM 或接口）数| 1500  |
| 在公司模型存储库中正在删除的 DcM 或接口数 | 每秒 10 个查询 （QPS）|
| 租户正在创建/更新的模型存储库数| 1 QPS |
| 在模型存储库中创建/更新/删除的授权密钥数 | 1 QPS|
| 在公司模型存储库中创建的 DCM 数量 | 10 QPS |
| 在公司模型存储库中创建的接口数 | 10 QPS|
| 在公共模型存储库中创建的 DCM 数量 | 10 QPS|
| 在公共模型存储库中创建的接口数 | 10 QPS|

## <a name="parser-library"></a>解析器库

解析器库遵循适用于[数字双定义语言](https://github.com/Azure/IoTPlugandPlay/tree/master/DTDL)的限制。

## <a name="next-steps"></a>后续步骤

建议的下一步是学习如何[连接到 IoT 即插即用设备并与之交互](./howto-develop-solution.md)。
