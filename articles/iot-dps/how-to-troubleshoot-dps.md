---
title: 诊断和排查与 Azure IoT 中心 DPS 断开连接的问题
description: 了解如何诊断和排查 Azure IoT 中心 DPS 设备连接的常见错误
author: xujing-ms
manager: nberdy
ms.service: iot-dps
services: iot-dps
ms.topic: conceptual
ms.date: 09/09/2019
ms.author: xujing
ms.openlocfilehash: b596a09e2185b38e6161ea4af2aa109e80faf248
ms.sourcegitcommit: dd69b3cda2d722b7aecce5b9bd3eb9b7fbf9dc0a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/12/2019
ms.locfileid: "70963416"
---
# <a name="troubleshooting-with-azure-iot-hub-device-provisioning-service"></a>Azure IoT 中心设备预配服务疑难解答

IoT 设备的连接问题可能难以排查，因为有许多可能的故障点，如证明失败、注册失败等。本文提供了有关如何通过[Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/overview)检测和解决设备连接问题的指南。

## <a name="using-azure-monitor-to-view-metrics-and-set-up-alerts"></a>使用 Azure Monitor 查看指标并设置警报

以下过程介绍了如何查看和设置 IoT 中心设备预配服务指标的警报。 

1. 登录到 [Azure 门户](https://portal.azure.com)。

2. 浏览到 IoT 中心设备预配服务。

3. 选择“指标”。

4. 选择所需的指标。 
   <br />对于 DPS，当前有三个指标：

    | 指标名称 | 描述 |
    |-------|------------|
    | 证明尝试次数 | 尝试通过设备预配服务进行身份验证的设备数|
    | 注册尝试次数 | 身份验证成功后试图注册到 IoT 中心的设备数|
    | 已分配设备 | 成功分配到 IoT 中心的设备数|

5. 选择所需的聚合方法，以创建指标的可视化视图。 

6. 若要设置指标警报，请选择 "指标" 边栏选项卡右上方的 "**新建警报规则**"，同样可以使用 "**警报**" 边栏选项卡并选择 "**新建警报规则**"。

7. 选择 "**添加条件**"，然后按照提示选择所需的指标和阈值。

若要了解详细信息，请参阅[什么是经典警报 Microsoft Azure？](../azure-monitor/platform/alerts-overview.md)

## <a name="using-log-analytic-to-view-and-resolve-errors"></a>使用日志分析查看和解决错误

1. 登录到 [Azure 门户](https://portal.azure.com)。

2. 浏览到 IoT 中心。

3. 选择“诊断设置”。

4. 选择“启用诊断”。

5. 启用要收集的所需日志。

    | 日志名称 | 描述 |
    |-------|------------|
    | DeviceOperations | 与设备连接事件相关的日志 |
    | ServiceOperations | 与使用服务 SDK （例如创建或更新注册组）相关的事件日志|

6. 启用**发送到 Log Analytics** （[请参阅定价](https://azure.microsoft.com/pricing/details/log-analytics/)）。 

7. 在设备预配服务资源下的 Azure 门户中转到**日志**选项卡。

8. 单击 "**运行**" 以查看最近事件。

9. 如果有结果， `OperationName`请查找`ResultSignature`、 `ResultType`、和`ResultDescription` （错误消息）以获取有关错误的更多详细信息。


## <a name="common-error-codes"></a>常见错误代码
使用下表来了解和解决常见错误。

| 错误代码| 描述 | HTTP 状态代码 |
|-------|------------|------------|
| 400 | 请求正文无效;例如，无法对其进行分析，或者无法验证对象。| 400格式错误 |
| 401 | 无法验证授权令牌;例如，它已过期或不适用于请求的 URI。 此错误代码也作为 TPM 证明流的一部分返回到设备。 | 401 未授权|
| 404 | 设备预配服务实例或资源（例如注册）不存在。 |404 未找到 |
| 412 | 根据 RFC7232，请求中的 ETag 与现有资源的 ETag 不匹配。 | 412预处理失败 |
| 429 | 服务正在阻止操作。 有关特定服务限制，请参阅[IoT 中心设备预配服务限制](https://docs.microsoft.com/azure/azure-subscription-service-limits#iot-hub-device-provisioning-service-limits)。 | 429请求太多 |
| 500 | 发生了内部错误。 | 500 内部服务器错误|
