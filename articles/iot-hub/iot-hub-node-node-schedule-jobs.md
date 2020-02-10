---
title: 使用 Azure IoT 中心安排作业 (Node) | Microsoft Docs
description: 如何安排 Azure IoT 中心作业对多台设备调用直接方法。 使用 Azure IoT SDK for Node.js 实现模拟设备应用以及用于运行作业的服务应用。
author: wesmc7777
manager: philmea
ms.author: wesmc
ms.service: iot-hub
services: iot-hub
ms.devlang: nodejs
ms.topic: conceptual
ms.date: 08/16/2019
ms.openlocfilehash: 5053935f52153f0cd6ff2f05c5153732f5bda945
ms.sourcegitcommit: 9add86fb5cc19edf0b8cd2f42aeea5772511810c
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/09/2020
ms.locfileid: "77110849"
---
# <a name="schedule-and-broadcast-jobs-nodejs"></a>计划和广播作业（node.js）

[!INCLUDE [iot-hub-selector-schedule-jobs](../../includes/iot-hub-selector-schedule-jobs.md)]

Azure IoT 中心是一项完全托管的服务，允许后端应用创建和跟踪用于计划和更新数百万台设备的作业。  作业可以用于以下操作：

* 更新所需属性
* 更新标记
* 调用直接方法

从概念上讲，一个作业包装上述一项作业，并跟踪一组设备中的执行进度（由设备孪生查询定义）。  例如，后端应用可使用作业重启 10,000 台设备（由设备孪生查询指定并计划在将来执行）。 该应用程序随后可以在其中每个设备接收和执行重新启动方法时跟踪进度。

可在以下文章中了解有关所有这些功能的详细信息：

* 设备孪生和属性：[设备孪生入门](iot-hub-node-node-twin-getstarted.md)和[教程：如何使用设备孪生属性](tutorial-device-twins.md)

* 直接方法： [IoT 中心开发人员指南-直接方法](iot-hub-devguide-direct-methods.md)和[教程：直接方法](quickstart-control-device-node.md)

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-whole.md)]

本教程演示如何：

* 创建一个具有直接方法的 Node.js 模拟设备应用，启用可由解决方案后端进行调用的 lockDoor。

* 创建一个 Node.js 控制台应用，该应用使用作业调用模拟设备应用中的 **lockDoor** 直接方法，并使用设备作业更新所需属性。

在本教程结束时，会创建两个 Node.js 应用：

* **simDevice.js**，它使用设备标识连接到 IoT 中心，并接收 **lockDoor** 直接方法。

* scheduleJobService.js，它调用模拟设备应用中的直接方法，并通过作业更新设备孪生的所需属性。

## <a name="prerequisites"></a>先决条件

