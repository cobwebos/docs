---
title: Azure IoT 中心模块标识和模块孪生 (Node.js) 入门 | Microsoft Docs
description: 了解如何使用用于 Node.js 的 IoT SDK 创建模块标识和更新模块孪生。
author: chrissie926
manager: ''
ms.service: iot-hub
services: iot-hub
ms.devlang: node
ms.topic: conceptual
ms.date: 04/26/2018
ms.author: menchi
ms.openlocfilehash: e1fcca7f37f928c488b1111d6d3299cb03c73cc1
ms.sourcegitcommit: 150a40d8ba2beaf9e22b6feff414f8298a8ef868
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/27/2018
ms.locfileid: "37034613"
---
# <a name="get-started-with-iot-hub-module-identity-and-module-twin-using-nodejs-back-end-and-nodejs-device"></a>使用 Node.js 后端和 Node.js 设备创建 IoT 中心模块标识和模块孪生入门

> [!NOTE]
> [模块标识和模块孪生](iot-hub-devguide-module-twins.md)类似于 Azure IoT 中心设备标识和设备孪生，但提供更精细的粒度。 Azure IoT 中心设备标识和设备孪生允许后端应用程序配置设备并提供设备条件的可见性，而模块标识和模块孪生为设备的各个组件提供这些功能。 在支持多个组件的设备上（例如基于操作系统的设备或固件设备），它允许每个部件拥有独立的配置和条件。

在本教程结束时，会创建两个 Node.js 应用：

* **CreateIdentities**，用于创建设备标识、模块标识和相关的安全密钥，以连接设备和模块客户端。
* **UpdateModuleTwinReportedProperties**，用于将更新的模块孪生报告属性发送到 IoT 中心。

> [!NOTE]
> 有关 Azure IoT SDK 的信息（可以使用这些 SDK 构建可在设备和解决方案后端上运行的应用程序），请参阅 [Azure IoT SDK][lnk-hub-sdks]。

要完成本教程，需要以下各项：

