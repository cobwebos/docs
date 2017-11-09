---
title: "了解 Azure IoT 中心作业 | Microsoft Docs"
description: "开发人员指南 - 计划要在连接到 IoT 中心的多个设备上运行的作业。 作业可以更新标记和所需属性，并可在多个设备上调用直接方法。"
services: iot-hub
documentationcenter: .net
author: juanjperez
manager: timlt
editor: 
ms.assetid: fe78458f-4f14-4358-ac83-4f7bd14ee8da
ms.service: iot-hub
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/24/2017
ms.author: juanpere
ms.openlocfilehash: f90ecb70ad12ed05d5d40f8b26a0a4e461c9f835
ms.sourcegitcommit: 9c3150e91cc3075141dc2955a01f47040d76048a
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/26/2017
---
# <a name="schedule-jobs-on-multiple-devices"></a>在多个设备上计划作业

Azure IoT 中心可启用多个构建基块（如 [设备孪生属性和标记][lnk-twin-devguide]和[直接方法][lnk-dev-methods]）。  通常情况下，后端应用允许设备管理员和操作员在计划的时间批量更新 IoT 设备并与之交互。  作业在计划的时间针对一组设备执行设备孪生更新和直接方法。  例如，操作员可使用用于启动和跟踪作业的后端应用在不会中断大楼运作的时间重新启动 43 号大楼第 3 层中的一组设备。

在以下情况下请考虑使用作业：需要计划并跟踪一组设备上的以下任何活动的进度：

* 更新所需属性
* 更新标记
* 调用直接方法

## <a name="job-lifecycle"></a>作业生命周期
作业由解决方案后端启动，并由 IoT 中心维护。  可以通过面向服务的 URI (`{iot hub}/jobs/v2/{device id}/methods/<jobID>?api-version=2016-11-14`) 启动作业，并通过面向服务的 URI (`{iot hub}/jobs/v2/<jobId>?api-version=2016-11-14`) 查询正在执行的作业的进度。 若要在启动作业后刷新正在运行的作业的状态，请运行作业查询。

> [!NOTE]
> 启动作业时，属性名称和值只能包含 US ASCII 可打印字母数字，但下列组中的任一项除外：`$ ( ) < > @ , ; : \ " / [ ] ? = { } SP HT`。

## <a name="jobs-to-execute-direct-methods"></a>用于执行直接方法的作业
以下代码片段显示了使用作业在一组设备上执行[直接方法][lnk-dev-methods]的 HTTPS 1.1 请求详细信息：

    PUT /jobs/v2/<jobId>?api-version=2016-11-14

    Authorization: <config.sharedAccessSignature>
    Content-Type: application/json; charset=utf-8
    Request-Id: <guid>
    User-Agent: <sdk-name>/<sdk-version>

    {
        jobId: '<jobId>',
        type: 'scheduleDirectRequest', 
        cloudToDeviceMethod: {
            methodName: '<methodName>',
            payload: <payload>,                 
            responseTimeoutInSeconds: methodTimeoutInSeconds 
        },
        queryCondition: '<queryOrDevices>', // query condition
        startTime: <jobStartTime>,          // as an ISO-8601 date string
        maxExecutionTimeInSeconds: <maxExecutionTimeInSeconds>        
    }

查询条件也可以位于单个设备 ID 上或位于设备 ID 列表中，如以下示例所示：

```
queryCondition = "deviceId = 'MyDevice1'"
queryCondition = "deviceId IN ['MyDevice1','MyDevice2']"
queryCondition = "deviceId IN ['MyDevice1']
```
[IoT 中心查询语言][lnk-query]格外详细地介绍了 IoT 中心查询语言。

## <a name="jobs-to-update-device-twin-properties"></a>用于更新设备孪生属性的作业
以下代码片段显示了使用作业更新设备克隆属性的 HTTPS 1.1 请求详细信息：

    PUT /jobs/v2/<jobId>?api-version=2016-11-14
    Authorization: <config.sharedAccessSignature>
    Content-Type: application/json; charset=utf-8
    Request-Id: <guid>
    User-Agent: <sdk-name>/<sdk-version>

    {
        jobId: '<jobId>',
        type: 'scheduleTwinUpdate', 
        updateTwin: <patch>                 // Valid JSON object
        queryCondition: '<queryOrDevices>', // query condition
        startTime: <jobStartTime>,          // as an ISO-8601 date string
        maxExecutionTimeInSeconds: <maxExecutionTimeInSeconds>        // format TBD
    }

