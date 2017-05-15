---
title: "通过 Azure IoT 中心进行设备固件更新 (Node) | Microsoft Docs"
description: "如何使用 Azure IoT 中心上的设备管理进行设备固件更新。 使用 Azure IoT SDK for Node.js 实现模拟设备应用以及触发固件更新的服务应用。"
services: iot-hub
documentationcenter: .net
author: juanjperez
manager: timlt
editor: 
ms.assetid: 70b84258-bc9f-43b1-b7cf-de1bb715f2cf
ms.service: iot-hub
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/06/2017
ms.author: juanpere
ms.translationtype: Human Translation
ms.sourcegitcommit: 4ba60cee8848079935111ed3de480081a4aa58f6
ms.openlocfilehash: 30a707ec15d592c8a10905e13a75ea2f6e52cccc
ms.contentlocale: zh-cn
ms.lasthandoff: 02/06/2017


---
# <a name="use-device-management-to-initiate-a-device-firmware-update-nodenode"></a>使用设备管理启动设备固件更新 (Node/Node)
[!INCLUDE [iot-hub-selector-firmware-update](../../includes/iot-hub-selector-firmware-update.md)]

## <a name="introduction"></a>介绍
在[设备管理入门][lnk-dm-getstarted]教程中，你已了解了如何使用[设备克隆][lnk-devtwin]和[直接方法][lnk-c2dmethod]基元来远程重新启动设备。 本教程使用相同的 IoT 中心基元，提供指南，并演示如何进行端到端模拟固件更新。  此模式在用于 Intel Edison 设备示例的固件更新实现中使用。

本教程演示如何：

* 创建一个 Node.js 控制台应用，该应用通过 IoT 中心在模拟设备应用上调用 firmwareUpdate 直接方法。
* 创建实现 **firmwareUpdate** 直接方法的模拟设备应用。 此方法会启动等待下载固件映像、下载固件映像以及最后应用固件映像的多阶段过程。 在更新的每个阶段中，设备使用报告属性来报告进度。

在本教程结束时，将会创建两个 Node.js 控制台应用：

**dmpatterns_fwupdate_service.js**，它调用模拟设备应用中的直接方法、显示响应并定期（每隔 500 毫秒）显示更新的报告属性。

**dmpatterns_fwupdate_device.js**，它使用早前创建的设备标识连接到 IoT 中心、接收 firmwareUpdate 直接方法、运行一个多状态过程以模拟固件更新，包括：等待映像下载、下载新映像以及最后应用映像。

若要完成本教程，需要以下各项：

* Node.js 版本 0.12.x 或更高版本， <br/>  [准备开发环境][lnk-dev-setup]介绍了如何在 Windows 或 Linux 上安装本教程所用的 Node.js。
* 有效的 Azure 帐户。 （如果没有帐户，只需花费几分钟就能创建一个[免费帐户][lnk-free-trial]。）

按照[设备管理入门](iot-hub-node-node-device-management-get-started.md)一文创建 IoT 中心，并获取 IoT 中心连接字符串。

[!INCLUDE [iot-hub-get-started-create-hub](../../includes/iot-hub-get-started-create-hub.md)]

[!INCLUDE [iot-hub-get-started-create-device-identity](../../includes/iot-hub-get-started-create-device-identity.md)]

## <a name="trigger-a-remote-firmware-update-on-the-device-using-a-direct-method"></a>使用直接方法在设备上触发远程固件更新
在本部分中，你将创建一个 Node.js 控制台应用以启动设备上的远程固件更新。 该应用使用直接方法来启动更新，并使用设备孪生查询定期获取活动固件更新的状态。

1. 创建一个名为 **triggerfwupdateondevice** 的空文件夹。  在 **triggerfwupdateondevice** 文件夹的命令提示符处，使用以下命令创建 package.json 文件。  接受所有默认值：
   
    ```
    npm init
    ```