* 有效的 Azure 帐户。 （如果没有帐户，只需花费几分钟就能创建一个[免费帐户][lnk-free-trial]。）
* IoT 中心。
* 安装最新的 [Node.js SDK](https://github.com/Azure/azure-iot-sdk-node)。


现已创建 IoT 中心，因此已获得完成本教程的其余部分所需的主机名和 IoT 中心连接字符串。

<a id="DeviceIdentity_csharp"></a>
## <a name="create-a-device-identity-and-a-module-identity-in-iot-hub"></a>在 IoT 中心中创建设备标识和模块标识

本部分将创建一个 Node.js 应用，用于在 IoT 中心的标识注册表中创建设备标识和模块标识。 设备或模块无法连接到 IoT 中心，除非它在标识注册表中具有条目。 有关详细信息，请参阅 [IoT 中心开发人员指南][lnk-devguide-identity]中的“标识注册表”部分。 运行此控制台应用时，它会为设备和模块生成唯一的 ID 和密钥。 设备和模块在向 IoT 中心发送设备到云的消息时，使用这些值来标识自身。 ID 区分大小写。

1.  创建目录以保存代码。
2. 在该目录中，首先运行 npm init -y 使用默认值创建一个空的 package.json。 这是代码的项目文件。
3. 运行 npm install -S azure-iothub@modules-preview 在 node_modules 子目录中安装服务 SDK。 

    > [!NOTE] 
    > 子目录名称 node_modules 使用字模块来表示“节点库”。 此处的术语与 IoT 中心模块无关。

4. 在目录中创建以下 .js 文件。 将它命名为 add.js。 复制并粘贴中心连接字符串和中心名称。

    ```javascript
    var Registry = require('azure-iothub').Registry;
    var uuid = require('uuid');
    // Copy/paste your connection string and hub name here
    var serviceConnectionString = '<hub connection string from portal>';
    var hubName = '<hub name>.azure-devices.net';
    // Create an instance of the IoTHub registry
    var registry = Registry.fromConnectionString(serviceConnectionString);
    // Insert your device ID and moduleId here.
    var deviceId = 'myFirstDevice';
    var moduleId = 'myFirstModule';
    // Create your device as a SAS authentication device
    var primaryKey = new Buffer(uuid.v4()).toString('base64');
    var secondaryKey = new Buffer(uuid.v4()).toString('base64');
    var deviceDescription = {
      deviceId: deviceId,
      status: 'enabled',
      authentication: {
        type: 'sas',
        symmetricKey: {
          primaryKey: primaryKey,
          secondaryKey: secondaryKey
        }
      }
    };

    // First, create a device identity
    registry.create(deviceDescription, function(err) {
      if (err) {
        console.log('Error creating device identity: ' + err);
        process.exit(1);
      }
      console.log('device connection string = "HostName=' + hubName + ';DeviceId=' + deviceId + ';SharedAccessKey=' + primaryKey + '"');

    // Then add a module to that device
      registry.addModule({ deviceId: deviceId, moduleId: moduleId }, function(err) {
        if (err) {
          console.log('Error creating module identity: ' + err);
          process.exit(1);
        }

    // Finally, retrieve the module details from the hub so we can construct the connection string
        registry.getModule(deviceId, moduleId, function(err, foundModule) {
          if (err) {
            console.log('Error getting module back from hub: ' + err);
            process.exit(1);
          }
          console.log('module connection string = "HostName=' + hubName + ';DeviceId=' + foundModule.deviceId + ';ModuleId='+foundModule.moduleId+';SharedAccessKey=' + foundModule.authentication.symmetricKey.primaryKey + '"');
          process.exit(0);
        });
      });
    });

    ```

此应用在设备“myFirstDevice”下创建 ID 为“myFirstDevice”的设备标识，以及 ID 为“myFirstModule”的模块标识。 （如果该模块 ID 已在标识注册表中，代码就只检索现有的模块信息。）然后，应用程序会显示该标识的主密钥。 在模拟模块应用中使用此密钥连接到 IoT 中心。

5. 使用节点 add.js 运行它。 它将为设备标识提供一个连接字符串，并为模块标识提供另一个连接字符串。

    > [!NOTE]
    > IoT 中心标识注册表只存储设备和模块标识，以启用对 IoT 中心的安全访问。 标识注册表存储用作安全凭据的设备 ID 和密钥。 标识注册表还为每个设备存储启用/禁用标志，该标志可以用于禁用对该设备的访问。 如果应用程序需要存储其他特定于设备的元数据，则应使用特定于应用程序的存储。 没有针对模块标识的“已启用/已禁用”标记。 有关详细信息，请参阅 [IoT 中心开发人员指南][lnk-devguide-identity]。


<a id="D2C_csharp"></a>
## <a name="update-the-module-twin-using-nodejs-device-sdk"></a>使用 Node.js 设备 SDK 更新模块孪生

在本节中，将在更新模块孪生报告属性的模拟设备上创建 Node.js 应用。

1. **获取模块连接字符串** -- 现在，如果登录到 [Azure 门户][lnk-portal]。 导航到 IoT 中心并单击 IoT 设备。 查找并打开 myFirstDevice，可以看到 myFirstModule 已成功创建。 复制模块连接字符串。 下一步将需要它。

    ![Azure 门户模块详细信息][15]

2. 与上面步骤中的操作类似，为设备代码创建一个目录，使用 NPM 对它进行初始化并安装设备 SDK (npm install -S azure-iot-device-amqp@modules-preview)。

    > [!NOTE]
    > npm install 命令可能有点慢。 请耐心等待，它正在从包存储库中提取大量代码。

    > [!NOTE] 
    > 如果看到错误 npm ERR! 分析 json 时出现注册表错误，可以安全忽略。 如果看到错误 npm ERR! 分析 json 时出现注册表错误，可以安全忽略。

3. 创建名为 twin.js 的文件。 复制并粘贴模块标识字符串。

    ```javascript
    var Client = require('azure-iot-device').Client;
    var Protocol = require('azure-iot-device-amqp').Amqp;
    // Copy/paste your module connection string here.
    var connectionString = '<insert module connection string here>';
    // Create a client using the Amqp protocol.
    var client = Client.fromConnectionString(connectionString, Protocol);
    client.on('error', function (err) {
      console.error(err.message);
    });
    // connect to the hub
    client.open(function(err) {
      if (err) {
        console.error('error connecting to hub: ' + err);
        process.exit(1);
      }
      console.log('client opened');
    // Create device Twin
      client.getTwin(function(err, twin) {
        if (err) {
          console.error('error getting twin: ' + err);
          process.exit(1);
        }
    // Output the current properties
        console.log('twin contents:');
        console.log(twin.properties);
    // Add a handler for desired property changes
        twin.on('properties.desired', function(delta) {
            console.log('new desired properties received:');
            console.log(JSON.stringify(delta));
        });
    // create a patch to send to the hub
        var patch = {
          updateTime: new Date().toString(),
          firmwareVersion:'1.2.1',
          weather:{
            temperature: 72,
            humidity: 17
          }
        };
    // send the patch
        twin.properties.reported.update(patch, function(err) {
          if (err) throw err;
          console.log('twin state reported');
        });
      });
    });
    ```

2. 现在请使用命令 node twin.js 来运行它。

    ```
    F:\temp\module_twin>node twin.js
    client opened
    twin contents:
    { reported: { update: [Function: update], '$version': 1 },
      desired: { '$version': 1 } }
    new desired properties received:
    {"$version":1}
    twin state reported
    ```

## <a name="next-steps"></a>后续步骤

若要继续了解 IoT 中心入门知识并浏览其他 IoT 方案，请参阅：

* [设备管理入门][lnk-device-management]
* [IoT 边缘入门][lnk-iot-edge]


<!-- Images. -->
[15]: ./media\iot-hub-csharp-csharp-module-twin-getstarted/module-detail.JPG
<!-- Links -->
[lnk-hub-sdks]: iot-hub-devguide-sdks.md
[lnk-free-trial]: http://azure.microsoft.com/pricing/free-trial/
[lnk-portal]: https://portal.azure.com/

[lnk-device-management]: iot-hub-node-node-device-management-get-started.md
[lnk-iot-edge]: ../iot-edge/tutorial-simulate-device-linux.md
[lnk-devguide-identity]: iot-hub-devguide-identity-registry.md
[lnk-nuget-service-sdk]: https://www.nuget.org/packages/Microsoft.Azure.Devices/