## <a name="querying-for-progress-on-jobs"></a>查询作业的进度
以下代码片段显示了用于[查询作业][lnk-query]的 HTTPS 1.1 请求详细信息：

    GET /jobs/v2/query?api-version=2016-11-14[&jobType=<jobType>][&jobStatus=<jobStatus>][&pageSize=<pageSize>][&continuationToken=<continuationToken>]

    Authorization: <config.sharedAccessSignature>
    Content-Type: application/json; charset=utf-8
    Request-Id: <guid>
    User-Agent: <sdk-name>/<sdk-version>

从响应提供 continuationToken。  

## <a name="jobs-properties"></a>作业属性
以下列表显示了属性和相应说明，在查询作业或作业结果时可使用这些属性。

| 属性 | 说明 |
| --- | --- |
| **jobId** |应用程序提供的作业 ID。 |
| **startTime** |应用程序提供的作业开始时间(ISO-8601)。 |
| **endTime** |IoT 中心提供的作业完成时的日期(ISO-8601)。 只有在作业达到“完成”状态后才有效。 |
| **类型** |作业的类型： |
| | **scheduledUpdateTwin**：用于更新一组所需属性或标记的作业。 |
| | **scheduledDeviceMethod**：用于对一组设备孪生调用设备方法的作业。 |
| **状态** |作业的当前状态。 可能的状态值： |
| | **挂起**：已计划并等待作业服务选取。 |
| | **已计划**：计划在将来某个时间。 |
| | **正在运行**：当前活动的作业。 |
| | **已取消**：已取消作业。 |
| | **失败**：作业失败。 |
| | **完成**：作业已完成。 |
| **deviceJobStatistics** |有关作业执行的统计信息。 |
| | **deviceJobStatistics** 属性： |
| | **deviceJobStatistics.deviceCount**：作业中的设备数。 |
| | **deviceJobStatistics.failedCount**：作业失败的设备数。 |
| | **deviceJobStatistics.succeededCount**：作业成功的设备数。 |
| | **deviceJobStatistics.runningCount**：当前正在运行作业的设备数。 |
| | **deviceJobStatistics.pendingCount**：等待运行作业的设备数。 |

### <a name="additional-reference-material"></a>其他参考资料
IoT 中心开发人员指南中的其他参考主题包括：

* [IoT 中心终结点][lnk-endpoints]，介绍了每个 IoT 中心针对运行时和管理操作公开的各种终结点。
* [限制和配额][lnk-quotas]介绍了适用于 IoT 中心服务的配额，以及使用服务时预期会碰到的限制行为。
* [Azure IoT 设备和服务 SDK][lnk-sdks] 列出了开发与 IoT 中心交互的设备和服务应用时可使用的各种语言 SDK。
* [设备孪生、作业和消息路由的 IoT 中心查询语言][lnk-query]一文介绍了可用于从 IoT 中心检索设备孪生和作业相关信息的 IoT 中心查询语言。
* [IoT 中心 MQTT 支持][lnk-devguide-mqtt]提供有关 IoT 中心对 MQTT 协议的支持的详细信息。

## <a name="next-steps"></a>后续步骤
若要尝试本文中介绍的一些概念，可以根据兴趣学习以下 IoT 中心教程：

* [计划和广播作业][lnk-jobs-tutorial]

<!-- links and images -->

[lnk-endpoints]: iot-hub-devguide-endpoints.md
[lnk-quotas]: iot-hub-devguide-quotas-throttling.md
[lnk-sdks]: iot-hub-devguide-sdks.md
[lnk-query]: iot-hub-devguide-query-language.md
[lnk-devguide-mqtt]: iot-hub-mqtt-support.md
[lnk-jobs-tutorial]: iot-hub-node-node-schedule-jobs.md
[lnk-c2d-methods]: iot-hub-node-node-direct-methods.md
[lnk-dev-methods]: iot-hub-devguide-direct-methods.md
[lnk-get-started-twin]: iot-hub-node-node-twin-getstarted.md
[lnk-twin-devguide]: iot-hub-devguide-device-twins.md
