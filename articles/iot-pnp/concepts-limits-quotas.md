---
title: 限制和配额 IoT 即插即用预览 |Microsoft Docs
description: 了解使用 IoT 即插即用预览时适用的限制、配额和限制。
author: miagdp
ms.author: miag
ms.date: 12/26/2019
ms.topic: conceptual
ms.service: iot-pnp
services: iot-pnp
ms.openlocfilehash: 48ecaaba6d956efd9da75d0582fa06d231cb3f80
ms.sourcegitcommit: ce4a99b493f8cf2d2fd4e29d9ba92f5f942a754c
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/28/2019
ms.locfileid: "75531371"
---
# <a name="iot-plug-and-play-preview-limits-quotas-and-throttles"></a>IoT 即插即用预览限制、配额和限制

本文介绍适用于公共预览版的 IoT 即插即用特定的限制、配额和限制。 还适用于现有的[IoT 中心配额和限制](../iot-hub/iot-hub-devguide-quotas-throttling.md)。

## <a name="iot-hub"></a>IoT 中心

对于公共预览版，以下限制和配额适用于 IoT 中心：

| 限制、限制和限制 | 值 | 说明 |
|-----|-----|-----|
| 每个中心可注册的设备功能模型（DCMs）或接口数量 | 1500 ||
| 每个设备可以注册的接口的最大数目 | 40 ||
| 每个设备可以注册的最大 DCMs 数 | 第 ||
| 接口/DCM 文件的最大大小 | 512个字符 ||
| 接口名称的最大大小 | 256个字符 ||
| 属性名的最大大小  | 64字节，深度为7个级别（为 `$iotin`保留第一个级别） | 允许的字符： a-z、a-z、0-9 （不是第一个字符）和下划线。 |
| 属性值的最大大小 | 512 字节 ||
| 命令名称的最大大小 | 100 字节 ||
| 设备克隆的大小 | 与[IoT 中心限制](../iot-hub/iot-hub-devguide-device-twins.md#device-twin-size)相同 ||
| 跨 SKU 的解析 API 调用（与设备无关） | 每秒100请求数 ||

## <a name="model-repository"></a>模型存储库

对于公共预览版，以下限制和配额适用于模型存储库：

| 限制、限制和限制| 值 |
|-----|-----|
| 每 Azure Active Directory 租户的公司型号存储库数 | 第 |
| 每个模型存储库的授权密钥数 | 10  |
| 每个公司模型存储库的模型数（DCMs 或接口）| 1500  |
| 每 Azure Active Directory 租户的公共模型存储库中的模型数（DCMs 或接口）| 1500  |
| 公司模型存储库中要删除的 DCMs 或接口数量 | 每秒10个查询（QPS）|
| 租户创建/更新的模型存储库数| 1 QPS |
| 在模型存储库中创建/更新/删除的授权密钥数 | 1 QPS|
| 在公司模型存储库中创建的 DCMs 数 | 10 QPS |
| 公司模型存储库中正在创建的接口数 | 10 QPS|
| 在公共模型存储库中创建的 DCMs 的数目 | 10 QPS|
| 公共模型存储库中正在创建的接口的数目 | 10 QPS|

## <a name="parser-library"></a>分析器库

分析器库遵循适用于[数字克隆定义语言](https://github.com/Azure/IoTPlugandPlay/tree/master/DTDL)的限制。

## <a name="next-steps"></a>后续步骤

建议的下一步是了解如何[连接到 IoT 即插即用设备并与之进行交互](./howto-develop-solution.md)。