2. 在 **triggerfwupdateondevice** 文件夹的命令提示符处，运行下述命令以安装 **azure-iot-hub** 和 **azure-iot-device-mqtt** 设备 SDK 包：
   
    ```
    npm install azure-iothub --save
    ```
3. 在 **triggerfwupdateondevice** 文件夹中，使用文本编辑器创建 **dmpatterns_getstarted_service.js** 文件。
4. 在 **dmpatterns_getstarted_service.js** 文件开头添加以下“require”语句：
   
    ```
    'use strict';
   
    var Registry = require('azure-iothub').Registry;
    var Client = require('azure-iothub').Client;
    ```
5. 添加以下变量声明并替换占位符值：
   
    ```
    var connectionString = '{device_connectionstring}';
    var registry = Registry.fromConnectionString(connectionString);
    var client = Client.fromConnectionString(connectionString);
    var deviceToUpdate = 'myDeviceId';
    ```
6. 添加以下函数以查找并显示 firmwareUpdate 报告属性的值。
   
    ```
    var queryTwinFWUpdateReported = function() {
        registry.getTwin(deviceToUpdate, function(err, twin){
            if (err) {
              console.error('Could not query twins: ' + err.constructor.name + ': ' + err.message);
            } else {
              console.log((JSON.stringify(twin.properties.reported.iothubDM.firmwareUpdate)) + "\n");
            }
        });
    };
    ```
7. 添加以下函数以调用 firmwareUpdate 方法来重新启动目标设备：
   
    ```
    var startFirmwareUpdateDevice = function() {
      var params = {
          fwPackageUri: 'https://secureurl'
      };
   
      var methodName = "firmwareUpdate";
      var payloadData =  JSON.stringify(params);
   
      var methodParams = {
        methodName: methodName,
        payload: payloadData,
        timeoutInSeconds: 30
      };
   
      client.invokeDeviceMethod(deviceToUpdate, methodParams, function(err, result) {
        if (err) {
          console.error('Could not start the firmware update on the device: ' + err.message)
        } 
      });
    };
    ```
8. 最后，向代码中添加以下函数以启动固件更新序列并开始定期显示报告的属性：
   
    ```
    startFirmwareUpdateDevice();
    setInterval(queryTwinFWUpdateReported, 500);
    ```
9. 保存并关闭 **dmpatterns_fwupdate_service.js** 文件。

[!INCLUDE [iot-hub-device-firmware-update](../../includes/iot-hub-device-firmware-update.md)]

## <a name="run-the-apps"></a>运行应用
现在可以运行应用了。

1. 在 **manageddevice** 文件夹的命令提示符处，运行以下命令以开始侦听重新启动直接方法。
   
    ```
    node dmpatterns_fwupdate_device.js
    ```
2. 在 **triggerfwupdateondevice** 文件夹的命令提示符处，运行以下命令以触发远程重新启动并查询设备孪生以查找上次重新启动时间。
   
    ```
    node dmpatterns_fwupdate_service.js
    ```
3. 可在控制台查看对直接方法的设备响应。

## <a name="next-steps"></a>后续步骤
在本教程中，已使用直接方法在设备上触发远程固件更新，并使用报告属性跟踪固件更新的进度。

若要了解如何扩展 IoT 解决方案以及在多个设备上计划方法调用，请参阅[计划和广播作业][lnk-tutorial-jobs]教程。

[lnk-devtwin]: iot-hub-devguide-device-twins.md
[lnk-c2dmethod]: iot-hub-devguide-direct-methods.md
[lnk-dm-getstarted]: iot-hub-node-node-device-management-get-started.md
[lnk-tutorial-jobs]: iot-hub-node-node-schedule-jobs.md

[lnk-dev-setup]: https://github.com/Azure/azure-iot-sdk-node/tree/master/doc/node-devbox-setup.md
[lnk-free-trial]: http://azure.microsoft.com/pricing/free-trial/
[lnk-transient-faults]: https://msdn.microsoft.com/library/hh680901(v=pandp.50).aspx

