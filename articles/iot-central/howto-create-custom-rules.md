---
title: 使用自定义规则和通知来扩展 Azure IoT Central |Microsoft Docs
description: 作为解决方案开发人员，当设备停止发送遥测数据时发送电子邮件通知 IoT Central 应用程序的配置。 此解决方案使用 Azure Stream Analytics 和 Azure Functions。
author: dominicbetts
ms.author: dobett
ms.date: 05/14/2019
ms.topic: conceptual
ms.service: iot-central
services: iot-central
ms.custom: mvc
manager: philmea
ms.openlocfilehash: 5248b9546ffe931b72123778d0d23574e5238405
ms.sourcegitcommit: 7042ec27b18f69db9331b3bf3b9296a9cd0c0402
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/06/2019
ms.locfileid: "66742413"
---
# <a name="extend-azure-iot-central-with-custom-rules-that-send-notifications"></a>使用自定义规则发送通知来扩展 Azure IoT Central

本操作方法指南介绍了，作为解决方案开发人员如何扩展 IoT Central 应用程序提供自定义规则和通知。 以下示例显示当设备停止发送遥测数据时向操作员发送通知。 该解决方案使用[Azure Stream Analytics](https://docs.microsoft.com/azure/stream-analytics/)查询来检测设备停止发送遥测数据。 Stream Analytics 作业使用[Azure Functions](https://docs.microsoft.com/azure/azure-functions/)发送通知电子邮件使用[SendGrid](https://sendgrid.com/docs/for-developers/partners/microsoft-azure/)。

本操作方法指南演示如何扩展 IoT Central 超出其已功能使用内置的规则和操作。

在本操作方法指南中，你了解如何：

* Stream 来自 IoT Central 应用程序中使用的遥测*持续的数据导出*。
* 创建设备停止发送数据时检测到一个 Stream Analytics 查询。
* 发送电子邮件通知使用的 Azure Functions 和 SendGrid 服务。

## <a name="prerequisites"></a>必备组件

完成本操作方法指南中的步骤需要有效的 Azure 订阅。

如果没有 Azure 订阅，请在开始之前创建一个[免费帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

### <a name="iot-central-application"></a>IoT Central 应用程序

创建 IoT Central 应用程序从[Azure IoT Central-我的应用程序](https://aka.ms/iotcentral)页具有以下设置：

| 设置 | 值 |
| ------- | ----- |
| 付款计划 | 即用即付 |
| 应用程序模板 | 示例 Contoso |
| 应用程序名称 | 接受默认值或选择自己的名称 |
| URL | 接受默认值或选择自己唯一的 URL 前缀 |
| Directory | Azure Active Directory 租户 |
| Azure 订阅 | Azure 订阅 |
| 区域 | 美国东部 |

示例和屏幕截图，在此文章使用**美国东部**区域。 选择离你近的位置，并确保在同一区域中创建的所有资源。

### <a name="resource-group"></a>资源组

使用[Azure 门户创建资源组](https://portal.azure.com/#create/Microsoft.ResourceGroup)称为**DetectStoppedDevices**包含您创建的其他资源。 在与 IoT 中心应用程序相同的位置中创建 Azure 资源。

### <a name="event-hubs-namespace"></a>事件中心命名空间

使用[Azure 门户创建事件中心命名空间](https://portal.azure.com/#create/Microsoft.EventHub)具有以下设置：

| 设置 | 值 |
| ------- | ----- |
| 名称    | 选择你的命名空间名称 |
| 定价层 | 基本 |
| 订阅 | 订阅 |
| 资源组 | DetectStoppedDevices |
| Location | 美国东部 |
| 吞吐量单位 | 第 |

### <a name="stream-analytics-job"></a>Stream Analytics 作业

使用[Azure 门户创建 Stream Analytics 作业](https://portal.azure.com/#create/Microsoft.StreamAnalyticsJob)具有以下设置：

| 设置 | 值 |
| ------- | ----- |
| Name    | 选择作业名称 |
| 订阅 | 订阅 |
| 资源组 | DetectStoppedDevices |
| Location | 美国东部 |
| 宿主环境 | 云 |
| 流式处理单位 | 3 |

### <a name="function-app"></a>函数应用

使用[Azure 门户创建 function app](https://portal.azure.com/#create/Microsoft.FunctionApp)具有以下设置：

| 设置 | 值 |
| ------- | ----- |
| 应用程序名称    | 选择函数应用名称 |
| 订阅 | 订阅 |
| 资源组 | DetectStoppedDevices |
| 操作系统 | Windows |
| 托管计划 | 使用计划 |
| Location | 美国东部 |
| 运行时堆栈 | .NET |
| 存储 | 新建 |

### <a name="sendgrid-account"></a>SendGrid 帐户

使用[Azure 门户创建 SendGrid 帐户](https://portal.azure.com/#create/Sendgrid.sendgrid)具有以下设置：

| 设置 | 值 |
| ------- | ----- |
| Name    | 选择你的 SendGrid 帐户名称 |
| 密码 | 创建密码 |
| 订阅 | 订阅 |
| 资源组 | DetectStoppedDevices |
| 定价层 | F1 免费 |
| 联系信息 | 填写所需信息 |

当你已创建所有所需的资源，你**DetectStoppedDevices**如以下屏幕截图所示的资源组：

![检测已停止的设备资源组](media/howto-create-custom-rules/resource-group.png)

## <a name="create-an-event-hub"></a>创建事件中心

可以配置 IoT Central 应用程序可以持续将遥测数据导出到事件中心。 在本部分中，您将创建事件中心以接收来自 IoT 中心应用程序的遥测数据。 事件中心将遥测数据传递给 Stream Analytics 作业进行处理。

1. 在 Azure 门户中，导航到事件中心命名空间，并选择 **+ 事件中心**。
1. 事件中心命名**centralexport**，然后选择**创建**。

事件中心命名空间如以下屏幕截图所示：

![事件中心命名空间](media/howto-create-custom-rules/event-hubs-namespace.png)

## <a name="get-sendgrid-api-key"></a>获取 SendGrid API 密钥

函数应用需要发送电子邮件的 SendGrid API 密钥。 若要创建 SendGrid API 密钥：

1. 在 Azure 门户中，导航到你的 SendGrid 帐户。 然后选择**管理**访问你的 SendGrid 帐户。
1. 在你的 SendGrid 帐户，选择**设置**，然后**API 密钥**。 选择**创建 API 密钥**:

    ![创建 SendGrid API 密钥](media/howto-create-custom-rules/sendgrid-api-keys.png)

1. 上**创建 API 密钥**页上，创建名为**AzureFunctionAccess**与**完全访问权限**权限。
1. 请记下的 API 密钥，配置函数应用时，会需要它。

## <a name="define-the-function"></a>定义函数

此解决方案使用 Azure Functions 应用 Stream Analytics 作业检测到已停止的设备时发送电子邮件通知。 若要创建函数应用：

1. 在 Azure 门户中，导航到**应用服务**实例中**DetectStoppedDevices**资源组。
1. 选择 **+** 若要创建新的函数。
1. 上**选择开发环境**页上，选择**门户内**，然后选择**继续**。
1. 上**CREATE FUNCTION**页上，选择**Webhook + API** ，然后选择**创建**。

门户将创建一个名为的默认函数**HttpTrigger1**:

![默认 HTTP 触发器函数](media/howto-create-custom-rules/default-function.png)

### <a name="configure-function-bindings"></a>配置函数绑定

若要发送电子邮件的 SendGrid，需要配置你的函数的绑定，如下所示：

1. 选择**集成**，选择输出**HTTP ($return)** ，然后选择**删除**。
1. 选择 **+ 新建输出**，然后选择**SendGrid**，然后选择**选择**。 选择**安装**安装 SendGrid 扩展。
1. 安装完成后，选择**使用函数返回值**。 添加有效**到地址**接收电子邮件通知。  添加有效**发件人地址**要用作电子邮件发件人。
1. 选择**新**旁边**SendGrid API 密钥应用设置**。 输入**SendGridAPIKey**密钥，以及前面记下的值的 SendGrid API 密钥。 然后选择“创建”  。
1. 选择**保存**以保存你的函数的 SendGrid 绑定。

如以下屏幕截图所示的集成设置：

![函数应用集成](media/howto-create-custom-rules/function-integrate.png)

### <a name="add-the-function-code"></a>添加函数代码

若要实现您的函数，添加C#代码，以分析传入的 HTTP 请求并发送电子邮件，如下所示：

1. 选择**HttpTrigger1**函数在函数应用中，并替换为C#代码与以下代码：

    ```csharp
    #r "Newtonsoft.Json"
    #r "..\bin\SendGrid.dll"

    using System;
    using SendGrid.Helpers.Mail;
    using Microsoft.Azure.WebJobs.Host;
    using Microsoft.AspNetCore.Mvc;
    using Microsoft.Extensions.Primitives;
    using Newtonsoft.Json;

    public static SendGridMessage Run(HttpRequest req, ILogger log)
    {
        string requestBody = new StreamReader(req.Body).ReadToEnd();
        log.LogInformation(requestBody);
        var notifications = JsonConvert.DeserializeObject<IList<Notification>>(requestBody);

        SendGridMessage message = new SendGridMessage();
        message.Subject = "Contoso device notification";

        var content = "The following device(s) have stopped sending telemetry:<br/><br/><table><tr><th>Device ID</th><th>Time</th></tr>";
        foreach(var notification in notifications) {
            log.LogInformation($"No message received - Device: {notification.deviceid}, Time: {notification.time}");
            content += $"<tr><td>{notification.deviceid}</td><td>{notification.time}</td></tr>";
        }
        content += "</table>";
        message.AddContent("text/html", content);

        return message;
    }

    public class Notification
    {
        public string deviceid { get; set; }
        public string time { get; set; }
    }
    ```

    保存新代码之前，可能会看到一条错误消息。

1. 选择**保存**若要保存该函数。

### <a name="test-the-function-works"></a>测试函数正常运行

若要在门户中测试的函数，首先选择**日志**在代码编辑器的底部。 然后选择**测试**到代码编辑器的右侧。 使用以下 JSON 作为**请求正文**:

```json
[{"deviceid":"test-device-1","time":"2019-05-02T14:23:39.527Z"},{"deviceid":"test-device-2","time":"2019-05-02T14:23:50.717Z"},{"deviceid":"test-device-3","time":"2019-05-02T14:24:28.919Z"}]
```

函数日志消息将显示在**日志**面板：

![函数日志输出](media/howto-create-custom-rules/function-app-logs.png)

在几分钟后**到**电子邮件地址会收到一封电子邮件包含以下内容：

```txt
The following device(s) have stopped sending telemetry:

Device ID   Time
test-device-1   2019-05-02T14:23:39.527Z
test-device-2   2019-05-02T14:23:50.717Z
test-device-3   2019-05-02T14:24:28.919Z
```

## <a name="add-stream-analytics-query"></a>添加 Stream Analytics 查询

此解决方案使用 Stream Analytics 查询来检测时设备将停止的时间超过 120 秒发送遥测数据。 该查询作为其输入使用从事件中心的遥测数据。 作业将查询结果发送到函数应用。 在本部分中，您将配置 Stream Analytics 作业：

1. 在 Azure 门户中，导航到 Stream analytics 作业下,**作业拓扑**选择**输入**，选择 **+ 添加流输入**，然后选择**事件中心**。
1. 使用下表中的信息来配置使用以前创建的事件中心输入，然后选择**保存**:

    | 设置 | 值 |
    | ------- | ----- |
    | 输入别名 | centraltelemetry |
    | 订阅 | 订阅 |
    | 事件中心命名空间 | 事件中心命名空间 |
    | 事件中心名称 | 使用现有的**centralexport** |

1. 下**作业拓扑**，选择**输出**，选择 **+ 添加**，然后选择**Azure 函数**。
1. 使用下表中的信息以配置输出，然后选择**保存**:

    | 设置 | 值 |
    | ------- | ----- |
    | 输出别名 | emailnotification |
    | 订阅 | 订阅 |
    | 函数应用 | Function app |
    | 函数  | HttpTrigger1 |

1. 下**作业拓扑**，选择**查询**并将现有查询替换为下面的 SQL:

    ```sql
    with
    LeftSide as
    (
        SELECT
        -- Get the device ID from the message metadata and create a column
        GetMetadataPropertyValue([centraltelemetry], '[EventHub].[IoTConnectionDeviceId]') as deviceid1, 
        EventEnqueuedUtcTime AS time1
        FROM
        -- Use the event enqueued time for time-based operations
        [centraltelemetry] TIMESTAMP BY EventEnqueuedUtcTime
    ),
    RightSide as
    (
        SELECT
        -- Get the device ID from the message metadata and create a column
        GetMetadataPropertyValue([centraltelemetry], '[EventHub].[IoTConnectionDeviceId]') as deviceid2, 
        EventEnqueuedUtcTime AS time2
        FROM
        -- Use the event enqueued time for time-based operations
        [centraltelemetry] TIMESTAMP BY EventEnqueuedUtcTime
    )

    SELECT
        LeftSide.deviceid1 as deviceid,
        LeftSide.time1 as time
    INTO
        [emailnotification]
    FROM
        LeftSide
        LEFT OUTER JOIN
        RightSide 
        ON
        LeftSide.deviceid1=RightSide.deviceid2 AND DATEDIFF(second,LeftSide,RightSide) BETWEEN 1 AND 120
        where
        -- Find records where a device didn't send a message 120 seconds
        RightSide.deviceid2 is NULL
    ```

1. 选择“保存”。 
1. 若要启动 Stream Analytics 作业，请选择**概述**，然后**启动**，然后**现在**，然后**启动**:

    ![流分析](media/howto-create-custom-rules/stream-analytics.png)

## <a name="configure-export-in-iot-central"></a>在 IoT 中心中配置导出

导航到[IoT 中心应用程序](https://aka.ms/iotcentral)从 Contoso 模板创建。 在本部分中，将配置要流式传输到事件中心的遥测数据从其模拟设备的应用程序。 若要配置导出：

1. 导航到**连续数据导出**页上，选择 **+ 新建**，然后**Azure 事件中心**。
1. 使用以下设置配置导出，然后选择**保存**:

    | 设置 | 值 |
    | ------- | ----- |
    | 显示名称 | 导出到事件中心 |
    | Enabled | 启用 |
    | 事件中心命名空间 | 事件中心命名空间名称 |
    | 事件中心 | centralexport |
    | 度量 | 启用 |
    | 设备 | 关闭 |
    | 设备模板 | 关闭 |

![连续数据的导出配置](media/howto-create-custom-rules/cde-configuration.png)

导出状态是等到**运行**，并继续。

## <a name="test"></a>测试

若要测试该解决方案，可以禁用从 IoT Central 连续数据导出到已停止模拟设备：

1. 在 IoT Central 应用程序中，导航到**连续数据导出**页，然后选择**导出到事件中心**导出配置。
1. 设置**已启用**到**Off** ，然后选择**保存**。
1. 至少两分钟后，**到**电子邮件地址会收到类似于以下示例内容的一个或多个电子邮件：

    ```txt
    The following device(s) have stopped sending telemetry:

    Device ID   Time
    7b169aee-c843-4d41-9f25-7a02671ee659    2019-05-09T14:28:59.954Z
    ```

## <a name="tidy-up"></a>整理

若要整理后本操作指南并避免不必要的费用，删除**DetectStoppedDevices**在 Azure 门户中的资源组。

可以删除从 IoT 中心应用程序**管理**应用程序中的页。

## <a name="next-steps"></a>后续步骤

通过本操作指南，我们已学会了：

* Stream 来自 IoT Central 应用程序中使用的遥测*持续的数据导出*。
* 创建设备停止发送数据时检测到一个 Stream Analytics 查询。
* 发送电子邮件通知使用的 Azure Functions 和 SendGrid 服务。

您已经知道了如何创建自定义规则和通知，建议下一步是了解如何[扩展 Azure IoT 中心使用自定义分析](howto-create-custom-analytics.md)。
