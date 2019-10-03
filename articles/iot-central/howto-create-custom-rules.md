---
title: 通过自定义规则和通知扩展 Azure IoT Central |Microsoft Docs
description: 作为解决方案开发人员, 配置一个 IoT Central 应用程序, 以便在设备停止发送遥测数据时发送电子邮件通知。 此解决方案使用 Azure 流分析和 Azure Functions。
author: dominicbetts
ms.author: dobett
ms.date: 08/23/2019
ms.topic: conceptual
ms.service: iot-central
services: iot-central
ms.custom: mvc
manager: philmea
ms.openlocfilehash: d89e8f174c7006c1a0f771dd4dfaa816ded3698c
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/28/2019
ms.locfileid: "70100990"
---
# <a name="extend-azure-iot-central-with-custom-rules-that-send-notifications"></a>通过用于发送通知的自定义规则扩展 Azure IoT Central

本操作方法指南为解决方案开发人员提供了如何使用自定义规则和通知扩展 IoT Central 应用程序。 该示例显示了在设备停止发送遥测数据时向操作员发送通知。 解决方案使用[Azure 流分析](https://docs.microsoft.com/azure/stream-analytics/)查询来检测设备何时停止发送遥测数据。 流分析作业使用[Azure Functions](https://docs.microsoft.com/azure/azure-functions/)通过[SendGrid](https://sendgrid.com/docs/for-developers/partners/microsoft-azure/)发送通知电子邮件。

本操作方法指南介绍了如何将 IoT Central 扩展到它对内置规则和操作所能执行的操作。

本操作方法指南介绍如何执行以下操作:

* 使用*连续数据导出*从 IoT Central 应用程序流式传输遥测数据。
* 创建一个流分析查询, 该查询可检测设备停止发送数据的时间。
* 使用 Azure Functions 和 SendGrid services 发送电子邮件通知。

## <a name="prerequisites"></a>先决条件

完成本操作方法指南中的步骤需要有效的 Azure 订阅。

如果没有 Azure 订阅，请在开始之前创建一个[免费帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

### <a name="iot-central-application"></a>IoT Central 应用程序

使用以下设置在[Azure IoT Central 应用程序管理器](https://aka.ms/iotcentral)网站上创建 IoT Central 应用程序:

| 设置 | ReplTest1 |
| ------- | ----- |
| 付款计划 | 即用即付 |
| 应用程序模板 | 示例 Contoso |
| 应用程序名 | 接受默认值或选择自己的名称 |
| URL | 接受默认值或选择自己的唯一 URL 前缀 |
| 目录 | 你的 Azure Active Directory 租户 |
| Azure 订阅 | Azure 订阅 |
| 地区 | East US |

本文中的示例和屏幕截图使用**美国东部**区域。 选择靠近你的位置, 并确保在同一区域中创建所有资源。

### <a name="resource-group"></a>资源组

使用 Azure 门户创建名为**DetectStoppedDevices**的[资源组](https://portal.azure.com/#create/Microsoft.ResourceGroup), 以包含您创建的其他资源。 在 IoT Central 应用程序所在的同一位置创建 Azure 资源。

### <a name="event-hubs-namespace"></a>Event Hubs 命名空间

使用 Azure 门户创建具有以下设置的[事件中心命名空间](https://portal.azure.com/#create/Microsoft.EventHub):

| 设置 | 值 |
| ------- | ----- |
| 姓名    | 选择命名空间名称 |
| 定价层 | 基本 |
| 订阅 | 你的订阅 |
| 资源组 | DetectStoppedDevices |
| Location | East US |
| 吞吐量单位 | 1 |

### <a name="stream-analytics-job"></a>流分析作业

使用 Azure 门户创建具有以下设置的[流分析作业](https://portal.azure.com/#create/Microsoft.StreamAnalyticsJob):

| 设置 | 值 |
| ------- | ----- |
| 姓名    | 选择作业名称 |
| 订阅 | 你的订阅 |
| 资源组 | DetectStoppedDevices |
| Location | 美国东部 |
| 宿主环境 | 云 |
| 流单元 | 3 |

### <a name="function-app"></a>Function App

使用 Azure 门户创建具有以下设置的[函数应用](https://portal.azure.com/#create/Microsoft.FunctionApp):

| 设置 | 值 |
| ------- | ----- |
| 应用程序名称    | 选择函数应用名称 |
| 订阅 | 你的订阅 |
| 资源组 | DetectStoppedDevices |
| OS | Windows |
| 宿主计划 | 消耗计划 |
| Location | East US |
| 运行时堆栈 | .NET |
| 存储 | 新建 |

### <a name="sendgrid-account"></a>SendGrid 帐户

使用 Azure 门户创建具有以下设置的[SendGrid 帐户](https://portal.azure.com/#create/Sendgrid.sendgrid):

| 设置 | 值 |
| ------- | ----- |
| 姓名    | 选择你的 SendGrid 帐户名称 |
| 密码 | 创建密码 |
| 订阅 | 你的订阅 |
| 资源组 | DetectStoppedDevices |
| 定价层 | F1 免费 |
| 联系信息 | 填写所需信息 |

创建所有必需的资源后, **DetectStoppedDevices**资源组将如以下屏幕截图所示:

![检测已停止的设备资源组](media/howto-create-custom-rules/resource-group.png)

## <a name="create-an-event-hub"></a>创建事件中心

可以将 IoT Central 应用程序配置为将遥测数据持续导出到事件中心。 在本部分中, 将创建一个事件中心来接收来自 IoT Central 应用程序的遥测数据。 事件中心将遥测传递到流分析作业进行处理。

1. 在 Azure 门户中, 导航到事件中心命名空间, 然后选择 " **+ 事件中心**"。
1. 将事件中心命名为**centralexport**, 然后选择 "**创建**"。

事件中心命名空间如以下屏幕截图所示:

![Event Hubs 命名空间](media/howto-create-custom-rules/event-hubs-namespace.png)

## <a name="get-sendgrid-api-key"></a>获取 SendGrid API 密钥

函数应用需要 SendGrid API 密钥才能发送电子邮件。 创建 SendGrid API 密钥:

1. 在 Azure 门户中, 导航到 SendGrid 帐户。 然后选择 "**管理**" 以访问你的 SendGrid 帐户。
1. 在 SendGrid 帐户中选择 "**设置**", 然后依次选择 " **API 密钥**"。 选择 "**创建 API 密钥**":

    ![创建 SendGrid API 密钥](media/howto-create-custom-rules/sendgrid-api-keys.png)

1. 在 "**创建 API 密钥**" 页上, 使用 "**完全访问**权限" 创建名为**AzureFunctionAccess**的密钥。
1. 记下 API 密钥, 在配置函数应用时需要它。

## <a name="define-the-function"></a>定义函数

此解决方案使用 Azure Functions 应用程序在流分析作业检测到停止的设备时发送电子邮件通知。 创建函数应用:

1. 在 Azure 门户中, 导航到**DetectStoppedDevices**资源组中的**应用服务**实例。
1. 选择 **+** 此功能可创建新函数。
1. 在 "**选择开发环境**" 页上, 选择 **"门户**", 然后选择 "**继续**"。
1. 在 "**创建函数**" 页上, 选择 " **Webhook + API** ", 然后选择 "**创建**"。

门户会创建一个名为**HttpTrigger1**的默认函数:

![默认 HTTP 触发器函数](media/howto-create-custom-rules/default-function.png)

### <a name="configure-function-bindings"></a>配置函数绑定

若要通过 SendGrid 发送电子邮件, 需要配置函数的绑定, 如下所示:

1. 选择 "**集成**", 选择 "输出**HTTP ($return)** ", 然后选择 "**删除**"。
1. 选择 " **+ 新建输出**", 然后选择 " **SendGrid**", 然后选择 "**选择**"。 选择 "**安装**" 以安装 SendGrid 扩展。
1. 安装完成后, 选择 "**使用函数返回值**"。 添加有效的**到地址**以接收电子邮件通知。  添加有效**的 "发**件人" 地址, 以用作电子邮件发件人。
1. 选择 " **SENDGRID API 密钥应用设置**" 旁边的 "**新建**"。 输入**SendGridAPIKey**作为密钥, 并将之前记下的 SendGrid API 密钥作为值。 然后选择“创建”。
1. 选择 "**保存**" 以保存函数的 SendGrid 绑定。

集成设置类似于以下屏幕截图:

![Function app 集成](media/howto-create-custom-rules/function-integrate.png)

### <a name="add-the-function-code"></a>添加函数代码

若要实现函数, 请添加C#代码以分析传入的 HTTP 请求并发送电子邮件, 如下所示:

1. 在函数应用中选择**HttpTrigger1**函数, 并将C#代码替换为以下代码:

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

    你可能会看到一条错误消息, 直到你保存新代码。

1. 选择 "**保存**" 以保存函数。

### <a name="test-the-function-works"></a>测试函数的工作

若要在门户中测试函数, 请首先选择代码编辑器底部的 "**日志**"。 然后选择代码编辑器右侧的 "**测试**"。 使用以下 JSON 作为**请求正文**:

```json
[{"deviceid":"test-device-1","time":"2019-05-02T14:23:39.527Z"},{"deviceid":"test-device-2","time":"2019-05-02T14:23:50.717Z"},{"deviceid":"test-device-3","time":"2019-05-02T14:24:28.919Z"}]
```

函数日志消息将显示在 "**日志**" 面板中:

![函数日志输出](media/howto-create-custom-rules/function-app-logs.png)

几分钟后,**收件人**电子邮件地址会收到包含以下内容的电子邮件:

```txt
The following device(s) have stopped sending telemetry:

Device ID   Time
test-device-1   2019-05-02T14:23:39.527Z
test-device-2   2019-05-02T14:23:50.717Z
test-device-3   2019-05-02T14:24:28.919Z
```

## <a name="add-stream-analytics-query"></a>添加流分析查询

此解决方案使用流分析查询来检测设备何时停止发送遥测超过120秒。 查询使用事件中心的遥测作为其输入。 作业会将查询结果发送到 function app。 在本部分中, 你将配置流分析作业:

1. 在 Azure 门户中, 导航到流分析作业, 在 "**作业拓扑**" 下, 选择 "**输入**", 选择 " **+ 添加流输入**", 然后选择 "**事件中心**"。
1. 使用下表中的信息, 使用之前创建的事件中心配置输入, 然后选择 "**保存**":

    | 设置 | ReplTest1 |
    | ------- | ----- |
    | 输入别名 | centraltelemetry |
    | 订阅 | 你的订阅 |
    | 事件中心命名空间 | 事件中心命名空间 |
    | 事件中心名称 | 使用现有- **centralexport** |

1. 在 "**作业拓扑**" 下, 选择 "**输出**", 选择 " **+ 添加**", 然后选择 " **Azure 函数**"。
1. 使用下表中的信息来配置输出, 然后选择 "**保存**":

    | 设置 | ReplTest1 |
    | ------- | ----- |
    | 输出别名 | emailnotification |
    | 订阅 | 你的订阅 |
    | Function App | 函数应用 |
    | 函数  | HttpTrigger1 |

1. 在 "**作业拓扑**" 下, 选择 "**查询**", 并将现有查询替换为以下 SQL:

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

1. 选择**保存**。
1. 若要启动流分析作业, 请依次选择 "**概述**"、"启动"、"**开始**" 和 "**启动**":

    ![流分析](media/howto-create-custom-rules/stream-analytics.png)

## <a name="configure-export-in-iot-central"></a>在 IoT Central 中配置导出

在[Azure IoT Central 应用程序管理器](https://aka.ms/iotcentral)网站上, 导航到从 Contoso 模板创建的 IoT Central 应用程序。 在本部分中, 将配置应用程序, 以便将遥测从其模拟设备流式传输到事件中心。 若要配置导出:

1. 导航到 "**连续数据导出**" 页, 依次选择 " **+ 新建**" 和 " **Azure 事件中心**"。
1. 使用以下设置配置导出, 然后选择 "**保存**":

    | 设置 | ReplTest1 |
    | ------- | ----- |
    | 显示名 | 导出到事件中心 |
    | Enabled | 开 |
    | Event Hubs 命名空间 | 事件中心命名空间名称 |
    | 事件中心 | centralexport |
    | 度量 | 开 |
    | 设备 | 关 |
    | 设备模板 | 关 |

![连续数据导出配置](media/howto-create-custom-rules/cde-configuration.png)

继续之前, 请等待导出状态为 "**正在运行**"。

## <a name="test"></a>测试

若要测试该解决方案, 可以禁用从 IoT Central 到模拟停止设备的连续数据导出:

1. 在 IoT Central 应用程序中, 导航到 "**连续数据导出**" 页, 选择 "**导出到事件中心**导出配置"。
1. 将 "**启用**" 设置为 "**关闭**" 并选择 "**保存**"。
1. 在至少两分钟后,**收件人**电子邮件地址会接收一个或多个类似于以下示例内容的电子邮件:

    ```txt
    The following device(s) have stopped sending telemetry:

    Device ID   Time
    7b169aee-c843-4d41-9f25-7a02671ee659    2019-05-09T14:28:59.954Z
    ```

## <a name="tidy-up"></a>整理

若要在此操作说明和避免不必要的成本后进行整理, 请删除 Azure 门户中的**DetectStoppedDevices**资源组。

可以从应用程序内的 "**管理**" 页中删除 IoT Central 应用程序。

## <a name="next-steps"></a>后续步骤

通过本操作指南，我们已学会了：

* 使用*连续数据导出*从 IoT Central 应用程序流式传输遥测数据。
* 创建一个流分析查询, 该查询可检测设备停止发送数据的时间。
* 使用 Azure Functions 和 SendGrid services 发送电子邮件通知。

现在, 你已了解如何创建自定义规则和通知, 接下来是了解如何[通过自定义分析来扩展 Azure IoT Central](howto-create-custom-analytics.md)。