* Node.js 版本 10.0. x 或更高版本。 [准备开发环境](https://github.com/Azure/azure-iot-sdk-node/tree/master/doc/node-devbox-setup.md)介绍如何在 Windows 或 Linux 上安装本教程所用的 node.js。

* 有效的 Azure 帐户。 （如果没有帐户，只需几分钟即可创建一个[免费帐户](https://azure.microsoft.com/pricing/free-trial/)。）

* 请确保已在防火墙中打开端口8883。 本文中的设备示例使用了 MQTT 协议，该协议通过端口8883进行通信。 此端口可能在某些企业和教育网络环境中被阻止。 有关此问题的详细信息和解决方法，请参阅[连接到 IoT 中心（MQTT）](iot-hub-mqtt-support.md#connecting-to-iot-hub)。

## <a name="create-an-iot-hub"></a>创建 IoT 中心

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

## <a name="register-a-new-device-in-the-iot-hub"></a>在 IoT 中心内注册新设备

[!INCLUDE [iot-hub-get-started-create-device-identity](../../includes/iot-hub-get-started-create-device-identity.md)]

## <a name="create-a-simulated-device-app"></a>创建模拟设备应用程序

本部分将创建一个 Node.js 控制台应用，用于响应通过云调用的方法，这会触发模拟 lockDoor 方法。

1. 新建名为 **simDevice** 的空文件夹。  在 **simDevice** 文件夹的命令提示符处，使用以下命令创建 package.json 文件。  接受所有默认值：

   ```console
   npm init
   ```

2. 在 **simDevice** 文件夹的命令提示符处，运行下述命令以安装 **azure-iot-device** 设备 SDK 包和 **azure-iot-device-mqtt** 包：

   ```console
   npm install azure-iot-device azure-iot-device-mqtt --save
   ```

3. 在 **simDevice.js** 文件夹中，利用文本编辑器创建新的 **simDevice** 文件。

4. 在 **simDevice.js** 文件的开头添加以下“require”语句：

    ```javascript
    'use strict';

    var Client = require('azure-iot-device').Client;
    var Protocol = require('azure-iot-device-mqtt').Mqtt;
    ```

5. 添加 **connectionString** 变量，并使用它创建一个**客户端**实例。 将 `{yourDeviceConnectionString}` 占位符值替换为先前复制的设备连接字符串。

    ```javascript
    var connectionString = '{yourDeviceConnectionString}';
    var client = Client.fromConnectionString(connectionString, Protocol);
    ```

6. 添加以下函数以处理 **lockDoor** 方法。

    ```javascript
    var onLockDoor = function(request, response) {

        // Respond the cloud app for the direct method
        response.send(200, function(err) {
            if (err) {
                console.error('An error occurred when sending a method response:\n' + err.toString());
            } else {
                console.log('Response to method \'' + request.methodName + '\' sent successfully.');
            }
        });

        console.log('Locking Door!');
    };
    ```

7. 添加以下代码以注册 **lockDoor** 方法的处理程序。

   ```javascript
   client.open(function(err) {
        if (err) {
            console.error('Could not connect to IotHub client.');
        }  else {
            console.log('Client connected to IoT Hub. Register handler for lockDoor direct method.');
            client.onDeviceMethod('lockDoor', onLockDoor);
        }
   });
   ```

8. 保存并关闭 **simDevice.js** 文件。

> [!NOTE]
> 为简单起见，本教程不实现任何重试策略。 在生产代码中，应该按文章 [Transient Fault Handling](/azure/architecture/best-practices/transient-faults)（暂时性故障处理）中所述实施重试策略（例如指数性的回退）。
>

## <a name="get-the-iot-hub-connection-string"></a>获取 IoT 中心连接字符串

[!INCLUDE [iot-hub-howto-schedule-jobs-shared-access-policy-text](../../includes/iot-hub-howto-schedule-jobs-shared-access-policy-text.md)]

[!INCLUDE [iot-hub-include-find-registryrw-connection-string](../../includes/iot-hub-include-find-registryrw-connection-string.md)]

## <a name="schedule-jobs-for-calling-a-direct-method-and-updating-a-device-twins-properties"></a>安排作业，用于调用直接方法和更新设备孪生的属性

在本部分中，将创建一个 Node.js 控制台应用，它使用直接方法在设备上启动远程 **lockDoor** 并更新设备孪生的属性。

1. 新建名为 **scheduleJobService** 的空文件夹。  在 **scheduleJobService** 文件夹的命令提示符处，使用以下命令创建 package.json 文件。  接受所有默认值：

    ```console
    npm init
    ```

2. 在 **scheduleJobService** 文件夹的命令提示符处，运行下述命令以安装 **azure-iothub** 设备 SDK 包和 **azure-iot-device-mqtt** 包：

    ```console
    npm install azure-iothub uuid --save
    ```

3. 在 **scheduleJobService** 文件夹中，利用文本编辑器创建新的 **scheduleJobService.js** 文件。

4. 在**scheduleJobService**文件的开头添加以下 "需要" 语句：

    ```javascript
    'use strict';

    var uuid = require('uuid');
    var JobClient = require('azure-iothub').JobClient;
    ```

5. 添加以下变量声明。 将 `{iothubconnectionstring}` 占位符值替换为在["获取 IoT 中心连接字符串](#get-the-iot-hub-connection-string)" 中复制的值。 如果注册的设备不同于**myDeviceId**，请确保在查询条件中进行更改。

    ```javascript
    var connectionString = '{iothubconnectionstring}';
    var queryCondition = "deviceId IN ['myDeviceId']";
    var startTime = new Date();
    var maxExecutionTimeInSeconds =  300;
    var jobClient = JobClient.fromConnectionString(connectionString);
    ```

6. 添加以下用于监视作业执行的函数：

    ```javascript
    function monitorJob (jobId, callback) {
        var jobMonitorInterval = setInterval(function() {
            jobClient.getJob(jobId, function(err, result) {
            if (err) {
                console.error('Could not get job status: ' + err.message);
            } else {
                console.log('Job: ' + jobId + ' - status: ' + result.status);
                if (result.status === 'completed' || result.status === 'failed' || result.status === 'cancelled') {
                clearInterval(jobMonitorInterval);
                callback(null, result);
                }
            }
            });
        }, 5000);
    }
    ```

7. 添加以下代码以安排调用设备方法的作业：
  
    ```javascript
    var methodParams = {
        methodName: 'lockDoor',
        payload: null,
        responseTimeoutInSeconds: 15 // Timeout after 15 seconds if device is unable to process method
    };

    var methodJobId = uuid.v4();
    console.log('scheduling Device Method job with id: ' + methodJobId);
    jobClient.scheduleDeviceMethod(methodJobId,
                                queryCondition,
                                methodParams,
                                startTime,
                                maxExecutionTimeInSeconds,
                                function(err) {
        if (err) {
            console.error('Could not schedule device method job: ' + err.message);
        } else {
            monitorJob(methodJobId, function(err, result) {
                if (err) {
                    console.error('Could not monitor device method job: ' + err.message);
                } else {
                    console.log(JSON.stringify(result, null, 2));
                }
            });
        }
    });
    ```

8. 添加以下代码，安排设备孪生的更新作业：

    ```javascript
    var twinPatch = {
       etag: '*',
       properties: {
           desired: {
               building: '43',
               floor: 3
           }
       }
    };

    var twinJobId = uuid.v4();

    console.log('scheduling Twin Update job with id: ' + twinJobId);
    jobClient.scheduleTwinUpdate(twinJobId,
                                queryCondition,
                                twinPatch,
                                startTime,
                                maxExecutionTimeInSeconds,
                                function(err) {
        if (err) {
            console.error('Could not schedule twin update job: ' + err.message);
        } else {
            monitorJob(twinJobId, function(err, result) {
                if (err) {
                    console.error('Could not monitor twin update job: ' + err.message);
                } else {
                    console.log(JSON.stringify(result, null, 2));
                }
            });
        }
    });
    ```

9. 保存并关闭 **scheduleJobService.js** 文件。

## <a name="run-the-applications"></a>运行应用程序

现在，已准备就绪，可以运行应用程序了。

1. 在 **simDevice** 文件夹的命令提示符处，运行以下命令以开始侦听重新启动直接方法。

    ```console
    node simDevice.js
    ```

2. 在 **scheduleJobService** 文件夹的命令提示符处运行以下命令，以便触发作业进行锁门和孪生项的更新

    ```console
    node scheduleJobService.js
    ```

3. 可以在控制台中看到直接方法和作业状态的设备响应。

   下面显示了对直接方法的设备响应：

   ![模拟设备应用输出](./media/iot-hub-node-node-schedule-jobs/sim-device.png)

   下面显示了直接方法和设备克隆更新的服务计划作业，以及运行到完成的作业：

   ![运行模拟设备应用](./media/iot-hub-node-node-schedule-jobs/schedule-job-service.png)

## <a name="next-steps"></a>后续步骤

在本教程中，使用了作业来安排用于设备的直接方法以及设备孪生属性的更新。

若要继续了解 IoT 中心和设备管理模式（如远程通过无线固件更新）的入门，请参阅[教程：如何进行固件更新](tutorial-firmware-update.md)。

若要继续了解 IoT 中心入门，请参阅[Azure IoT Edge](../iot-edge/tutorial-simulate-device-linux.md)入门。
