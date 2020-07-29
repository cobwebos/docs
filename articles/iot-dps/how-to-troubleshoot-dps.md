---
title: 诊断和排查 Azure IoT 中心 DPS 的连接断开问题
description: 了解如何诊断和排查 Azure IoT 中心设备预配服务 (DPS) 设备连接的常见错误
author: xujing-ms
manager: nberdy
ms.service: iot-dps
services: iot-dps
ms.topic: conceptual
ms.date: 09/09/2019
ms.author: xujing
ms.openlocfilehash: 3cbab09c6b50abb590cfe9f2720713a8fa547aa7
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/02/2020
ms.locfileid: "75646466"
---
# <a name="troubleshooting-with-azure-iot-hub-device-provisioning-service"></a>使用 Azure IoT 中心设备预配服务进行故障排除

由于存在许多可能的故障点（例如证明失败、注册失败等），IoT 设备的连接问题有时很难排查。本文介绍如何通过 [Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/overview) 检测和排查设备连接问题。

## <a name="using-azure-monitor-to-view-metrics-and-set-up-alerts"></a>使用 Azure Monitor 查看指标并设置警报

以下过程介绍如何查看和设置基于 IoT 中心设备预配服务指标的警报。 

1. 登录到 [Azure 门户](https://portal.azure.com)。

2. 浏览到 IoT 中心设备预配服务

3. 选择“指标”  。

4. 选择所需指标。 
   <br />目前有三种用于 DPS 的指标：

    | 指标名称 | 说明 |
    |-------|------------|
    | 证明尝试次数 | 尝试使用设备预配服务进行身份验证的设备数|
    | 注册尝试次数 | 尝试在成功进行身份验证后注册到 IoT 中心的设备数|
    | 分配的设备 | 已成功分配给 IoT 中心的设备数|

5. 选择所需的聚合方法，用于创建指标的视图。 

6. 若要设置某个指标的警报，请在指标边栏选项卡的右上角选择“新建警报规则”  。同样，也可以转到“警报”边栏选项卡，然后选择“新建警报规则”。  

7. 选择“添加条件”，  然后按照以下提示选择所需指标和阈值。

有关详细信息，请参阅 [Microsoft Azure 中的经典警报是什么？](../azure-monitor/platform/alerts-overview.md)

## <a name="using-log-analytic-to-view-and-resolve-errors"></a>使用 Log Analytic 查看和解决错误

1. 登录到 [Azure 门户](https://portal.azure.com)。

2. 浏览到 IoT 中心。

3. 选择“诊断设置”。 

4. 选择“启用诊断”  。

5. 允许收集所需的日志。

    | 日志名称 | 说明 |
    |-------|------------|
    | DeviceOperations | 与设备连接事件相关的日志 |
    | ServiceOperations | 与使用服务 SDK（例如，创建或更新注册组）相关的事件日志|

6. 启用“发送到 Log Analytics”  （[请参见定价](https://azure.microsoft.com/pricing/details/log-analytics/)）。 

7. 转到 Azure 门户中“设备预配服务”资源下的“日志”  选项卡。

8. 单击“运行”  以查看最近事件。

9. 如果有结果，请查找 `OperationName`、`ResultType`、`ResultSignature` 和 `ResultDescription`（错误消息）以获取有关错误的更多详细信息。


## <a name="common-error-codes"></a>常见错误代码
使用下表来了解和解决常见错误。

| 错误代码| 说明 | HTTP 状态代码 |
|-------|------------|------------|
| 400 | 请求正文无效；例如，它不能进行分析，或者对象无法验证。| 400 格式错误 |
| 401 | 授权令牌无法验证；例如，它已过期或无法应用到请求的 URI。 此错误代码也会在 TPM 证明流中返回给设备。 | 401 未授权|
| 404 | 设备预配服务实例或资源（例如注册）不存在。 |404 未找到 |
| 412 | 根据 RFC7232 规范，请求中的 ETag 与现有资源中的 ETag 不匹配。 | 412 先决条件失败 |
| 429 | 服务限制了操作。 对于具体的服务限制，请参阅 [IoT 中心设备预配服务限制](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits#iot-hub-device-provisioning-service-limits)。 | 429 请求过多 |
| 500 | 发生了内部错误。 | 500 内部服务器错误|
